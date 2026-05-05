---
title: "Debugging multi-agent AI: When the failure is in the space between agents"
url: "https://blog.sentry.io/debugging-multi-agent-ai-when-the-failure-is-in-the-space-between-agents/"
date: "Thu, 16 Apr 2026 00:00:00 GMT"
author: ""
feed_url: "https://blog.sentry.io/feed.xml"
---
<p>I've been building a multi-agent research system. The idea is simple: give it a controversial technical topic like &quot;Should we rewrite our Python backend in Rust?&quot;, and three agents work on it. An Advocate argues for it, a Skeptic argues against, and a Synthesizer reads both briefs blind and produces a balanced analysis. Each agent has its own model, its own tools, its own system prompt.</p>
<p>It worked great in testing. Then I noticed the Synthesizer kept producing analyses that leaned heavily toward one side. Not wrong, but noticeably lopsided. I mean, rewriting the Sentry monorepo in Rust is arguably a bad idea, but it was arguing against on things where I clearly knew it should be for it.</p>
<p>I eventually traced it to the Skeptic's <code>web_search</code> tool. The Advocate was returning 3-4 solid data points per query. The Skeptic, however, was searching for different terms that didn't match the data as well, and was getting back a single generic result. So the Advocate's brief was well-sourced with citations, and the Skeptic's brief was... vibes. The Synthesizer did what any reasonable reader would do: it weighted the better-sourced argument more heavily.</p>
<p>The bug was in a tool call, inside one agent, that silently degraded the input to a completely different agent two steps later. I only found it by clicking through the trace and reading tool outputs at each step.</p>
<h2>What is multi-agent observability?</h2>
<p>Multi-agent observability is visibility into how multiple AI agents coordinate, hand off work, and influence each other's decisions.</p>
<p>You probably already know single-agent observability: one reasoning chain, some tool calls, a response. The multi-agent version tracks a <em>graph</em> of interconnected reasoning chains where the output of one agent becomes the input of another. A failure anywhere in the graph can silently corrupt everything downstream.</p>
<p>If you're running a single agent with a few tools, <a href="https://blog.sentry.io/ai-agent-observability-developers-guide-to-agent-monitoring/">standard agent observability</a> has you covered. But the moment you have agents calling other agents, delegating subtasks, or running in parallel with results merged later, you need a different level of visibility.</p>
<h2>Why single-agent monitoring doesn't cut it here</h2>
<p>Your existing agent monitoring tells you that <code>Skeptic</code> ran in 3.1 seconds and consumed 2,400 tokens. It does not tell you that <code>Skeptic</code>'s <code>web_search</code> returned weak results, that the brief it produced was thin compared to the <code>Advocate</code>'s, and that the <code>Synthesizer</code> produced a biased analysis because one of its inputs was poor.</p>
<p>There are three specific reasons this falls apart.</p>
<p><strong>Blame is distributed.</strong> When the final output is wrong, you can't point at one agent. The Advocate built a reasonable argument from what its tools gave it. The Synthesizer did a reasonable synthesis of what it received. The bug is in the interaction between them, and no single agent's logs will show it.</p>
<p><strong>The worst failures look fine.</strong> In traditional software, things throw errors. In multi-agent AI, an agent returns a plausible-but-thin result, the next agent incorporates it without question, and by the time the final output arrives, weak data has been confidently summarized through multiple layers. You'd never know unless you compared the raw inputs.</p>
<p><strong>You can't test every path.</strong> A single agent with 5 tools has 5 possible actions per step. Three agents with 5 tools each, running in parallel and merging results? The number of possible execution paths is absurd. You need to observe what actually happens in production because you can't pre-test every combination.</p>
<h2>Most &quot;multi-agent&quot; examples are actually single-agent</h2>
<p>Before going further, I want to be honest. I built a multi-agent startup idea validator as my first attempt at this playground, and then realized... it was fake multi-agent. A &quot;Market Analyst&quot; handing off to a &quot;Technical Advisor&quot; handing off to a &quot;Devil's Advocate&quot; is just one agent with different tools. A single agent with all the tools and a comprehensive system prompt produces the same output with less latency and less cost.</p>
<p><a href="https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ai-agents/single-agent-multiple-agents">Microsoft's Cloud Adoption Framework</a> puts it directly: &quot;Don't assume role separation requires multiple agents. Distinct roles might suggest multiple agents, but they don't automatically justify a multi-agent architecture.&quot;</p>
<p>Multi-agent earns its pain when:</p>
<ul>
<li><strong>Objectives genuinely conflict.</strong> An agent told to &quot;argue for&quot; and &quot;argue against&quot; in the same prompt produces mediocre output at both. A generator and a critic need to be separate, or the critic pulls its punches.</li>
<li><strong>Information must be isolated.</strong> If Agent A seeing Agent B's work would bias the result, they can't share a context window. Advocate/skeptic. Blind peer review.</li>
<li><strong>Different models serve different roles.</strong> Cheap fast model for research, expensive capable model for synthesis. One agent means one model.</li>
<li><strong>Tasks should run in parallel.</strong> Two independent research tasks running concurrently as separate agents is genuinely faster than one agent doing them sequentially.</li>
<li><strong>Security boundaries require separation.</strong> The agent reading user PII shouldn't have database write access.</li>
</ul>
<p>If your use case doesn't hit at least two of these, start with a single agent and save yourself the debugging pain I'm about to describe.</p>
<h2>Common multi-agent architecture patterns</h2>
<p>Each pattern produces a different trace shape and breaks in its own way.</p>
<h3>Orchestrator / Worker</h3>
<p>One agent routes tasks to specialists. This is the most common pattern in the OpenAI Agents SDK, LangGraph, and custom implementations.</p>
<pre><code class="language-text">POST /api/research (http.server)
└── gen_ai.invoke_agent &quot;Research Director&quot;
    ├── gen_ai.request &quot;chat gpt-5.4&quot;                         ← plan subtasks
    ├── gen_ai.execute_tool &quot;delegate_research&quot;
    │   └── gen_ai.invoke_agent &quot;Web Research Agent&quot;
    │       ├── gen_ai.request &quot;chat gpt-5.4-mini&quot;
    │       ├── gen_ai.execute_tool &quot;web_search&quot;
    │       └── gen_ai.request &quot;chat gpt-5.4-mini&quot;            ← summarize
    ├── gen_ai.execute_tool &quot;delegate_analysis&quot;
    │   └── gen_ai.invoke_agent &quot;Data Analysis Agent&quot;
    │       ├── gen_ai.request &quot;chat gpt-5.4-mini&quot;
    │       ├── gen_ai.execute_tool &quot;query_database&quot;
    │       └── gen_ai.request &quot;chat gpt-5.4-mini&quot;
    └── gen_ai.request &quot;chat gpt-5.4&quot;                         ← synthesize
