---
title: "Fixing JavaScript observability, one library at a time"
url: "https://blog.sentry.io/fixing-javascript-observability/"
date: "2026-05-07"
author: ""
feed_url: "https://blog.sentry.io/feed.xml"
---
Over the past few weeks, we have been driving a cross-ecosystem effort to replace the "monkey-patching" that powers all JavaScript APM tools today with something built into the runtime. Here is why, how, and where it stands. This applies to server-side JavaScript only (Node.js, Bun, Deno, Cloudflare Workers). Browsers do not have diagnostics_channel and lack the async context propagation primitives needed to polyfill it. Monkey-patching does not scale My teammate Sigrid wrote a detailed breakdown of why monkey-patching is failing and how TracingChannel solves it.
