---
title: "Sample AI traces at 100% without sampling everything"
url: "https://blog.sentry.io/sample-ai-traces-at-100-percent-without-sampling-everything/"
date: "Thu, 09 Apr 2026 00:00:00 GMT"
author: ""
feed_url: "https://blog.sentry.io/feed.xml"
---
<p>A little while ago, when agents were telling me &quot;You're absolutely right!&quot;, I was building <a href="https://webvitals.com">webvitals.com</a>. You put in a URL, it kicks off an API request to a Next.js API route that invokes an agent with a few tools to scan it and provide AI generated suggestions to improve your… you guessed it… Web Vitals. Do we even care about these anymore?</p>
<p>I had the <code>traceSampleRate</code> set to 100% in development, but in production, I sampled it down to 10% because… well that's what our instrumentation recommends. Kyle wrote a great blog post explaining that &quot;<a href="https://blog.sentry.io/sampling-strategy-sentry/">Watching everything is watching nothing</a>&quot;. But AI is non-deterministic. And when I was debugging an error from a tool call, I realized I was missing very important spans emitted from the Vercel AI SDK because of that sampling strategy.</p>
<p>An agent run with 7 tool calls doesn't get partially sampled. You either capture the whole span tree or you lose it entirely. This is how head-based sampling works.</p>
<p>I was chasing ghosts.</p>
<h2>Agent Runs Are Span Trees, and Sampling Is All-or-Nothing</h2>
<p>A typical agent execution looks like this in Sentry's trace view:</p>
<pre><code>POST /api/chat (http.server)
└── gen_ai.invoke_agent &quot;Research Agent&quot;
    ├── gen_ai.request &quot;chat claude-sonnet-4-6&quot;        ← initial reasoning
    ├── gen_ai.execute_tool &quot;search_docs&quot;              ← tool call
    ├── gen_ai.request &quot;chat claude-sonnet-4-6&quot;        ← process results
    ├── gen_ai.execute_tool &quot;summarize&quot;                ← second tool call
    ├── gen_ai.request &quot;chat claude-sonnet-4-6&quot;        ← decides to hand off
    └── gen_ai.execute_tool &quot;transfer_to_writer&quot;       ← handoff via tool
        └── gen_ai.invoke_agent &quot;Writer Agent&quot;
            ├── gen_ai.request &quot;chat gemini-2.5-flash&quot;
            └── gen_ai.execute_tool &quot;format_output&quot;