</code></pre>
<p><strong>How it breaks:</strong> The orchestrator misclassifies the task and routes to the wrong specialist, who then does perfect work on the wrong problem. Or it passes insufficient context, and the specialist hallucinates what's missing.</p>
<h3>Parallel with merge</h3>
<p>Independent agents work concurrently on the same problem, and a final agent merges results. This is what the balanced research system uses, and it's the pattern I think has the most interesting debugging challenges.</p>
<pre><code class="language-text">Advocate workflow .............. 3.2s  (parallel)
├── gen_ai.invoke_agent &quot;Advocate&quot;
│   ├── gen_ai.request &quot;chat gpt-5.4-mini&quot;         ← plan research
│   ├── gen_ai.execute_tool &quot;web_search&quot;           ← find evidence
│   ├── gen_ai.execute_tool &quot;fetch_benchmark&quot;      ← get numbers
│   └── gen_ai.request &quot;chat gpt-5.4-mini&quot;         ← write brief

Skeptic workflow ............... 2.8s  (parallel)
├── gen_ai.invoke_agent &quot;Skeptic&quot;
│   ├── gen_ai.request &quot;chat gpt-5.4-mini&quot;         ← plan research
│   ├── gen_ai.execute_tool &quot;web_search&quot;           ← find counter-evidence
│   └── gen_ai.request &quot;chat gpt-5.4-mini&quot;         ← write brief

Synthesizer workflow ........... 4.1s  (sequential, after both)
└── gen_ai.invoke_agent &quot;Synthesizer&quot;
    └── gen_ai.request &quot;chat gpt-5.4&quot;              ← blind analysis
</code></pre>
<p><strong>How it breaks:</strong> Uneven tool quality. If one agent's tool calls return richer data, the merge agent naturally weights that side more heavily. The merge agent has no way to know its inputs were unequal, because it only sees the finished briefs, not the raw tool results underneath. This is the bug I had the pleasure of dealing with while crafting this blog post.</p>
<h3>Peer handoffs</h3>
<p>Agents transfer control directly to each other. The OpenAI Agents SDK <code>handoff()</code> pattern works this way.</p>
<pre><code class="language-text">POST /api/chat (http.server)
└── gen_ai.invoke_agent &quot;Triage Agent&quot;
    ├── gen_ai.request &quot;chat gpt-5.4-mini&quot;
    ├── gen_ai.handoff &quot;from Triage Agent to Billing Agent&quot;
    └── gen_ai.invoke_agent &quot;Billing Agent&quot;
        ├── gen_ai.request &quot;chat gpt-5.4-mini&quot;
        ├── gen_ai.execute_tool &quot;check_balance&quot;
        ├── gen_ai.handoff &quot;from Billing Agent to Dispute Specialist&quot;
        └── gen_ai.invoke_agent &quot;Dispute Specialist&quot;
            ├── gen_ai.request &quot;chat gpt-5.4&quot;
            └── gen_ai.execute_tool &quot;file_dispute&quot;
