# Self-Host Discourse on DigitalOcean: A Complete Walkthrough From Droplet to Live Forum — Pricing, Plugins, Email Setup, and Common Pitfalls Explained (With a $200 Free Credit to Get You Started)

If you've ever tried to set up a modern community forum, you already know the rabbit hole it can become. One minute you're comparing phpBB to Flarum, the next you're three browser tabs deep into Docker tutorials and arguing with yourself about whether 1 GB of RAM is "really" enough. The keyword that probably brought you here — **digital ocean discourse** — is a tell. You've already narrowed it down: you want Discourse, and you want it on DigitalOcean. Smart choice. Discourse is the most polished open-source forum platform of the last decade, and DigitalOcean's one-click Marketplace app is the single fastest way to get a Discourse instance live without losing a weekend to manual configuration.

This is the guide I wish I'd had the first time I deployed a Discourse forum. It's not a sales pitch and it's not a dry manual — it's the practical, end-to-end walkthrough of what actually happens when you spin up **digital ocean discourse**, what it costs, where the traps are, and how to choose between self-hosting and Discourse's own managed plans. Let's get into it.

## Why Discourse, and Why DigitalOcean Specifically

Discourse is the brainchild of Stack Overflow co-founder Jeff Atwood and a team that has spent years obsessing over what makes online conversation good. It's a 100% open-source Ruby on Rails application that runs inside a Docker container, ships with a real-time chat layer, trust levels, gamification, and a moderation toolkit that puts older forum software to shame. The 2026 community-platform reviews consistently put Discourse at or near the top for discussion quality — FeverBee's most recent evaluation scored it 9/10 for discussions and 8/10 for AI and moderation, calling it the best discussion platform in their series.

But Discourse has a personality: it's a little heavy. The official install documentation recommends a modern single-core CPU (dual-core recommended), a minimum of 1 GB RAM with swap, and at least 10 GB of disk. In practice, anyone who has run Discourse on a 1 GB VPS will tell you the same thing — it technically works, but you'll be happier at 2 GB, and you'll be much happier at 4 GB once your community starts actually using it. The official Discourse 1-Click app page on DigitalOcean's Marketplace explicitly recommends "at least 2GB RAM machine for your Discourse droplet."

That's where DigitalOcean comes in. DigitalOcean's Droplets are Linux-based virtual machines with predictable monthly pricing, a clean control panel, and — critically for **digital ocean discourse** — a Marketplace 1-Click App that pre-bakes Discourse onto Ubuntu 24.04. You don't have to clone a repo, write a `docker-compose.yml`, or wrestle with Nginx. The script that runs on first boot takes roughly 10 to 15 minutes to download, install, and configure Discourse and all of its dependencies, and then hands you a working forum at your droplet's IP.

## What You Actually Get With the DigitalOcean Discourse 1-Click App

When you create a Droplet from the Discourse 1-Click App, you're getting a curated stack rather than a blank Ubuntu box. Here's what's included out of the gate:

- **Discourse** (latest stable), running inside its official Docker container
- **Ubuntu 24.04 LTS** as the base operating system
- **Docker** pre-installed and configured
- **Nginx** as the front-facing web server
- **Let's Encrypt** integration for free SSL certificates
- The Discourse setup wizard (`discourse-setup`), which walks you through editing `app.yml` — the file that controls your forum's domain, SMTP credentials, and admin account

The first time you SSH into the droplet, you'll be prompted to fill in a handful of values: your domain name, the email address of the first admin, and the SMTP settings for whatever transactional email provider you choose (Mailgun, SendGrid, Mailjet, and Amazon SES are all commonly used). Once that's done, the launcher rebuilds the container and, after another few minutes, your forum is reachable at `https://yourdomain.com`.

> **A note on the one-click install "not finishing" issue**: The Discourse Meta forums have a long-running thread about installs that appear to hang. In almost every case, the cause is either insufficient RAM (the rebuild step is memory-hungry) or DNS not yet pointing at the droplet. If your install seems stuck, give the droplet more RAM and verify your A records before you panic.

## The Real Cost of Self-Hosting Discourse on DigitalOcean

Here's where most **digital ocean discourse** guides get vague. Let's be specific. Effective January 1, 2026, DigitalOcean moved to per-second billing for Droplets, with a 60-second minimum (or $0.01, whichever is higher) and a monthly cap — so you'll never pay more than the listed monthly price even if you leave a droplet running all month. The full Droplet pricing page breaks down five families, but for a Discourse forum you realistically only need to consider the first two.

### Basic Droplets (the sweet spot for most Discourse forums)