</code></pre>
<p>That's 11 spans in a single run. The sampling decision happens once, at the root: the <code>POST /api/chat</code> HTTP transaction. Every child span inherits that decision. If the root is dropped, all 9 spans disappear.</p>
<p>This is fundamentally different from sampling HTTP requests, where dropping one <code>GET /api/users</code> is no big deal because the next one is basically identical.</p>
<p>Agent runs are not identical. Each one makes different decisions, calls different tools, processes different data. An agent that hallucinated on run 67 might work perfectly on run 420. If your sample rate dropped 67, you'll never know what went wrong.</p>
<h2>How Head-Based Sampling Actually Works (and Why It Matters Here)</h2>
<p>Both the Sentry JavaScript and Python SDKs use head-based sampling: the decision is made at the start of the trace, before any child spans exist.</p>
<p>In the JavaScript SDK, <a href="https://github.com/getsentry/sentry-javascript/blob/develop/packages/opentelemetry/src/sampler.ts#L79"><code>SentrySampler.shouldSample()</code></a> is explicit about this:</p>
<pre><code class="language-javascript">// We only sample based on parameters (like tracesSampleRate or tracesSampler)
// for root spans. Non-root spans simply inherit the sampling decision
// from their parent.
</code></pre>
<p>Non-root spans don't get a vote. If the root span was dropped, <code>tracesSampler</code> is never called for any child, including your <code>gen_ai.request</code> and <code>gen_ai.execute_tool</code> spans. They inherit the parent's fate.</p>
<p>In Python, the same logic lives in <a href="https://github.com/getsentry/sentry-python/blob/master/sentry_sdk/tracing.py#L1150"><code>Transaction._set_initial_sampling_decision()</code></a>. The <code>traces_sampler</code> callback receives a <code>sampling_context</code> dict with <code>transaction_context</code> (containing <code>op</code> and <code>name</code>) and <code>parent_sampled</code>. It only fires for root transactions.</p>
<p>This means head-based sampling doesn't support <strong>independently sampling gen_ai child spans at a different rate than their parent transaction.</strong> There's no &quot;sample 100% of LLM calls but 10% of HTTP requests.&quot; If the HTTP request is dropped, the LLM calls inside it are dropped too.</p>
<p>I'd love to walk through a few different scenarios to show the difference in filtering approaches based on wether or not the root span is from an agent or the application.</p>
<h2>Scenario 1: The <code>gen_ai</code> Span IS the Root</h2>
<p>Sometimes your agent run <em>is</em> the root span. Maybe it's a cron job thats running an agent, a queue consumer processing an AI task, or a CLI script. In these cases, <code>tracesSampler</code> sees the <code>gen_ai.*</code> operation directly and you can match on it:</p>
<p><strong>JavaScript:</strong></p>
<pre><code class="language-javascript">Sentry.init({
  dsn: process.env.SENTRY_DSN,
  tracesSampler: ({ name, attributes, inheritOrSampleWith }) =&gt; {
    // Standalone gen_ai root spans - always sample
    if (attributes?.['sentry.op']?.startsWith('gen_ai.') || attributes?.['gen_ai.system']) {
      return 1.0;
    }

    return inheritOrSampleWith(0.2);
  },
});
</code></pre>
<p><strong>Python:</strong></p>
<pre><code class="language-python">def traces_sampler(sampling_context):
    op = sampling_context.get(&quot;transaction_context&quot;, {}).get(&quot;op&quot;, &quot;&quot;)

    # Standalone gen_ai root spans - always sample
    if op.startswith(&quot;gen_ai.&quot;):
        return 1.0

    parent = sampling_context.get(&quot;parent_sampled&quot;)
    if parent is not None:
        return float(parent)

    return 0.2

sentry_sdk.init(dsn=&quot;...&quot;, traces_sampler=traces_sampler)
</code></pre>
<p>This is the easy case. The hard case is next.</p>
<h2>Scenario 2: The <code>gen_ai</code> Spans Are Children of an HTTP Transaction</h2>
<p>This is the common case in web applications. A user hits <code>POST /api/chat</code>, your framework creates an <code>http.server</code> root span, and somewhere inside that request handler your agent runs. By the time the first <code>gen_ai.request</code> span is created, the sampling decision was already made for the HTTP transaction.</p>
<p>The fix: <strong>identify which routes trigger AI calls and sample those routes at 100%.</strong></p>
<p><strong>JavaScript:</strong></p>
<pre><code class="language-javascript">Sentry.init({
  dsn: process.env.SENTRY_DSN,
  tracesSampler: ({ name, attributes, inheritOrSampleWith }) =&gt; {
    // Standalone gen_ai root spans
    if (attributes?.['sentry.op']?.startsWith('gen_ai.') || attributes?.['gen_ai.system']) {
      return 1.0;
    }

    // HTTP routes that serve AI features - always sample
    if (name?.includes('/api/chat') ||
        name?.includes('/api/agent') ||
        name?.includes('/api/generate')) {
      return 1.0;
    }

    return inheritOrSampleWith(0.2);
  },
});
</code></pre>
<p><strong>Python:</strong></p>
<pre><code class="language-python">def traces_sampler(sampling_context):
    tx_context = sampling_context.get(&quot;transaction_context&quot;, {})
    op = tx_context.get(&quot;op&quot;, &quot;&quot;)
    name = tx_context.get(&quot;name&quot;, &quot;&quot;)

    # Standalone gen_ai root spans
    if op.startswith(&quot;gen_ai.&quot;):
        return 1.0

    # HTTP routes that serve AI features - always sample
    if op == &quot;http.server&quot; and any(
        p in name for p in [&quot;/api/chat&quot;, &quot;/api/agent&quot;, &quot;/api/generate&quot;]
    ):
        return 1.0

    # Honour parent decision in distributed traces
    parent = sampling_context.get(&quot;parent_sampled&quot;)
    if parent is not None:
        return float(parent)

    return 0.2

