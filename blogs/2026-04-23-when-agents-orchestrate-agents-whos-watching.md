---
title: "When agents orchestrate agents, who's watching?"
url: "https://blog.sentry.io/scaling-observability-for-multi-agent-ai-systems/"
date: "Thu, 23 Apr 2026 00:00:00 GMT"
author: ""
feed_url: "https://blog.sentry.io/feed.xml"
---
<p>You used to monitor services.</p>
<p>Then you started monitoring AI calls inside services.</p>
<p>Now your AI agent is spinning up <em>other</em> AI agents to complete tasks. Your old monitoring instincts need to evolve.</p>
<p>This isn't hypothetical. Agentic architectures are already in production. Coding agents are calling search agents; orchestrators are spawning specialized sub-agents for retrieval, planning, and execution. Teams are shipping these systems faster than they're figuring out how to watch them.</p>
<p>The problem isn't that agents fail. It's that when they do, you often can't tell which agent introduced the failure, or whether anything technically failed at all.</p>
<h2>Traditional tracing wasn't built for this</h2>
<p>In a traditional stack, debugging a request means following one thread from entry point to database. One service, one owner, one place to look.</p>
<p>In a multi-agent system, a single user action might trigger a planner agent, three tool-call agents, a validation agent, and a write agent. That's five actors, potentially across different models, different prompts, and very different latency budgets. Errors don't always surface as exceptions. A bad output from a sub-agent might not throw an error at all. It might just start the spiral, propagating as <em>context corruption</em> further down the chain. The orchestrator thinks it succeeded. The user sees something wrong. You open your logs and find nothing obviously broken.</p>
<p>If you want to see what this looks like in practice, <a href="https://blog.sentry.io/debugging-multi-agent-ai-when-the-failure-is-in-the-space-between-agents/">this breakdown of a real multi-agent debugging session</a> shows exactly how a silent tool failure two hops upstream can corrupt final output without triggering a single error. It's a good illustration of why the instinct to &quot;read the logs&quot; stops working at this level of complexity. In this world, little missteps compound and avalanche.</p>
<p>This post focuses on what that complexity looks like when you're operating at scale, across teams, with enterprise reliability expectations.</p>
<h2>The visibility problem compounds with scale</h2>
<p>One agent is readable. Two agents are manageable. Five agents calling each other conditionally, with branching logic and shared context? It's a different category of problem entirely.</p>
<p>You're no longer debugging code execution. You're debugging emergent behavior across a distributed decision graph. The same way microservices made &quot;it's slow somewhere in the stack&quot; a meaningless statement without traces, multi-agent systems make &quot;the AI did something wrong&quot; nearly impossible to act on without the right instrumentation.</p>
<p>Most teams discover this the hard way. Maybe that's a sudden uptick in user churn with no clear cause, or an LLM silently returning bad data three hops down the chain. A token cost bill that tripled overnight. No alert fired because no single component technically crossed a threshold.</p>
<p>Distributed tracing solved this exact problem for microservices. The question is whether your AI pipeline is instrumented to handle the next version of that problem.</p>
<h2>What actual, useful multi-agent monitoring looks like</h2>
<p>Getting visibility into multi-agent systems isn't a new product category. It's about applying the right primitives with the right granularity. <a href="https://sentry.io/solutions/ai-observability/">Sentry's AI observability</a> tooling is built on the same foundation as its distributed tracing, which means the mental model transfers even as the complexity scales. Here's what that actually requires:</p>
<p><strong>Trace continuity across agent handoffs.</strong> The trace ID needs to follow the task through every agent invocation, not restart at each boundary. You need to see the full tree: who called what, in what order, with what inputs and outputs. A flat list of spans with the same parent doesn't offer the same value when you need to understand which agent in the middle of the chain introduced a bad state.</p>
<p><strong>Per-agent span attribution.</strong> Latency, token usage, model version, prompt hash, and output signal should be attributable to each agent individually, not rolled up to the top-level call. Knowing your orchestrator took 4.2 seconds tells you almost nothing. Knowing it was waiting 3.8 seconds on a retrieval sub-agent that returned low-confidence results tells you exactly where to go. This level of attribution is possible by attaching metadata such as model version, token counts, and prompt identifiers to each span during instrumentation.</p>
<p><strong>Failure mode differentiation.</strong> Agent timeout, bad tool call output, context window overflow, model refusal, and hallucination downstream of a technically valid response are completely different problems with completely different fixes. Grouping them all as &quot;AI errors&quot; is the equivalent of logging every 500 as &quot;server error.&quot; Technically accurate, operationally useless.</p>
<p><strong>Cost and token attribution at the task level.</strong> A task that spawned six agents and consumed 40K tokens is a different animal than one that consumed 4K. You need this at query time, broken down per transaction, per user, and per feature. Not buried in an end-of-month billing aggregate. Just tag spans with cost and usage metadata at each agent boundary during instrumentation.</p>
<p><strong>Nested span trees showing agent relationships.</strong> Sentry's trace view shows agent invocations as nested spans, so you can see which agent called which, in what order, and what each one consumed. When multiple agents are calling a shared tool or a downstream agent is being invoked by more than one parent, that structure is visible in the trace.</p>
<h2>Where Sentry fits</h2>
<p>Sentry already has the primitives: distributed tracing, spans, breadcrumbs, performance metrics. If you're using the Sentry SDK in your AI pipeline, you're closer than you think.</p>
<p>For supported frameworks, setup is minimal. Sentry auto-instruments agent invocations, tool calls, and LLM requests across the major AI frameworks in both Python and Node.js, including OpenAI, Anthropic, Google GenAI, LangChain, LangGraph, Pydantic AI, OpenAI Agents SDK, and Vercel AI SDK. Install the SDK and enable tracing to capture baseline visibility. Sentry can group similar failures across runs based on error patterns and metadata. For multi-agent systems, you'll typically extend this with custom spans and tags to reflect your agent architecture.</p>
<p>Here's what that looks like for the OpenAI Agents SDK in Python:</p>
<pre><code class="language-python">
from sentry_sdk.integrations.openai_agents import OpenAIAgentsIntegration

