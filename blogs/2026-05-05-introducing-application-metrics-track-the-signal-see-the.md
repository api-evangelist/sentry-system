---
title: "Introducing Application Metrics: Track the signal, see the spike, jump to the trace"
url: "https://blog.sentry.io/introducing-application-metrics/"
date: "2026-05-05"
author: ""
feed_url: "https://blog.sentry.io/feed.xml"
---
A few weeks ago we had a bug with Session Replay. Replays were failing in some browsers once more than 1,000 video segments loaded. We had no idea how often it happened or who was hitting it, and because the failure didn't always produce an error, we had no way to find affected users to reproduce it. Before, we could've answered this with spans or logs, but it's clunky — spans are often sampled, so you can miss outliers; logs are less structured and tend to change over time. Both are better suited for investigation. Metrics are ideal for tracking known behaviors over time.