sentry_sdk.init(dsn=&quot;...&quot;, traces_sampler=traces_sampler)
</code></pre>
<p>Replace the route strings with whatever paths your AI features live on. If your entire app is AI-powered, skip the <code>tracesSampler</code> and just set <code>tracesSampleRate: 1.0</code>.</p>
<h2>The Cost Math: AI API Bills Dwarf Observability Costs</h2>
<p>The instinct to sample AI traces at a lower rate usually comes from cost concerns. Let's look at the actual numbers.</p>
<table>
<thead>
<tr>
<th>What</th>
<th>Cost per event</th>
</tr>
</thead>
<tbody><tr>
<td>Claude Sonnet 4 input (1K tokens)</td>
<td>~$0.003</td>
</tr>
<tr>
<td>Claude Sonnet 4 output (1K tokens)</td>
<td>~$0.015</td>
</tr>
<tr>
<td>Gemini 2.5 Flash input (1K tokens)</td>
<td>~$0.00015</td>
</tr>
<tr>
<td>Gemini 2.5 Flash output (1K tokens)</td>
<td>~$0.0006</td>
</tr>
<tr>
<td>A typical agent run (3 LLM calls, 2 tool calls)</td>
<td>$0.02-$0.15</td>
</tr>
<tr>
<td><strong>Sentry span events for that agent run (~9 spans)</strong></td>
<td><strong>Fraction of a cent</strong></td>
</tr>
</tbody></table>
<p>The LLM calls themselves are 10-100x more expensive than the monitoring. You're already paying for the AI call; dropping the observability span to save a fraction of a cent per call is like skipping the dashcam to save on gas.</p>
<h2>When 100% Tracing Isn't Feasible: Metrics and Logs as a Safety Net</h2>
<p>If you genuinely can't sample AI routes at 100%, because of, say, massive scale or strict budget restraints, you can still capture the important signals from every AI call using Sentry <a href="https://docs.sentry.io/platforms/python/metrics/">Metrics</a> and <a href="https://docs.sentry.io/platforms/javascript/guides/node/logs/">Logs</a>. Both are independent of trace sampling.</p>
<p><strong>JavaScript - emit metrics on every LLM call:</strong></p>
<pre><code class="language-javascript">

// After every LLM call, regardless of trace sampling:
Sentry.metrics.distribution(&quot;gen_ai.token_usage&quot;, result.usage.totalTokens, {
  unit: &quot;none&quot;,
  attributes: {
    model: &quot;claude-sonnet-4-6&quot;,
    user_id: user.id,
    endpoint: &quot;/api/chat&quot;,
  },
});

Sentry.metrics.distribution(&quot;gen_ai.latency&quot;, responseTimeMs, {
  unit: &quot;millisecond&quot;,
  attributes: { model: &quot;claude-sonnet-4-6&quot; },
});

Sentry.metrics.count(&quot;gen_ai.calls&quot;, 1, {
  attributes: {
    model: &quot;claude-sonnet-4-6&quot;,
    status: result.error ? &quot;error&quot; : &quot;success&quot;,
  },
});
</code></pre>
<p><strong>Python - emit metrics on every LLM call:</strong></p>
<pre><code class="language-python">

sentry_sdk.metrics.distribution(
    &quot;gen_ai.token_usage&quot;,
    result.usage.total_tokens,
    attributes={
        &quot;model&quot;: &quot;claude-sonnet-4-6&quot;,
        &quot;user_id&quot;: str(user.id),
        &quot;endpoint&quot;: &quot;/api/chat&quot;,
    },
)

sentry_sdk.metrics.distribution(
    &quot;gen_ai.latency&quot;,
    response_time_ms,
    unit=&quot;millisecond&quot;,
    attributes={&quot;model&quot;: &quot;claude-sonnet-4-6&quot;},
)