Basic Droplets use shared vCPUs and are the most cost-efficient option for workloads that don't need dedicated cores. For a small-to-medium Discourse community, the 2 GiB / 1 vCPU plan at $12/month is the absolute floor I'd recommend, and the 4 GiB / 2 vCPUs plan at $24/month is what most people actually end up on once their forum sees real traffic.

### CPU-Optimized and General Purpose Droplets (for busy communities)

Once you're past a few hundred active daily users, the shared CPU on a Basic Droplet starts to feel the strain during rebuilds and sidekiq job spikes. CPU-Optimized Droplets give you dedicated vCPUs at a 2:1 memory-to-CPU ratio with 2.6 GHz+ cores; the 4 GiB / 2 vCPUs plan at $42/month is a common upgrade path. General Purpose Droplets offer a balanced 4:1 ratio for production workloads.

### Full DigitalOcean Droplet Plan Comparison

Below is every Droplet tier currently listed on DigitalOcean's official pricing page — none omitted. The "Purchase" links route through the affiliate program, so if you're signing up fresh you'll also pick up the new-user credit that's detailed in the next section.

| Plan Family | Memory | vCPU | Transfer | SSD | $/hr | $/mo | Get Started |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **Basic** | 512 MiB | 1 | 500 GiB | 10 GiB | $0.00595 | $4.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **Basic** | 1 GiB | 1 | 1,000 GiB | 25 GiB | $0.00893 | $6.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **Basic** | 2 GiB | 1 | 2,000 GiB | 50 GiB | $0.01786 | $12.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **Basic** | 2 GiB | 2 | 3,000 GiB | 60 GiB | $0.02679 | $18.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **Basic** | 4 GiB | 2 | 4,000 GiB | 80 GiB | $0.03571 | $24.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **Basic** | 8 GiB | 4 | 5,000 GiB | 160 GiB | $0.07143 | $48.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **Basic** | 16 GiB | 8 | 6,000 GiB | 320 GiB | $0.14286 | $96.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **CPU-Optimized** | 4 GiB | 2 | 4,000 GiB | 25 GiB | $0.06250 | $42.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **CPU-Optimized** | 8 GiB | 4 | 5,000 GiB | 50 GiB | $0.12500 | $84.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **CPU-Optimized** | 16 GiB | 8 | 6,000 GiB | 100 GiB | $0.25000 | $168.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **CPU-Optimized** | 32 GiB | 16 | 7,000 GiB | 200 GiB | $0.50000 | $336.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **CPU-Optimized** | 64 GiB | 32 | 9,000 GiB | 400 GiB | $1.00000 | $672.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **CPU-Optimized** | 96 GiB | 48 | 11,000 GiB | 600 GiB | $1.50000 | $1,008.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **General Purpose** | 8 GiB | 2 | 4,000 GiB | 25 GiB | $0.09375 | $63.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **General Purpose** | 16 GiB | 4 | 5,000 GiB | 50 GiB | $0.18750 | $126.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **General Purpose** | 32 GiB | 8 | 6,000 GiB | 100 GiB | $0.37500 | $252.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **General Purpose** | 64 GiB | 16 | 7,000 GiB | 200 GiB | $0.75000 | $504.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **General Purpose** | 128 GiB | 32 | 8,000 GiB | 400 GiB | $1.50000 | $1,008.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **General Purpose** | 160 GiB | 40 | 9,000 GiB | 500 GiB | $1.87500 | $1,260.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **Memory-Optimized** | 16 GiB | 2 | 4,000 GiB | 50 GiB | $0.12500 | $84.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **Memory-Optimized** | 32 GiB | 4 | 6,000 GiB | 100 GiB | $0.25000 | $168.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **Memory-Optimized** | 64 GiB | 8 | 7,000 GiB | 200 GiB | $0.50000 | $336.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **Memory-Optimized** | 128 GiB | 16 | 8,000 GiB | 400 GiB | $1.00000 | $672.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **Memory-Optimized** | 192 GiB | 24 | 9,000 GiB | 600 GiB | $1.50000 | $1,008.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **Memory-Optimized** | 256 GiB | 32 | 10,000 GiB | 800 GiB | $2.00000 | $1,344.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **Storage-Optimized** | 16 GiB | 2 | 4,000 GiB | 300 GiB | $0.19494 | $131.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **Storage-Optimized** | 32 GiB | 4 | 6,000 GiB | 600 GiB | $0.38988 | $262.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **Storage-Optimized** | 64 GiB | 8 | 7,000 GiB | 1,170 GiB | $0.77976 | $524.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **Storage-Optimized** | 128 GiB | 16 | 8,000 GiB | 2,340 GiB | $1.55952 | $1,048.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **Storage-Optimized** | 192 GiB | 24 | 9,000 GiB | 3,520 GiB | $2.33929 | $1,572.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |
| **Storage-Optimized** | 256 GiB | 32 | 10,000 GiB | 4,690 GiB | $3.11905 | $2,096.00 | [Create Discourse Droplet](https://bit.ly/DigitaLocean) |

A few notes that don't fit neatly into a table cell:

- Every Droplet includes a generous amount of free outbound data transfer (starting at 500 GiB/month on the smallest plan and scaling up). Inbound bandwidth is always free.
- Every Droplet ships with a dedicated public IPv4 address, free monitoring, and free firewalls.
- Backups are an add-on: percentage-based backups run 20% (weekly) or 30% (daily) of the Droplet cost, while usage-based backups start at $0.06/GB per month with flexible retention from 3 days to 6 months.
- Snapshots are billed at $0.06/GB per month if you want to keep a frozen image of a working Discourse setup for fast redeployment.

For the typical **digital ocean discourse** use case — a single forum with a few hundred to a few thousand members — the math is straightforward. A 2 GiB / 2 vCPUs Basic Droplet at $18/month plus daily backups (about $5.40/month) puts you at roughly $23/month all-in. That's a fraction of what managed Discourse hosting costs, and you keep full root access to your own data.

## The $200 Free Credit: How the Affiliate Signup Actually Works

This is the part that makes the **digital ocean discourse** path genuinely cheap to try. When you sign up through a referral link, DigitalOcean's standard offer credits new accounts with $200 in free credit valid for 60 days (some recent community reports note the default has shifted to a smaller $5/90-day credit on certain flows, but the referral program's documentation still describes the $200/60-day offer as the headline incentive). The credit is auto-applied — there's no coupon code to fumble with. You add a payment method, the credit lands in your account, and your first $200 of usage is on the house.

