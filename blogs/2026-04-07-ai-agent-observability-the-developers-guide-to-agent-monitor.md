---
title: "AI agent observability: The developer's guide to agent monitoring"
url: "https://blog.sentry.io/ai-agent-observability-developers-guide-to-agent-monitoring/"
date: "Tue, 07 Apr 2026 00:00:00 GMT"
author: ""
feed_url: "https://blog.sentry.io/feed.xml"
---
<p>Most discussions about agent observability read like outdated compliance checklists with &quot;AI&quot; substituted for older technologies. They emphasize comprehensive logging, evaluation metrics, and governance frameworks—but provide no actual code examples or guidance for real debugging scenarios.</p>
<p>Effective agent monitoring requires two essential components: dashboards showing aggregate behavior across all agents, and detailed traces explaining specific failures. Most platforms provide only one. Here's what having both looks like in practice.</p>
<h2>What is Agent Observability?</h2>
<p>Agent observability provides complete visibility into AI agent operations: model invocations, tool selections, decision sequences, handoffs, token consumption, and associated costs.</p>
<p>Traditional application monitoring focuses on requests, errors, and response times. This works adequately for stateless HTTP services where requests are independent.</p>
<p>AI agents operate fundamentally differently. A single agent execution might involve multiple model calls, tool invocations, sub-agent transfers, and reasoning loops—all interdependent. When outputs are incorrect, failure points could be anywhere: incorrect tool responses, context window limitations, wrong function selection, or lost state during handoffs.</p>
<p>Agent observability provides comprehensive visibility into the complete decision-making process across these interconnected operations. Agent quality assessment, workflow debugging, and cost control all require this visibility level.</p>
<h3>Why Traditional Monitoring Fails for AI Agents</h3>
<p>Standard APM tools report that <code>POST /api/chat</code> returned status 200 in 4.2 seconds. They won't reveal that internally, the agent executed 5 model calls, with the third call selecting an incorrect tool that returned outdated information, which the model then accurately summarized as garbage.</p>
<p>An &quot;log everything later&quot; approach produces dashboards showing counts and averages without enabling deeper investigation. An agent producing incorrect output might have completed 12 model calls, executed 4 tools, transferred to a sub-agent, then generated incorrect output. Aggregate metrics indicate error rate increases. They don't indicate where reasoning failed.</p>
<p>The solution requires <strong>structured tracing</strong> based on consistent standards, allowing dashboards, traces, and alerts to communicate uniformly.</p>
<h2>The OpenTelemetry Standard for Agent Observability</h2>
<p>The <a href="https://opentelemetry.io/docs/specs/semconv/gen-ai/">OpenTelemetry <code>gen_ai</code> semantic conventions</a> establish standardized instrumentation for agent systems. Instead of custom logging, every AI operation produces a structured span containing consistent attributes. Core operations include:</p>
<table>
<thead>
<tr>
<th>Span Operation</th>
<th>Captured Information</th>
</tr>
</thead>
<tbody><tr>
<td><code>gen_ai.request</code></td>
<td>Single model call: model name, prompt, response, token counts</td>
</tr>
<tr>
<td><code>gen_ai.invoke_agent</code></td>
<td>Complete agent lifecycle from task initiation to final output</td>
</tr>
<tr>
<td><code>gen_ai.execute_tool</code></td>
<td>Tool/function invocation: name, input, output, duration</td>
</tr>
</tbody></table>
<p>These compose hierarchically:</p>
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
<p>This is an open standard, not proprietary. Any platform following it can ingest these spans. The span operation follows the pattern <code>gen_ai.{operation_name}</code>. For manual instrumentation, <code>gen_ai.request</code> covers all model calls. SDK auto-instrumentation may generate more specific operations like <code>gen_ai.chat</code> or <code>gen_ai.embeddings</code> depending on API calls. Because these are structured spans rather than unstructured logs, they enable both dashboards and trace visualization.</p>
<h2>Key Metrics for AI Agent Monitoring</h2>
<p>Before selecting tools, track these measurements for production agents:</p>
<p><strong>Reliability metrics:</strong></p>
<ul>
<li><strong>Agent error rate</strong> — percentage of agent executions that fail or produce errors</li>
<li><strong>Tool failure rate</strong> — identifies unreliable tools and their impact on agent success</li>
<li><strong>Latency (p50, p95)</strong> — per-agent and per-model tracking to identify regressions</li>
</ul>
<p><strong>Cost metrics:</strong></p>
<ul>
<li><strong>Token usage</strong> — input, output, cached, and reasoning tokens per model. Cached and reasoning tokens are subsets, not cumulative. Incorrect calculation means fictional cost dashboards.</li>
<li><strong>Cost per model</strong> — compare similar workloads. Example: <code>claude-sonnet-4-6</code> costs $10.8K weekly while <code>gemini-2.5-flash-lite</code> handles equivalent volume for $645.</li>
<li><strong>Cost per user/tier</strong> — identifies which users or pricing levels consume most AI resources</li>
</ul>
<p><strong>Quality metrics:</strong></p>
<ul>
<li><strong>Tool call frequency</strong> — tracks how often agents invoke each tool and invocation sequence</li>
<li><strong>Token efficiency</strong> — average tokens per successful completion. Growing numbers suggest inflating prompts or context windows.</li>
<li><strong>Cache hit rate</strong> — percentage of input tokens served from cache. If caching is enabled but this metric isn't improving, something needs investigation.</li>
</ul>
<p>Comprehensive platforms following OpenTelemetry conventions surface these metrics automatically from trace data.</p>
<h2>Auto-instrumentation for 10+ Frameworks</h2>
<p>Sentry auto-instruments major AI frameworks in <a href="https://docs.sentry.io/platforms/python/tracing/instrumentation/custom-instrumentation/ai-agents-module/">Python</a> and <a href="https://docs.sentry.io/platforms/javascript/guides/node/ai-agent-monitoring/">Node.js</a>, including OpenAI, Anthropic, Google GenAI, LangChain, LangGraph, Pydantic AI, OpenAI Agents SDK, Vercel AI SDK, and others. Manual span creation isn't needed. Installation, tracing enablement, and automatic pickup occur.</p>
<p>Complete setup:</p>
<pre><code class="language-python">