</code></pre>
<p><strong>How it breaks:</strong> State management at the handoff. When Agent A transfers to Agent B, what gets passed? Full conversation history? A summary? Just the last message? Pass everything and you blow context windows. Summarize and you lose nuance. Bugs in the handoff protocol are the hardest to find because they look like bugs in the receiving agent.</p>
<h2>What makes multi-agent debugging different</h2>
<p>There are a few specific problems you only hit when multiple agents are involved.</p>
<ul>
<li><p><strong>Blame attribution across boundaries.</strong> When a multi-agent system returns wrong output, the question is: did the right agent receive the task? Did it get the right context? Did it do bad work with good input, or good work with bad input? Without traces that span the full agent graph, you're reading each agent's logs in isolation trying to reconstruct what happened at the boundaries.</p>
</li>
<li><p><strong>Silent cascading failures.</strong> This is the one that got me. An agent returns a plausible response, the downstream agent accepts it, and the final output is wrong, but every span shows <code>status: ok</code>. To catch these, you need to be able to compare input and output at each agent boundary and see the full prompt and response at each LLM call. Token counts and latency alone won't help.</p>
</li>
<li><p><strong>Context drift across handoffs.</strong> Every time an agent summarizes before passing to the next, information is lossy-compressed. After three handoffs, the original user intent can be barely recognizable. In a trace, you can see this by reading the prompts in sequence: the first agent has the full query, the second has a summary, the third has a summary of a summary. The fix is usually architectural (pass structured data instead of natural language), but you have to see the drift before you can fix it.</p>
</li>
<li><p><strong>Cost explosion without attribution.</strong> In our research system, the Synthesizer uses <code>gpt-5.4</code> while the researchers use <code>gpt-5.4-mini</code>. Without per-agent cost tracking, you'd see total spend growing but wouldn't know the Synthesizer accounts for 60% of the cost despite running only once per query.</p>
</li>
</ul>
<h2>A debugging walkthrough with the balanced research system</h2>
<p>Here's how I actually found the bug from the opening. The Synthesizer was producing lopsided analyses, and I wanted to figure out why.</p>
<h3>Comparing the parallel agents</h3>
<p>First thing I did was look at both research agent workflows side by side in the trace view:</p>
<pre><code class="language-text">Advocate workflow .......................... 3.2s  ✓
├── gen_ai.invoke_agent &quot;Advocate&quot; ......... 3.1s
│   ├── gen_ai.request &quot;chat gpt-5.4-mini&quot; . 0.6s  ← plan
│   ├── gen_ai.execute_tool &quot;web_search&quot; ... 0.2s  ← &quot;rust performance&quot;
│   ├── gen_ai.execute_tool &quot;web_search&quot; ... 0.1s  ← &quot;rust adoption&quot;
│   ├── gen_ai.execute_tool &quot;fetch_benchmark&quot; 0.1s ← rust benchmarks
│   └── gen_ai.request &quot;chat gpt-5.4-mini&quot; . 1.8s  ← write brief

Skeptic workflow ........................... 2.8s  ✓
├── gen_ai.invoke_agent &quot;Skeptic&quot; .......... 2.7s
│   ├── gen_ai.request &quot;chat gpt-5.4-mini&quot; . 0.5s  ← plan
│   ├── gen_ai.execute_tool &quot;web_search&quot; ... 0.1s  ← &quot;python migration costs&quot;
│   └── gen_ai.request &quot;chat gpt-5.4-mini&quot; . 1.9s  ← write brief
</code></pre>
<p>The asymmetry was immediately obvious. The Advocate made 3 tool calls. The Skeptic made 1.</p>
<h3>Inspecting the tool results</h3>
<p>Clicking into the Advocate's <code>web_search</code> spans, each returned 3-4 data points:</p>
<pre><code class="language-js">[&quot;Rust programs typically run 2-5x faster than equivalent Python...&quot;,
 &quot;Discord switched from Go to Rust... latency drop from 50ms to 1ms&quot;,
 &quot;Figma rewrote their multiplayer server... memory usage by 10x&quot;]