That's enough to run a 4 GiB / 2 vCPUs Basic Droplet for over eight months at full price, or to comfortably test-drive a CPU-Optimized Droplet for a couple of months while you stress-test your forum. If you're ready to take that route, you can 👉 [claim the new-user credit and spin up your Discourse Droplet here](https://bit.ly/DigitaLocean).

## Self-Hosting on DigitalOcean vs. Official Discourse Managed Plans

The honest answer to "should I self-host or pay Discourse to host for me?" depends entirely on how much you value your own time. Discourse offers four official plans on their pricing page, and they're worth understanding because they're the natural comparison point for anyone evaluating **digital ocean discourse** as a path.

| Discourse Plan | Price | Best For | Staff Seats | Plugins | Key Differentiator |
| --- | --- | --- | --- | --- | --- |
| **Free** | $0 | Exploring whether a community fits your org | 2 | Core only | Limited, for evaluation |
| **Pro** | $100/month | Growing communities that need scale and spam tools | 5 | 15+ (DiscourseConnect, Chat integration, Solved, GitHub, etc.) | Custom domain, API/webhooks, custom themes |
| **Business** | $500/month | Communities that are a core channel | 15 | 20+ additional (Data explorer, Policy, Calendar, User notes, etc.) | Advanced reporting, automation, events, gamification, SSO |
| **Enterprise** | Custom | Mission-critical communities | Unlimited | 50+ (Code review, Saved search, Topic tooltips, SAML, etc.) | Expert design/dev services, 99.9% uptime SLA, GDPR/CCPA hosting, migration from Khoros/XenForo/Salesforce |

The math is brutal in a useful way. A self-hosted Discourse forum on a $24/month DigitalOcean Droplet gives you the same Discourse software, the same plugin ecosystem (every plugin is open source and installable on a self-hosted instance), the same themes, and the same API access as the $100/month Pro plan. What you don't get is Discourse's team managing updates, backups, and infrastructure for you, and you don't get their email support.

For a hobbyist community, a small business that already has a developer, or anyone who wants full control of their data, self-hosting on DigitalOcean is the obvious economic choice. For a company that needs the forum to be a customer-facing channel and has no one who wants to be on call for it, the Pro or Business plan is the obvious operational choice. There's no wrong answer — just different trade-offs.

## Step-by-Step: From Zero to Live Forum in an Afternoon

Here's the actual sequence I'd run through, condensed from the official Discourse install documentation and the DigitalOcean Marketplace getting-started guide.

1. **Sign up and claim your credit.** Create a DigitalOcean account through the referral link, add a payment method, and confirm the $200 credit appears in your billing dashboard.
2. **Point your domain at DigitalOcean's nameservers.** Update your registrar's nameservers to `ns1.digitalocean.com`, `ns2.digitalocean.com`, and `ns3.digitalocean.com`, then use DigitalOcean's DNS quickstart to create an A record for your forum's hostname pointing at the droplet's IP (you'll fill in the IP after step 3).
3. **Create the Discourse Droplet.** From the Marketplace, hit "Create Discourse Droplet," pick a region close to your audience (DigitalOcean runs datacenters in NYC, San Francisco, Toronto, London, Amsterdam, Frankfurt, Bangalore, Singapore, and Sydney among others), and choose a plan. Start at 2 GiB / 2 vCPUs ($18/month) for a brand-new forum; jump to 4 GiB / 2 vCPUs ($24/month) if you expect traffic on day one.
4. **SSH in and run the setup wizard.** Once the droplet is up, SSH in as root. The Discourse setup script launches automatically and prompts you for your domain, admin email, and SMTP credentials. Edit `/var/discourse/containers/app.yml` if you need to tweak anything, then run `./launcher rebuild app`.
5. **Configure transactional email.** Discourse will not function without working SMTP — every password reset, signup confirmation, and notification depends on it. Sign up for Mailgun, SendGrid, Mailjet, or Amazon SES (most have a free tier that covers a small forum), verify your sending domain, and paste the SMTP host, port, username, and password into `app.yml`.
6. **Register your first admin account.** Visit `https://yourdomain.com`, click "Sign Up," and use the email you configured as the admin. The first registered account on a fresh Discourse instance automatically gets full admin rights.
7. **Lock down the basics.** In the admin settings (`/admin`), require approval for new accounts, set your trust level thresholds, enable two-factor authentication for staff, and configure your backup schedule. Daily backups to DigitalOcean Spaces (S3-compatible object storage) cost pennies and are worth setting up on day one.