sentry_sdk.metrics.count(
    &quot;gen_ai.calls&quot;,
    1,
    attributes={
        &quot;model&quot;: &quot;claude-sonnet-4-6&quot;,
        &quot;status&quot;: &quot;error&quot; if error else &quot;success&quot;,
    },
)
</code></pre>
<p>You can also log every call with structured attributes for searchability:</p>
<p><strong>JavaScript:</strong></p>
<pre><code class="language-javascript">Sentry.logger.info(&quot;LLM call completed&quot;, {
  model: &quot;claude-sonnet-4-6&quot;,
  user_id: user.id,
  input_tokens: result.usage.promptTokens,
  output_tokens: result.usage.completionTokens,
  latency_ms: responseTimeMs,
  status: &quot;success&quot;,
});
</code></pre>
<p><strong>Python:</strong></p>
<pre><code class="language-python">sentry_sdk.logger.info(
    &quot;LLM call completed&quot;,
    model=&quot;claude-sonnet-4-6&quot;,
    user_id=str(user.id),
    input_tokens=result.usage.prompt_tokens,
    output_tokens=result.usage.completion_tokens,
    latency_ms=response_time_ms,
    status=&quot;success&quot;,
)
</code></pre>
<p>Here's what each telemetry layer gives you:</p>
<table>
<thead>
<tr>
<th>Signal</th>
<th>Traces (sampled)</th>
<th>Metrics (100%)</th>
<th>Logs (100%)</th>
</tr>
</thead>
<tbody><tr>
<td>Full span tree with prompts/responses</td>
<td>Yes</td>
<td>No</td>
<td>No</td>
</tr>
<tr>
<td>Token usage distributions (p50, p99)</td>
<td>Partial</td>
<td>Yes</td>
<td>No</td>
</tr>
<tr>
<td>Cost attribution by model/user</td>
<td>Partial</td>
<td>Yes</td>
<td>Yes</td>
</tr>
<tr>
<td>Error rates by model/endpoint</td>
<td>Partial</td>
<td>Yes</td>
<td>Yes</td>
</tr>
<tr>
<td>Latency distributions</td>
<td>Partial</td>
<td>Yes</td>
<td>No</td>
</tr>
<tr>
<td>Searchable per-call records</td>
<td>Yes</td>
<td>No</td>
<td>Yes</td>
</tr>
</tbody></table>
<p><strong>The recommended approach:</strong> Use <code>tracesSampler</code> to capture 100% of AI-related routes. If that's not possible, combine a lower trace rate with metrics and logs emitted on every call. Traces give you the debugging depth; metrics and logs give you the aggregate picture.</p>
<p>Once you're emitting these metrics, you can build custom dashboards that go beyond what the <a href="https://docs.sentry.io/ai/monitoring/agents/dashboards/">pre-built AI Agents dashboard</a> shows. The <a href="https://cli.sentry.dev/">Sentry CLI</a> makes this scriptable:</p>
<pre><code class="language-bash"># Find your most expensive users - the pre-built dashboard doesn't group by user
sentry dashboard create 'AI Cost Attribution'
sentry dashboard widget add 'AI Cost Attribution' &quot;Most Expensive Users&quot; \
  --display table --dataset spans \
  --query &quot;sum:gen_ai.usage.total_tokens&quot; \
  --where &quot;span.op:gen_ai.request&quot; \
  --group-by &quot;user.id&quot; \
  --sort &quot;-sum:gen_ai.usage.total_tokens&quot; \
  --limit 20

# Cost per conversation - find runaway multi-turn sessions
sentry dashboard widget add 'AI Cost Attribution' &quot;Cost per Conversation&quot; \
  --display table --dataset spans \
  --query &quot;sum:gen_ai.usage.total_tokens&quot; &quot;count&quot; \
  --where &quot;span.op:gen_ai.request&quot; \
  --group-by &quot;gen_ai.conversation.id&quot; \
  --sort &quot;-sum:gen_ai.usage.total_tokens&quot; \
  --limit 20
</code></pre>
<p>The pre-built dashboard gives you per-model and per-tool aggregates. Custom dashboards answer the business questions: <em>who's driving cost, which features justify their AI spend, and which conversations are spiraling.</em></p>
<h2>The Full Production Config</h2>
<p>Here's a complete setup that samples AI routes at 100%, everything else at your baseline, and emits metrics as a safety net:</p>
<p><strong>JavaScript:</strong></p>
<pre><code class="language-javascript">

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  tracesSampler: ({ name, attributes, inheritOrSampleWith }) =&gt; {
    if (attributes?.['sentry.op']?.startsWith('gen_ai.') || attributes?.['gen_ai.system']) {
      return 1.0;
    }
    if (name?.includes('/api/chat') || name?.includes('/api/agent')) {
      return 1.0;
    }
    return inheritOrSampleWith(0.2);
  },
});