</code></pre>
<p>The Skeptic's single <code>web_search</code> had searched for &quot;python migration costs&quot;:</p>
<pre><code class="language-js">[&quot;No specific data found for 'python migration costs'. Consider refining your search terms.&quot;]
</code></pre>
<p>So the Skeptic wrote its brief from general knowledge with no citations, while the Advocate had 10+ data points from 3 searches.</p>
<h3>Following it to the Synthesizer</h3>
<p>Clicking the Synthesizer's <code>gen_ai.request</code> span and reading the prompt confirmed it. It received one well-sourced brief with citations and benchmark data, and one brief with general arguments and no data. It weighted the better-sourced one more heavily, which is exactly what you'd want a synthesizer to do. The problem was upstream.</p>
<h3>The fix</h3>
<p>Two options: improve the Skeptic's prompt to try multiple search queries when the first returns weak results, or improve the <code>web_search</code> tool to handle broader query terms. I did both. Watched the traces afterward, and both agents were producing comparably sourced briefs.</p>
<p>The root cause was a weak tool result for one agent that cascaded through the pipeline as information asymmetry. Without seeing every tool call and every prompt in the trace, I would have blamed the Synthesizer's prompt for being biased.</p>
<h2>Auto-instrumenting multi-agent frameworks</h2>
<p>Sentry auto-instruments the OpenAI Agents SDK, LangGraph, and other frameworks. The integration activates automatically when the package is detected. Here's the setup for the balanced research system:</p>
<pre><code class="language-python">

from agents import Agent, Runner, function_tool, ModelSettings

sentry_sdk.init(
    send_default_pii=True,   # captures prompts and responses in spans
    traces_sample_rate=1.0,
    enable_logs=True,
)

@function_tool
def web_search(query: str) -&gt; str:
    &quot;&quot;&quot;Search the web for information on a topic.&quot;&quot;&quot;
    ...

advocate = Agent(
    name=&quot;Advocate&quot;,
    model=&quot;gpt-5.4-mini&quot;,
    model_settings=ModelSettings(temperature=0.3),
    instructions=&quot;Build the strongest case FOR the position...&quot;,
    tools=[web_search],
)

skeptic = Agent(
    name=&quot;Skeptic&quot;,
    model=&quot;gpt-5.4-mini&quot;,
    model_settings=ModelSettings(temperature=0.3),
    instructions=&quot;Build the strongest case AGAINST the position...&quot;,
    tools=[web_search],
)

synthesizer = Agent(
    name=&quot;Synthesizer&quot;,
    model=&quot;gpt-5.4&quot;,
    model_settings=ModelSettings(temperature=0.5),
    instructions=&quot;Produce balanced analysis from two research briefs...&quot;,
)

async def analyze(topic: str):
    # Parallel execution: two independent trace trees
    advocate_result, skeptic_result = await asyncio.gather(
        Runner.run(advocate, topic),
        Runner.run(skeptic, topic),
    )

    synthesis_input = f&quot;&quot;&quot;
    Brief A: {advocate_result.final_output}
    Brief B: {skeptic_result.final_output}
    &quot;&quot;&quot;
    return await Runner.run(synthesizer, synthesis_input)
</code></pre>
<p><code>SENTRY_DSN</code> is read from the environment. <code>send_default_pii=True</code> is what enables prompt and response capture in spans, which is essential for debugging the handoff problems described above. The SDK creates <code>gen_ai.invoke_agent</code> spans for each agent, <code>gen_ai.execute_tool</code> spans for tool calls, and <code>gen_ai.request</code> spans for LLM calls with token counts and model info.</p>
<p>For JavaScript/TypeScript with the Vercel AI SDK or LangChain, use <code>tracesSampler</code> to capture AI routes at 100%:</p>
<pre><code class="language-js">

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  sendDefaultPii: true,
  tracesSampler: ({ name, attributes, inheritOrSampleWith }) =&gt; {
    if (attributes?.['sentry.op']?.startsWith('gen_ai.')) {
      return 1.0;
    }
    if (name?.includes('/api/chat') || name?.includes('/api/agent')) {
      return 1.0;
    }
    return inheritOrSampleWith(0.2);
  },
});
</code></pre>
<p>For more on why you should sample AI traces at 100%, see the companion post on <a href="https://blog.sentry.io/sample-ai-traces-at-100-percent-without-sampling-everything/">sampling strategies for agentic applications</a>.</p>
<h2>Building multi-agent dashboards</h2>
<p>Pre-built agent dashboards show per-model and per-tool aggregates. For multi-agent systems, you need to slice by agent. Some dashboards you can build with the <a href="https://cli.sentry.dev/">Sentry CLI</a>:</p>
<p><strong>Per-agent cost attribution:</strong></p>
<pre><code class="language-bash">sentry dashboard widget add 'Multi-Agent Monitoring' &quot;Cost by Agent&quot; \
  --display table --dataset spans \
  --query &quot;sum:gen_ai.usage.total_tokens&quot; &quot;count&quot; \
  --where &quot;span.op:gen_ai.invoke_agent&quot; \
  --group-by &quot;gen_ai.agent.name&quot; \
  --sort &quot;-sum:gen_ai.usage.total_tokens&quot;