sentry_sdk.init(
    dsn=&quot;YOUR_DSN&quot;,
    traces_sample_rate=1.0,
)
# OpenAI, Anthropic, LangChain, LangGraph, Pydantic AI,
# Google GenAI -- all auto-instrumented when detected.
</code></pre>
<p>That's the entire configuration. Making Anthropic or OpenAI calls produces visible spans.</p>
<h2>Pre-built Agent Monitoring Dashboards</h2>
<p>Most observability platforms include pre-built agent monitoring dashboards. Once instrumentation is active, Sentry's <a href="https://docs.sentry.io/ai/monitoring/agents/dashboard/">AI Agents dashboard</a> provides three views:</p>
<h3>AI Agents Overview</h3>
<p><img alt="AI Agents Overview Dashboard" src="../../assets/images/posts/ai-agent-observability-developers-guide-to-agent-monitoring/overview.png" /></p>
<p>Displays agent runs, duration, total model calls, tokens consumed, and tool invocations. This is the &quot;is everything functioning?&quot; view.</p>
<h3>AI Agents Model Details</h3>
<p><img alt="AI Agents Model Details Dashboard" src="../../assets/images/posts/ai-agent-observability-developers-guide-to-agent-monitoring/agent-models-dash.png" /></p>
<p>Per-model cost projections, token breakdown (input/output/cached/reasoning), and latency. This automatically displays cost metrics.</p>
<h3>AI Agents Tool Details</h3>
<p><img alt="AI Agents Tool Details Dashboard" src="../../assets/images/posts/ai-agent-observability-developers-guide-to-agent-monitoring/agent-tools-dash.png" /></p>
<p>Per-tool invocation frequency, error rates, and p95 latency. A tool failing 12% of the time appears here before users report problems.</p>
<p>These dashboards appear immediately once spans flow. However, they display aggregates: per-model totals, per-tool error rates, overall agent counts. They answer technical questions and highlight problems—but what about business-level inquiries?</p>
<h2>Custom Agent Monitoring Dashboards</h2>
<p>Pre-built dashboards show aggregate health signals. They don't show who drives AI costs, which features justify spending, or whether caching strategies save money. Addressing these questions requires slicing trace data by custom dimensions: user tier, feature flag, experiment group.</p>
<p>Some platforms enable custom queries against span data. With the <a href="https://cli.sentry.dev/">Sentry CLI</a>, you can script this—and its <a href="https://cli.sentry.dev/agentic-usage/">agent skill system</a> allows AI coding assistants like Claude Code to build dashboards:</p>
<p><strong>&quot;Who are my most expensive users?&quot;</strong></p>
<pre><code class="language-bash">sentry dashboard create 'AI Cost Attribution'

