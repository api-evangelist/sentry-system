---
title: "Sentry's integration with Perforce is now generally available"
url: "https://blog.sentry.io/perforce-integration-ga/"
date: "Wed, 29 Apr 2026 00:00:00 GMT"
author: ""
feed_url: "https://blog.sentry.io/feed.xml"
---
<h2>Perforce meets Sentry</h2>
<p>If you work in game development, VFX, or any industry dealing with large binary assets, chances are your codebase lives in Perforce P4. It's the version control system behind some of the biggest games and creative projects in the world — and until now, it's been one of the last major SCMs without first-class Sentry support.</p>
<p>Today, we're changing that. The Sentry + Perforce P4 integration is now generally available for all Sentry organizations.</p>
<h2>What you get</h2>
<p>The integration connects your P4 server directly to Sentry, unlocking the same source-code-aware debugging workflows that Git-based teams have relied on for years:</p>
<ul>
<li><strong>Stack trace linking</strong> — Click from an error's stack trace directly to the corresponding file in your Perforce P4 depot or P4 Code Review (formerly Helix Swarm) instance.</li>
<li><strong>Commit tracking</strong> — Associate Perforce P4 changelists with Sentry releases so you always know exactly what code shipped.</li>
<li><strong>Suspect commits</strong> — Sentry automatically identifies which changelists likely introduced an error, cutting your triage time.</li>
<li><strong>Suggested assignees</strong> — Get assignment recommendations based on changelist authorship — the person who last touched the code gets surfaced first.</li>
<li><strong>P4 Code Review (formerly Swarm) linking</strong> — If your team uses the P4 Code Review (formerly Helix Swarm) application for code reviews and browser-based depot browsing, Sentry links directly to your P4 Code Review instance. Stack trace links open the exact file in P4 Code Review's web UI, so reviewers and investigators can jump from an error straight into the code without needing a local workspace.</li>
</ul>
<h2>On-demand source context</h2>
<p>The most requested capability during our beta: show me the code. Previously, showing the source code for native crashes required users to upload source maps to Sentry. Without source maps, showing the stacktraces was possible, but they didn't have source context.</p>
<p>With on-demand SCM source context, Sentry fetches source code directly from your Perforce P4 depot and displays it inline in the stack trace — even when your crash dumps or error reports don't include embedded source. Expand any in-app frame and Sentry pulls the relevant lines on the fly, with the error line highlighted.</p>
<p>This is especially valuable for native game development workflows where minidumps and crash reports rarely carry source context. Instead of switching to your IDE or running <code>p4 print</code> manually, the code is right there in the issue.</p>
<h2>How it works</h2>
<p>Setup takes just a few minutes:</p>
<ul>
<li><strong>Connect your server</strong> — Go to Settings &gt; Integrations &gt; Perforce and enter your credentials.</li>
<li><strong>Configure code mappings</strong> — Map your Sentry projects to Perforce P4 depots and set up path translations.</li>
<li><strong>Enable source context (optional)</strong> — Turn on SCM source context in your project's General Settings to get inline code in stack traces.</li>
</ul>
<p>Sentry communicates with your Perforce P4 server using the P4Python library, executing lightweight read-only commands (<code>p4 depots</code>, <code>p4 changes</code>, <code>p4 print</code>). Each organization maintains isolated credentials, and we support both password-based auth and pre-generated P4 tickets for LDAP environments.</p>
<h2>How to get started</h2>
<p>During beta, we worked closely with multiple game studios to battle-test the integration against real-world Perforce P4 deployments. We resolved concurrency challenges around P4 trust and ticket file isolation, ensuring connections stay clean and independent — whether you have one project or hundreds hitting the same or multiple servers.</p>
<p>The Perforce integration is available now for all Sentry organizations.</p>
<ul>
<li><a href="https://docs.sentry.io/organization/integrations/source-code-mgmt/perforce/">Read the Docs</a></li>
</ul>
<p>Have questions or feedback?</p>
<ul>
<li>Join the conversation <a href="https://discord.com/invite/sentry">in our Discord</a></li>
<li>Email us at <a href="mailto:gaming-updates@sentry.io">gaming-updates@sentry.io</a></li>
</ul>
<p>New to Sentry?</p>
<ul>
<li>Try <a href="https://sentry.io/signup/">Sentry for free</a></li>
</ul>
