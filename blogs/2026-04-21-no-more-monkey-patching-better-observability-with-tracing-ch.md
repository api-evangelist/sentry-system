---
title: "No more monkey-patching: Better observability with tracing channels"
url: "https://blog.sentry.io/observability-with-tracing-channels/"
date: "Tue, 21 Apr 2026 00:00:00 GMT"
author: ""
feed_url: "https://blog.sentry.io/feed.xml"
---
<p>Almost every production application uses a number of different tools and libraries, whether that's a library to communicate with a database, a cache, or frameworks like Nest.js or Nitro. To be able to observe what's going on in production, application developers reach out for <a href="https://sentry.io/solutions/application-performance-monitoring/">Application Performance Monitoring</a> (APM) tools like Sentry.</p>
<p>But there's an inherent problem: the performance data that APM tools need is most often not coming natively from the libraries themselves. The task of getting this data is delegated to APM tools like Sentry or <a href="https://blog.sentry.io/send-your-existing-opentelemetry-traces/">OpenTelemetry</a>, which instrument crucial functionality of a library on their behalf.</p>
<h2>What is instrumentation?</h2>
<p>The most fundamental requirement to make an application observable is the ability to instrument each of its components and the libraries it uses. <strong>Instrumentation</strong> is the process of adding code to a program to monitor and analyze its internal operations and generate diagnostic data. It's exactly what the Sentry SDKs and OpenTelemetry instrumentation are doing under the hood.</p>
<p>Consider a typical HTTP client library. Application developers want to know when a request starts and completes, along with some metadata like URL, status code and headers. Today, libraries handle this inconsistently: some provide custom hooks like <code>emitter.on('request', ...)</code>, while others offer vendor-specific middleware to intercept requests. In these cases, Sentry and OpenTelemetry can write plug-ins that emit observability data.</p>
<p>This works, but it puts the burden on the library or framework (e.g. Nuxt) to consciously design an instrumentation API and identify the right places to expose it. Hooks and interceptors allow injecting observability code at the correct spots, but APM maintainers are entirely dependent on library authors to keep those APIs stable over time. On top of that, there is no shared convention (each library exposes different hook shapes and different metadata) so APM maintainers must write and maintain very different plugins for each library.</p>
<h2>How server-side JavaScript is instrumented</h2>
<p>The traditional approach to JavaScript instrumentation is &quot;monkey-patching&quot;. That's modifying library code at runtime so that library functions not only do their original job, but also emit observability data. This is only possible in CommonJS (CJS), where modules are mutable and synchronously loaded.</p>
<p>However, the ecosystem is shifting. As server-side JavaScript moves further toward ES Modules (ESM), this approach breaks down. ES modules are immutable and loaded asynchronously, which means you simply can't patch imports at runtime the same way anymore. For further information: the <a href="https://github.com/getsentry/esm-observability-guide">ESM Observability Instrumentation Guide</a> covers this topic in greater detail.</p>
<p>The current workaround (and a way to &quot;patch&quot; imports) is using Module Customization Hooks paired with the <code>--import</code> flag. A popular hook is <code>import-in-the-middle/hook.mjs</code>. It works, but it's brittle, complex, and feels like what it is: a workaround.</p>
<p>Both monkey-patching in CJS and Module Customization Hooks in ESM share the same fundamental flaw: they apply instrumentation &quot;from the outside&quot;. The library itself is passive. The question worth asking is: <strong>what if libraries were active participants in their own observability and emit telemetry data themselves?</strong></p>
<p>This would be possible through diagnostics APIs like Tracing Channels.</p>
<h2>Libraries should emit their own telemetry</h2>
<p>Rather than waiting for APM tools to reach in and grab data, libraries can proactively expose their internal operations using tools built directly into the runtime. The right tool for this is <strong>Diagnostics Channels</strong>, and more specifically, <strong>Tracing Channels</strong>. Those features are being developed by the <a href="https://github.com/nodejs/diagnostics">Node.js Diagnostics Working Group</a>.</p>
<p>A huge shoutout to <a href="https://github.com/qard">Stephen Belanger</a>, the creator of the <code>diagnostics_channel</code> API in Node.js, who founded the working group and has been instrumental in pushing this topic forward. He's been providing feedback on proposals and acting as a voice of authority, which is sometimes exactly what's needed to convince library maintainers to get on board.</p>
<h3>Diagnostics Channels</h3>
<p><a href="https://nodejs.org/api/diagnostics_channel.html">Diagnostics Channels</a> are a high-performance, synchronous event system built directly into Node.js. They're also supported in Bun, Deno, and Cloudflare Workers (via the Node.js compatibility flag), making them a cross-runtime primitive.</p>
<p>Their primary use case is one-off events. For example, &quot;a connection was opened&quot; (like <code>node-redis</code> <a href="https://github.com/redis/node-redis/blob/41c908e6d65419fed6d985a9664427df1f48fb98/docs/diagnostics-channel.md?plain=1#L45-L48">does this here</a>). The limitation is that they don't inherently represent a full lifecycle. You have to manually link <code>start</code> and <code>stop</code> events to measure duration.</p>
<h3>Tracing Channels</h3>
<p><a href="https://nodejs.org/api/diagnostics_channel.html#class-tracingchannel">Tracing Channels</a> solve exactly that limitation. A Tracing Channel is a bundle of related Diagnostics Channels that automatically creates sub-channels for a complete operation lifecycle: <code>start</code>, <code>end</code>, <code>error</code>, and <code>asyncStart</code>. More importantly, a <code>TracingChannel</code> automatically propagates context across async boundaries. This means APM tools can correlate a database query back to the incoming HTTP request that caused it, without any manual bookkeeping.</p>
<p>Together, they give library and framework authors a standardized way to expose internal operations without coupling to any specific logging or tracing vendor. The library emits structured events and observability tools decide what to do with them.</p>
<h2>How libraries can implement Tracing Channels</h2>
<p>Tracing Channels have essentially zero cost when unused. If no subscriber is listening, emitting data costs almost nothing. It means library authors can add tracing channels without worrying about penalizing users who don't need observability. The benefits are that there is no monkey-patching needed anymore and it eliminates the need for users to pass <code>--import</code> flags for preloading in ESM.</p>
<h3>Naming and consistency: The channel is the contract</h3>
<p>Tracing Channels should always be scoped to the library that emits them, using the npm package name as the namespace. Since package names are globally unique, this keeps channel names collision-free. For example, <code>mysql2</code> ships <code>mysql2:query</code> which would emit <code>tracing:mysql2:query:start</code> and all other channels. And the <code>unstorage</code> library ships <code>unstorage.get</code> which emits <code>tracing:unstorage.get:start</code> and so on. The <a href="https://github.com/unjs/untracing"><code>untracing</code></a> package is working to establish broader naming standards across the ecosystem.</p>
<p>Equally important: Always emit a consistent data structure. Sentry and other APM tools can only provide automatic instrumentation if they know what shape your payload will have.</p>
<p>The pattern itself is straightforward. The library wraps its operation in a <code>tracePromise</code> call:</p>
<pre><code class="language-js">// Library side (e.g. inside ioredis)