sentry dashboard widget add 'AI Cost Attribution' &quot;Most Expensive Users&quot; \
  --display table --dataset spans \
  --query &quot;sum:gen_ai.usage.total_tokens&quot; \
  --where &quot;span.op:gen_ai.request&quot; \
  --group-by &quot;user.id&quot; \
  --sort &quot;-sum:gen_ai.usage.total_tokens&quot; \
  --limit 20
</code></pre>
<p><strong>&quot;Which pricing tier is eating my AI budget?&quot;</strong></p>
<p>Tag users with their plan, then group in the dashboard:</p>
<pre><code class="language-python">sentry_sdk.set_tag(&quot;user_tier&quot;, user.plan)  # &quot;free&quot;, &quot;pro&quot;, &quot;enterprise&quot;
</code></pre>
<pre><code class="language-bash">sentry dashboard widget add 'AI Cost Attribution' &quot;AI Cost by Tier&quot; \
  --display bar --dataset spans \
  --query &quot;sum:gen_ai.usage.total_tokens&quot; \
  --where &quot;span.op:gen_ai.request&quot; \
  --group-by &quot;user_tier&quot; \
  --sort &quot;-sum:gen_ai.usage.total_tokens&quot;
</code></pre>
<p>This reveals that free-tier users consume 60% of AI budget. The same tagging pattern works for any dimension: <code>team</code>, <code>feature_flag</code>, <code>experiment_group</code>.</p>
<p><strong>&quot;Which agents are token-hungry?&quot;</strong></p>
<pre><code class="language-bash">sentry dashboard widget add 'AI Cost Attribution' &quot;Avg Tokens per Agent&quot; \
  --display table --dataset spans \
  --query &quot;avg:gen_ai.usage.total_tokens&quot; &quot;count&quot; \
  --where &quot;span.op:gen_ai.invoke_agent&quot; \
  --group-by &quot;gen_ai.agent.name&quot; \
  --sort &quot;-avg:gen_ai.usage.total_tokens&quot;
</code></pre>
<p>If &quot;Research Agent&quot; averages 15K tokens per run while &quot;Summarizer Agent&quot; averages 2K, you know where to focus prompt optimization.</p>
<p><strong>&quot;Is my prompt caching actually saving money?&quot;</strong></p>
<pre><code class="language-bash">sentry dashboard widget add 'AI Cost Attribution' &quot;Cache Hit Rate&quot; \
  --display line --dataset spans \
  --query &quot;sum:gen_ai.usage.input_tokens.cached&quot; &quot;sum:gen_ai.usage.input_tokens&quot; \
  --where &quot;span.op:gen_ai.request&quot;