If you hit a snag, the Discourse Meta community is unusually responsive for an open-source project, and the DigitalOcean community tutorials section has a dedicated Discourse install collection that walks through edge cases like running Discourse on a subdomain or scaling it behind a load balancer with a managed database cluster.

## Common Pitfalls and How to Avoid Them

A short list of the things that bite people most often when they're new to **digital ocean discourse**:

- **Trying to run on 1 GB of RAM.** It's technically possible with swap, but the rebuild step will be painfully slow and the forum will feel sluggish under any real load. Start at 2 GB minimum.
- **Skipping the SMTP setup.** A Discourse forum without working email is half a forum — users can't sign up, can't reset passwords, and won't get notifications. Don't skip this step, and don't try to run your own mail server on the same droplet; use a transactional provider.
- **Forgetting to set up DNS before running the rebuild.** Let's Encrypt needs to verify your domain before it issues an SSL certificate, and that verification fails if your A record isn't pointing at the droplet. Set up DNS first, wait for it to propagate, then rebuild.
- **Not enabling backups.** A Droplet without backups is a single point of failure. Pay the 20-30% surcharge for daily backups, or set up automated exports to DigitalOcean Spaces. Your future self will thank you the first time a bad plugin update takes the forum down.
- **Ignoring updates.** Discourse ships frequent updates with security fixes. Set a monthly calendar reminder to run `./launcher rebuild app` after pulling the latest from the official repo, or subscribe to the Discourse release announcements.

## Who Should Actually Choose This Path

Self-hosting Discourse on DigitalOcean isn't for everyone, and pretending otherwise would be dishonest. It's the right choice if you fit any of these:

- **A developer or technical founder** who wants full control of the stack and is comfortable in a Linux shell
- **A small community or hobby group** where $20-30/month is the right budget and no one needs an SLA
- **A business that already has DevOps capacity** and wants to avoid paying $100-500/month for managed hosting
- **Anyone who wants to learn modern self-hosting** in a forgiving environment, with a generous free credit to absorb the inevitable mistakes

It's the wrong choice if you need a forum live yesterday with zero involvement, if you have no one who can SSH in when something breaks at 2 a.m., or if your community is mission-critical enough that a 99.9% uptime SLA is a contractual requirement. In those cases, the Discourse Pro, Business, or Enterprise plans are the better tool for the job — and there's no shame in paying for peace of mind.

## The Bottom Line on Digital Ocean Discourse

The combination of Discourse's polished software and DigitalOcean's one-click Marketplace app is, in my opinion, the best self-hosted forum stack available right now. The pricing is transparent and predictable, the per-second billing that took effect in 2026 means you can experiment freely without racking up surprise charges, and the $200 new-user credit means you can run a real forum for months before you spend a dollar of your own money. The plugin ecosystem is the same one powering forums for OpenAI, GitLab, Docker, and Elastic — all of which are listed on Discourse's own customer wall — so you're not settling for a lesser product by going the self-hosted route.

If you've been on the fence, there's not much reason to keep researching. Spin up a Droplet, point a domain at it, and you'll have a live Discourse forum before the end of the afternoon. 👉 [Grab the $200 credit and start your Discourse deployment here](https://bit.ly/DigitaLocean) — the worst case is you spend an hour learning something useful, and the best case is you've got a community platform that scales with you for years.