const commandChannel = dc.tracingChannel(&quot;ioredis:command&quot;);

// In the command execution path:
commandChannel.tracePromise(
  async () =&gt; {
    return await executeCommand(cmd);
  },
  { command: cmd.name, args: cmd.args },
);
</code></pre>
<p>And on the consumer side, an SDK like Sentry subscribes to those events:</p>
<pre><code class="language-js">// Consumer side (e.g. Sentry SDK)


dc.tracingChannel(&quot;ioredis:command&quot;).subscribe({
  start(payload) {
    // create span
  },
  asyncEnd(payload) {
    // finish span
  },
  error({ error }) {
    // record error
  },
});
</code></pre>
<p>The library and the observability tool never need to know about each other. The channel is the contract.</p>
<h2>The ecosystem is already moving</h2>
<p>In early February 2026, we (<a href="https://github.com/andreiborza">Andrei</a>, <a href="https://github.com/JPeer264">Jan</a> and <a href="https://github.com/s1gr1d">Sigrid</a>) from Sentry attended <a href="https://opentelemetry.io/blog/2025/otel-unplugged-fosdem/">OTel Unplugged EU</a> and brought up the topic &quot;Prepare for better JS ESM Support&quot;, which was voted on the list of top priorities for the OpenTelemetry ecosystem.</p>
<p>So this isn't a theoretical proposal. A growing number of well-known libraries have already shipped or merged PRs for Diagnostics Channel and Tracing Channel support.</p>
<p>On the framework and HTTP side, <code>undici</code> (Node.js's built-in HTTP client) has <a href="https://undici-docs.vramana.dev/docs/api/DiagnosticsChannel">shipped Diagnostics Channels</a> since Node 20.12, and also <code>fastify</code> (<a href="https://fastify.dev/docs/latest/Reference/Hooks/#diagnostics-channel-hooks">docs</a>), <code>nitro</code> (<a href="https://github.com/nitrojs/nitro/pull/4001">PR</a>) and <code>h3</code> (<a href="https://github.com/h3js/h3/pull/1251">PR</a>) have native support. On the database side, <code>unstorage</code> (<a href="https://github.com/unjs/unstorage/pull/707">PR</a>) and <code>mysql2</code> (<a href="https://sidorares.github.io/node-mysql2/docs/documentation/tracing-channels">Docs</a>) already use Tracing Channels, and <code>pg</code> / <code>pg-pool</code> are actively working on it. Redis clients aren't far behind either and already support Tracing Channels in <code>ioredis</code> (<a href="https://github.com/redis/ioredis/pull/2089">PR</a>) and <code>node-redis</code> (<a href="https://github.com/redis/node-redis/pull/3195">PR</a>).</p>
<p>None of this happens without the people willing to do the work. A massive shoutout to Sentry engineer <strong>Abdelrahman Awad</strong> (<a href="https://github.com/logaretm">@logaretm</a>) for driving Tracing Channel implementations across multiple libraries. And a special thanks to <strong>Pooya Parsa</strong> (<a href="https://github.com/pi0">@pi0</a>), his openness to collaborate in <code>h3</code> and <code>nitro</code> was instrumental in formalizing this approach and showing the ecosystem what it could look like.</p>
<h2>The vision ahead</h2>
<p>We're still in a &quot;chicken and egg&quot; phase. Libraries need to add channels before APM tools have strong reasons to listen to them, and APM tools need to start listening before authors feel the pressure to add them.</p>
<p>The goal is <strong>universal JS observability</strong>: a world where Node.js, Bun, and Deno share the same diagnostic patterns, and instrumentation just works without monkey-patching in CJS, without <code>--import</code> flags in ESM, and without fragile workarounds. Libraries become active drivers of observability ensuring they are emitting data they think is the most relevant to their users.</p>