</code></pre>
<p>If cached-to-total ratio isn't improving after enabling caching, your prompt structure needs investigation.</p>
<h2>Why Tracing Matters for Agent Monitoring</h2>
<p>Dashboards show totals. Traces show decisions.</p>
<p>A dashboard indicates error rates increased or latency spiked. A trace identifies which agent, which model call, and which tool caused it.</p>
<p>Distributed tracing already captures complete span hierarchies for requests: browser interactions, HTTP calls, server routing, database queries. Agent observability integrates into this. Your <code>gen_ai.*</code> spans appear as children within existing traces, so model calls, tool executions, MCP server interactions, and sub-agent transfers sit alongside regular application spans. No separate system required.</p>
<p>This integration is powerful. You're examining agent data within full request context, from user click to final tool response, with agent decisions as one layer in the entire stack.</p>
<p>Here's what this looks like in Sentry's trace view:</p>
<p><img alt="Distributed trace showing full agent workflow" src="../../assets/images/posts/ai-agent-observability-developers-guide-to-agent-monitoring/agent-detailed-trace-view.png" /></p>
<p>Single request, end-to-end: from user clicking &quot;Send Message&quot; through API, agent orchestration with model calls and MCP server interactions, through handoff to second agent. Clicking any span reveals model, tokens, cost, and system prompt details.</p>
<h2>Agent Observability Best Practices</h2>
<p>Whatever platform you choose, implement these practices:</p>
<ol>
<li><p><strong>Use structured tracing, not logs.</strong> Unstructured logs can't reconstruct reasoning chains. OpenTelemetry <code>gen_ai</code> spans provide searchable, filterable hierarchies powering dashboards and trace views simultaneously.</p>
</li>
<li><p><strong>Sample AI traces at 100%.</strong> Agent runs are span hierarchies. Sampling drops complete executions, not individual calls. If <code>tracesSampleRate</code> is below 1.0, you're losing entire agent runs. Use <code>tracesSampler</code> to keep AI routes at 100% while sampling everything else at baseline. (<a href="https://blog.sentry.io/sample-ai-traces-at-100-percent-without-sampling-everything/">Detailed sampling guide</a>)</p>
</li>
<li><p><strong>Track cost by user, not just by model.</strong> The pre-built dashboard shows per-model totals. You need per-user and per-tier attribution for business decisions about rate limiting, pricing, and model routing.</p>
</li>
<li><p><strong>Monitor tool reliability separately.</strong> A tool failing 5% of the time might not appear in overall error rates, but causes 1 in 20 agent runs to produce bad output. Your dashboard should surface per-tool error rates distinctly.</p>
</li>
<li><p><strong>Connect AI monitoring to your full stack.</strong> Agent failure might stem from slow database queries, failed external API calls, or frontend timeouts. Isolated AI monitoring can't reveal these root causes.</p>
</li>
</ol>
<h2>Full-Stack Agent Observability</h2>
<p>Agent observability becomes most powerful when layered on top of comprehensive APM platforms, linking agent spans to errors, performance traces, session replays, and logs across your entire system.</p>
<p>Isolated AI monitoring shows <code>gen_ai</code> spans separately. You see that Research Agent completed 8 model calls costing $0.04. What remains invisible is why it made 8 instead of 3: your <code>search_docs</code> tool executes a slow Postgres query timing out, causing the agent to retry with rephrased queries repeatedly.</p>
<p>When agent spans share context with your broader infrastructure, everything clarifies. Errors include their complete span hierarchy. Session replays show user interactions triggering bad agent runs. Upstream issues (sluggish vector databases, unreliable external APIs) appear in the same trace as resulting agent behavior.</p>
<h3>Four Steps to First Trace</h3>
<ol>
<li>Install the SDK: <code>pip install sentry-sdk</code> or <code>npm install @sentry/node</code></li>
<li>Initialize with tracing enabled</li>
<li>Make an AI call; spans and dashboards populate automatically</li>
<li>(Optional) Install the CLI skill for your AI assistant:</li>
</ol>
<pre><code class="language-bash">npx skills add https://cli.sentry.dev
</code></pre>
<p>If your framework is auto-instrumented, you're complete. If not, <a href="https://docs.sentry.io/platforms/python/tracing/instrumentation/custom-instrumentation/ai-agents-module/">manual instrumentation</a> requires approximately 10 lines per span type.</p>
<p>For comprehensive guidance on capturing 100% of AI traces, see our companion post on <a href="https://blog.sentry.io/sample-ai-traces-at-100-percent-without-sampling-everything/">sampling strategies for agentic applications</a>.</p>
<p><a href="https://sentry.io/signup/">Try Sentry at no cost</a> - AI monitoring is included across all plans.</p>
<h2>AI Agent Monitoring FAQs</h2>
<p><strong>What is agent observability?</strong></p>
<p>Agent observability is complete visibility into AI agent operations: model calls, tool selections, decision chains, handoffs, token consumption, and costs. It transcends traditional monitoring by tracking complete reasoning sequences across multi-turn interactions.</p>
<p><strong>How is agent monitoring different from LLM monitoring?</strong></p>
<p>LLM monitoring measures individual model calls (latency, tokens, errors). Agent monitoring tracks complete agent cycles: multi-step reasoning, tool execution, agent-to-agent transfers, and how individual calls combine into workflows.</p>
<p><strong>What metrics should I track for AI agents?</strong></p>
<p>Minimum metrics: agent error rate, tool failure rate, latency (p50/p95), token usage per model, cost per user/tier, and cache hit rate. These divide into reliability (functioning properly?), cost (expenditure?), and quality (improving?) categories.</p>
<p><strong>What tools support agent observability?</strong></p>
<p>OpenTelemetry <code>gen_ai</code> semantic conventions represent the emerging standard. Sentry, LangSmith, Langfuse, Arize, and Datadog all provide agent observability with distinct approaches. Sentry distinguishes itself through full-stack context: agent data connected to errors, performance traces, session replays, and logs unified in one system.</p>
