---
title: "Grave improvements: Native crash postmortems via Android tombstones"
url: "https://blog.sentry.io/native-crash-postmortems-via-android-tombstones/"
date: "Wed, 15 Apr 2026 00:00:00 GMT"
author: ""
feed_url: "https://blog.sentry.io/feed.xml"
---
Native crashes on Android have always been harder to debug than they should be. The platform has its own crash reporter ( debuggerd ) that captures the crashing thread, every other running thread, register state, and memory maps into a file called a tombstone. Tombstones have been a part of Android for a long time; in fact, they've been there in one form or another since Android's first commit.
