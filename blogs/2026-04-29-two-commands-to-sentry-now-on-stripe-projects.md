---
title: "Two commands to Sentry: now on Stripe Projects"
url: "https://blog.sentry.io/sentry-stripe-projects/"
date: "Wed, 29 Apr 2026 07:00:00 GMT"
author: ""
feed_url: "https://blog.sentry.io/feed.xml"
---
<p>Two commands. That's how little it takes to go from nothing to a fully configured Sentry project with error monitoring, performance tracing, and session replay:</p>
<pre><code class="language-bash">stripe projects init my-app
stripe projects add sentry/project
</code></pre>
<p>No signup form. No email verification dance. No dashboard tab-switching to copy-paste a DSN into your <code>.env</code>. Your account is created, your project is provisioned, and five environment variables land in your working directory, ready for your SDK to pick up.</p>
<p>And if you're using a coding agent? It does the same thing, except you didn't type the commands. You just said &quot;add error monitoring.&quot;</p>
<h2>What this actually is</h2>
<p>Sentry is now a provider in <a href="https://projects.dev">Stripe Projects</a>. Stripe Projects is a CLI workflow that lets developers (and their AI agents) discover, provision, and manage infrastructure services directly from the terminal. Think of it as a package manager, but for the services your app depends on at runtime.</p>
<p>Here's the full catalog:</p>
<pre><code>$ stripe projects catalog sentry

SERVICES
    project   ● Free tier
    seer      ● Paid

PLANS
    developer ● Free
    team      ● $29/month
    business  ● $89/month
</code></pre>
<p>Two deployable services (a Sentry project and Seer AI), three plan tiers. All manageable from the CLI. Billing goes through your existing Stripe payment method, with no separate Sentry billing setup.</p>
<h2>The &quot;just tell your agent&quot; part</h2>
<p>When you run <code>stripe projects init</code>, it scaffolds agent skill files into your project:</p>
<pre><code>.agents/skills/stripe-projects-cli/SKILL.md
.claude/skills/stripe-projects-cli/SKILL.md
.cursor/rules/stripe-projects-cli.md
</code></pre>
<p>These teach Claude Code, Cursor, or any coding agent how to use the Stripe Projects CLI. The agent reads the skill, discovers available services via <code>stripe projects catalog sentry --json</code>, and provisions what you need.</p>
<p>A real interaction looks like this:</p>
<pre><code>You: &quot;Add error monitoring to this project&quot;

Agent: [runs stripe projects catalog sentry --json]
Agent: [runs stripe projects add sentry/project --no-interactive --accept-tos]
Agent: &quot;Done. Sentry is provisioned. Your DSN and auth token are in .env.
        I can integrate the Sentry SDK into your app next.&quot;
</code></pre>
<p>The agent doesn't need special Sentry knowledge. It just needs the Stripe Projects CLI and the skill file that <code>init</code> already created. Provisioning becomes a step it handles between writing your code and running your tests.</p>
<p>Once the account is provisioned, you can ask the agent to instrument your app with <code>sentry init</code>. Since the auth token and DSN are already in the environment, the <a href="https://cli.sentry.dev">Sentry CLI</a> knows exactly which project to target, with no configuration prompts, no guesswork.</p>
<h2>Upgrades, downgrades, and the billing dance</h2>
<p>Plans are first-class resources:</p>
<pre><code class="language-bash">stripe projects add sentry/team                          # start on Team ($29/mo)
stripe projects upgrade sentry-plan sentry/business      # upgrade to Business
stripe projects downgrade sentry-plan sentry/team        # change your mind
</code></pre>
<p>All non-interactive, all scriptable. Billing happens through Stripe's <a href="https://docs.stripe.com/agentic-commerce/concepts/shared-payment-tokens">Shared Payment Token</a>, so your existing Stripe payment method pays for Sentry with zero billing configuration on the Sentry side.</p>
<p>You can also add Seer (our AI debugging assistant) as a separate service:</p>
<pre><code class="language-bash">stripe projects add sentry/seer      # $40/active contributor/month
stripe projects remove sentry-seer   # if you change your mind
</code></pre>
<h2>The magic login</h2>
<p>This one's my favorite. When you need your Sentry dashboard:</p>
<pre><code class="language-bash">stripe projects open sentry
</code></pre>
<p>This mints a single-use magic login URL. Click it (or let the CLI open it), and you're logged into your Sentry dashboard. You skip the password prompt, the single sign-on (SSO) redirect, and the &quot;which account was this again?&quot; moment. Straight to your issues page.</p>
<p>It's the kind of feature that sounds trivial but has a surprisingly dense security model once you start thinking about two-factor authentication (2FA) users, expired passwords, and SSO bypass prevention.</p>
<h2>Multi-team collaboration</h2>
<p>Here's where it gets interesting. Stripe's protocol distinguishes between the <strong>account owner</strong> (the Stripe account's email) and the <strong>actor</strong> (the person running the CLI command). We use this to build a proper collaboration model:</p>
<ul>
<li>First team member runs <code>stripe projects add sentry/project</code> → creates the Sentry org</li>
<li>Second team member runs the same command → joins the <em>same</em> Sentry org</li>
<li>Everyone shares one org, one billing setup, one set of projects</li>
</ul>
<p>We tie the Sentry organization to the Stripe organization, so everyone on the same Stripe account ends up in the same Sentry org. No per-developer silos, no &quot;who created this and why can't I see it&quot; conversations.</p>
<h2>Credential rotation</h2>
<pre><code class="language-bash">stripe projects rotate sentry-project
</code></pre>
<p>New DSN, new auth token, old ones revoked. The fresh credentials land in your <code>.env</code> automatically. The dashboard stays closed.</p>
<h2>How to try it</h2>
<ol>
<li><p>Install the Stripe CLI and the Projects plugin:</p>
<pre><code class="language-bash">stripe plugin install projects
</code></pre>
</li>
<li><p>Initialize and add Sentry:</p>
<pre><code class="language-bash">stripe projects init my-app
stripe projects add sentry/project
</code></pre>
</li>
<li><p>That's it. Your <code>SENTRY_DSN</code> and <code>SENTRY_AUTH_TOKEN</code> are in <code>.env</code>, ready for any Sentry SDK to pick up.</p>
</li>
</ol>
<p>For the full lifecycle (catalog browsing, plan management, Seer, deep links, credential rotation), check the <a href="https://projects.dev">Stripe Projects documentation</a>.</p>
<p>If you're building with AI coding agents and want error monitoring that provisions itself, <a href="https://projects.dev">give it a try</a>. And if your agent breaks something in the process, well, you'll have Sentry to tell you about it.</p>