sentry_sdk.init(
    dsn=&quot;YOUR_DSN&quot;,
    traces_sample_rate=1.0,
    integrations=[OpenAIAgentsIntegration()],
)
</code></pre>
<p>If your framework isn't on the supported list, manual instrumentation takes about 10 lines of code per span type using Sentry's <code>gen_ai.*</code> span conventions such as <code>gen_ai.invoke_agent</code>, <code>gen_ai.execute_tool</code>, and <code>gen_ai.request</code>.</p>
<p>One thing worth knowing before you set this up: all AI integrations capture prompt and response content by default, since <code>recordInputs</code> and <code>recordOutputs</code> both default to <code>true</code>. If your prompts or responses contain sensitive data, set both to <code>false</code>. Make sure your privacy policy permits capturing this content before going to production with the defaults enabled.</p>
<p>Either way, you end up with a trace tree showing nested agent invocations, tool executions, and LLM calls as child spans. That gives you visibility into execution and performance. Understanding output correctness and decision quality still requires additional validation layers on top.</p>
<p><strong>Seer can help reduce time-to-triage.</strong> When a multi-agent task fails and you have a trace spanning five agents, <a href="https://sentry.io/product/seer/">Seer</a> can analyze the error context, surface the most likely source of degradation, and give you a starting point grounded in your actual production data rather than five equally plausible places to begin.</p>
<p>For a full setup guide across supported frameworks, the <a href="https://blog.sentry.io/ai-agent-observability-developers-guide-to-agent-monitoring/">AI agent observability guide</a> covers instrumentation in detail. Start there if you're setting this up for the first time.</p>
<p>Practical starting point: instrument your orchestrator first. Get the top-level task as a transaction, with each agent call as a child span. Even partial visibility is better than none when you're trying to triage a production degradation at 2am.</p>
<h2>This is the readiness question</h2>
<p>Teams adopting agentic systems are going to face the same question their SRE teams faced when they migrated from monolith to microservices: how do we know this is working?</p>
<p>It's not a question that stays abstract for long. The first time an agent-orchestrated workflow produces a wrong answer at scale, or quietly runs up a token bill nobody can attribute, or degrades in a way that no individual span flagged, that's when the question becomes urgent. By then, the teams that already instrumented are triaging. The teams that didn't are left guessing.</p>
<p>The teams that answer that question first with real traces, real attribution, and real alerting are the ones that get to keep running agents in production. The others roll it back after the first incident they can't explain.</p>
<p>Multi-agent observability isn't a nice-to-have at scale. It's table stakes for anyone taking agents beyond the prototype phase. The complexity doesn't ask permission before it shows up in production. It's already there.</p>