</code></pre>
<p>This is how I found out the Synthesizer was 60% of my cost despite running once per query (because it uses <code>gpt-5.4</code> instead of <code>gpt-5.4-mini</code>).</p>
<p><strong>Tool reliability by agent:</strong></p>
<pre><code class="language-bash">sentry dashboard widget add 'Multi-Agent Monitoring' &quot;Tool Errors by Agent&quot; \
  --display table --dataset spans \
  --query &quot;failure_rate&quot; &quot;count&quot; \
  --where &quot;span.op:gen_ai.execute_tool&quot; \
  --group-by &quot;gen_ai.agent.name&quot; &quot;gen_ai.tool.name&quot; \
  --sort &quot;-failure_rate&quot;
</code></pre>
<p>If the Skeptic's <code>web_search</code> returns empty results 15% of the time while the Advocate's returns empty 3% of the time, you've found your lopsided synthesis problem before users report it.</p>
<p><strong>Agent duration comparison:</strong></p>
<pre><code class="language-bash">sentry dashboard widget add 'Multi-Agent Monitoring' &quot;Agent Duration p95&quot; \
  --display bar --dataset spans \
  --query &quot;p95:span.duration&quot; \
  --where &quot;span.op:gen_ai.invoke_agent&quot; \
  --group-by &quot;gen_ai.agent.name&quot;
</code></pre>
<p>Agents doing similar work should take similar time. Big duration gaps between parallel agents usually mean one is making more (or fewer) tool calls than expected.</p>
<h2>What I'd recommend if you're building multi-agent systems</h2>
<p>Based on debugging this system and reading a lot of traces:</p>
<p><strong>Capture prompts and responses at every agent boundary.</strong> This is the <code>send_default_pii=True</code> flag. Token counts show cost. But the prompts, responses, and tool input/output data are where you'll actually find bugs. The handoff boundaries between agents are where most multi-agent issues live.</p>
<p><strong>Name your agents clearly.</strong> &quot;Agent&quot; and &quot;Sub-Agent&quot; in your trace view tells you nothing. &quot;Advocate&quot; and &quot;Skeptic&quot; and &quot;Synthesizer&quot; tells a story you can follow.</p>
<p><strong>Compare parallel agents.</strong> When agents run concurrently and their outputs merge, the merge agent can't tell if its inputs were equally good. But you can tell from the traces. Look for asymmetry in tool call counts, token usage, and duration between agents that should be doing similar work.</p>
<p><strong>Sample at 100%.</strong> This matters even more for multi-agent than single-agent. A run that fails on a specific combination of tool results might happen 1 in 50 times. At 10% sampling, you'll need 500 runs before you capture one. See <a href="https://blog.sentry.io/sample-ai-traces-at-100-percent-without-sampling-everything/">how to sample AI traces at 100%</a> for the setup.</p>
<p><strong>Alert on tool failure rates per agent, not globally.</strong> A tool that fails 5% globally might fail 20% for one specific agent because of how it formulates queries. Global averages hide per-agent problems.</p>
<p><strong>Connect to your full stack.</strong> A slow <code>web_search</code> tool might be caused by rate limiting from an upstream API, not an agent issue. Multi-agent traces that sit inside your existing distributed traces let you see everything.</p>
<h2>Getting started</h2>
<p>If you're already using Sentry for agent monitoring, multi-agent traces work automatically. The SDKs detect agent invocations, handoffs, and tool calls.</p>
<p>Starting fresh:</p>
<ol>
<li><code>pip install sentry-sdk</code> or <code>npm install @sentry/node</code></li>
<li>Initialize with <code>traces_sample_rate=1.0</code> and <code>send_default_pii=True</code></li>
<li>Run your multi-agent workflow. Spans appear in Sentry's trace view.</li>
</ol>
<p>For setup across 10+ frameworks, see the <a href="https://blog.sentry.io/ai-agent-observability-developers-guide-to-agent-monitoring/">AI agent observability guide</a>.</p>