// Wrapper for any LLM call - emit metrics regardless of sampling
function trackLLMCall(model, usage, latencyMs, userId) {
  Sentry.metrics.distribution(&quot;gen_ai.token_usage&quot;, usage.totalTokens, {
    attributes: { model, user_id: userId },
  });
  Sentry.metrics.distribution(&quot;gen_ai.latency&quot;, latencyMs, {
    unit: &quot;millisecond&quot;,
    attributes: { model },
  });
  Sentry.metrics.count(&quot;gen_ai.calls&quot;, 1, {
    attributes: { model, status: &quot;success&quot; },
  });
}
</code></pre>
<p><strong>Python:</strong></p>
<pre><code class="language-python">

def traces_sampler(sampling_context):
    tx = sampling_context.get(&quot;transaction_context&quot;, {})
    op, name = tx.get(&quot;op&quot;, &quot;&quot;), tx.get(&quot;name&quot;, &quot;&quot;)

    if op.startswith(&quot;gen_ai.&quot;):
        return 1.0
    if op == &quot;http.server&quot; and any(
        p in name for p in [&quot;/api/chat&quot;, &quot;/api/agent&quot;]
    ):
        return 1.0

    parent = sampling_context.get(&quot;parent_sampled&quot;)
    if parent is not None:
        return float(parent)
    return 0.2

sentry_sdk.init(
    dsn=&quot;...&quot;,
    traces_sampler=traces_sampler,
)

# Wrapper for any LLM call - emit metrics regardless of sampling
def track_llm_call(model, usage, latency_ms, user_id):
    sentry_sdk.metrics.distribution(
        &quot;gen_ai.token_usage&quot;, usage.total_tokens,
        attributes={&quot;model&quot;: model, &quot;user_id&quot;: str(user_id)},
    )
    sentry_sdk.metrics.distribution(
        &quot;gen_ai.latency&quot;, latency_ms,
        unit=&quot;millisecond&quot;,
        attributes={&quot;model&quot;: model},
    )
    sentry_sdk.metrics.count(
        &quot;gen_ai.calls&quot;, 1,
        attributes={&quot;model&quot;: model, &quot;status&quot;: &quot;success&quot;},
    )
</code></pre>
<h2>Quick Reference</h2>
<table>
<thead>
<tr>
<th>Situation</th>
<th>What to do</th>
</tr>
</thead>
<tbody><tr>
<td>AI is the core product</td>
<td><code>tracesSampleRate: 1.0</code> - sample everything</td>
</tr>
<tr>
<td>AI is one feature in a larger app</td>
<td><code>tracesSampler</code> with AI routes at 1.0, baseline for the rest</td>
</tr>
<tr>
<td>Can't afford 100% on AI routes</td>
<td>Lower trace rate + metrics/logs on every call</td>
</tr>
<tr>
<td>Already using <code>tracesSampler</code></td>
<td>Add AI route matching to your existing logic</td>
</tr>
<tr>
<td>Sample rate is already 1.0</td>
<td>No change needed</td>
</tr>
</tbody></table>
<p>The underlying principle: agent runs are high-value, low-volume (relative to HTTP traffic), and expensive to reproduce. Sample them accordingly.</p>
<p>If you're just getting started with AI monitoring, check out our companion post on <a href="https://blog.sentry.io/ai-agent-observability-developers-guide-to-agent-monitoring/">the developer's guide to AI agent monitoring</a>, which covers the full setup across 10+ frameworks, the pre-built dashboards, and a real debugging walkthrough.</p>
<p><em>For framework-specific setup, see our</em> <a href="https://docs.sentry.io/ai/monitoring/agents/"><em>AI monitoring docs</em></a><em>. If you're using an AI coding assistant, install the</em> <a href="https://cli.sentry.dev/agentic-usage/"><em>Sentry CLI skill</em></a> <em>(<code>npx skills add &lt;https://cli.sentry.dev&gt;</code>) to configure your sampling, build custom dashboards, and investigate issues directly from your editor.</em></p>
