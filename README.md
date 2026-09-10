<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:1a1b27,45:414868,100:7aa2f7&height=140&section=header" width="100%"/>

# Shun Ikeda

**Security research · Product engineering · Building and leading teams**

Computer science student at Yokohama National University<br/>
Research intern at NTT DATA Institute of Management Consulting

In production: [tabito.site](https://tabito.site) · [cocodoco.jp](https://cocodoco.jp)

</div>

---

## About

<img src="img/senzai-face.PNG" width="230" align="right" alt="Shun Ikeda"/>

Roughly half of my time goes to **security research** at university — IoT malware, honeypots, and what newly disclosed vulnerabilities look like once someone actually attacks them. The other half goes to **building and running products**: a travel platform live in production, a regional-tourism service I founded, and the internal platform for my university's programming circle, where I'm an org owner.

The two halves are closer than they look. Both start with something nobody has a clear picture of yet — an unknown binary phoning home, or a town that gets no visitors — and both end in the same place: observe it properly, work out what is actually going on, then turn that into something that runs. A reproducible analysis setup, or a service people use.

The part I keep gravitating towards is the seam between the technical detail and the decision: what to build, in what order, and with whom.

<br clear="right"/>

---

## Selected work

### Tabito — inbound travel platform

**[tabito.site](https://tabito.site)** (live) · **[tabito-travel-app](https://github.com/Tivo0921/tabito-travel-app)** · `Next.js 16` `React 19` `TypeScript` `Supabase` `Stripe`

**Problem.** Visitors to Japan rarely reach the places locals actually go, and the locals who could take them there have no way to reach visitors.

**My role.** Requirements, data model, and the full implementation — I am the sole committer on the repository, and the product is co-owned with one collaborator. It runs in production in Japanese, English and Korean.

**What's inside**, and what I'd point a reviewer at:

- **Authorisation lives in the database, not in `if` statements.** Row Level Security on all 27 tables, 81 policies. "You cannot open this chat unless you bought this package, the purchase completed, and the other party really is that package's guide" is a `WITH CHECK` clause, not application code — so adding an API route later can't quietly open a hole. Recursive checks are pushed into `SECURITY DEFINER` functions in a private schema to keep policies from re-entering each other.
- **Payments written for a webhook that fires twice.** Stripe delivers the same event more than once, so purchases are upserted on `stripe_session_id` as the conflict key. Replays are inert.
- **Translation split into two problems.** UI strings are typed against the Japanese dictionary as the source of truth, so a missing key fails `tsc` rather than rendering blank. User-written content stays in the language it was written in, with paired `*_translations` tables only where translation is genuinely wanted — machine-translating a guide's voice would defeat the point of the product.
- **A release path that assumes I'll break something.** Feature branch → PR → a `staging` domain that stays at one URL for testers → real-device testing → `main`.

Roughly 2,700 lines of SQL across the migrations, 32 pages, three locales.

---

### Cybersecurity research

Research at my university lab, on how attacks behave in the real world rather than in the abstract.

**Focus**

- Dynamic analysis of **IoT malware**
- **High-interaction honeypots** and observation of live attack traffic
- **Active cyber defence**
- **Newly disclosed vulnerabilities** — reproducing them and watching how they are exploited in practice

**The habit it builds.** Most of it starts unlabelled: a sample, a connection, a scan against a host that has no business being interesting. The work is to build a place where that can be run safely and watched closely, keep evidence that still means something a week later, and get from *"something happened"* to a description precise enough for somebody else to reproduce.

That is the same move I make on the product side, which is why I don't treat these as two careers.

> This work happens inside a lab and its repositories are not public, so what's written here is scope, not results. Public code in the same spirit is under [Workshop notes](#workshop-notes) below.

---

### COCODOCO — travel by deduction

**[cocodoco.jp](https://cocodoco.jp)** · **[cocodoco-jp](https://github.com/cocodoco-jp)** · founder

**Problem.** Tourism in Japan piles onto the same handful of landmarks while everywhere else gets nobody — overtourism and under-visited regions are one problem seen from two ends.

**The idea.** You're handed a photograph, you work out where it was taken, and then you actually go there, as a game, with friends. It aims foot traffic at streets that ordinary tourism skips entirely.

**My role.** Founder, and I run the product side: the concept, how an event is shaped, what gets built next. Events have run in Asakusa, Minatomirai, and on the Yokohama National University campus during its festival; a collaboration with the manga *りゅうとあまがみ* is scheduled in Niigata City.

Three products ship under the org — a **web service** for browsing and joining events, a **Dart/Flutter admin app** for running them, and a **LINE bot**, because that is where participants already are. I work in the LINE bot codebase alongside the product work. *(Repositories are private.)*

---

### Lumos-Programming — org owner

**[Lumos-Programming](https://github.com/Lumos-Programming)** · **[lumos-web](https://github.com/Lumos-Programming/lumos-web)** · `Next.js` `NextAuth v5` `Firestore` `Cloud Run`

The programming circle at Yokohama National University. I hold owner rights on the organisation and work on the internal platform the circle runs on — member profiles, Discord-authenticated members' area, and the tooling that keeps a student org from being run out of a spreadsheet.

**15 merged PRs across the org**, most of them on the operations side rather than the shiny side:

- the admin **member-management dashboard** and its filtering
- **Discord role synchronisation** — narrowing it to year and faculty, branching per role type, handling alumni, and fixing a diff bug that deleted roles the sync didn't own
- news publishing from the web UI, plus `CODEOWNERS` and docs fixes so new members can find who to ask

---

## Engineering

What I can actually build, and where to check.

| | Stack | Evidence |
|---|---|---|
| **Production web apps** — auth, payments, multi-language content, realtime | TypeScript, Next.js (App Router), React 19, Tailwind | [Tabito](https://github.com/Tivo0921/tabito-travel-app), [lumos-web](https://github.com/Lumos-Programming/lumos-web), [shinkan-search](https://github.com/Tivo0921/shinkan-search) |
| **Schema design and DB-level authorisation** | PostgreSQL, Supabase, Row Level Security, SQL migrations | Tabito — 27 tables, 81 policies |
| **Payments that survive retries** | Stripe Checkout, webhooks, idempotent writes | Tabito |
| **Services other people can run** | Docker / Compose, GitHub Actions, Vercel, Cloud Run | [ymon-web](https://github.com/Tivo0921/ymon-web), Tabito, lumos-web |
| **Analysis and evaluation tooling** | Python, Linux, shell | [audit harness](https://github.com/Tivo0921/owasp-benchmark-audit-organizer), security research |
| **Systems and fundamentals** | C, Java | [MyOS](https://github.com/Tivo0921/MyOS), [projectC](https://github.com/Tivo0921/projectC) |

---

## Workshop notes

**Not original research** — noted here only because the code is public.

For a workshop I re-ran an existing experimental design on a different dataset: **can an AI coding agent judge, from source alone, whether a piece of code is genuinely vulnerable?** The design comes from CVE-Bench. I swapped in [OWASP BenchmarkJava](https://owasp.org/www-project-benchmark/), which pairs real vulnerabilities with deliberate look-alikes that turn out to be safe, and varied how common vulnerabilities were in the bundle ([10 %](https://github.com/Tivo0921/owasp-benchmark-java-audit-vuln10) / [90 %](https://github.com/Tivo0921/owasp-benchmark-java-audit-vuln90)) to see what that does to an agent's judgement. The scripts that select cases, build the bundles and grade the JSON reports are [mine](https://github.com/Tivo0921/owasp-benchmark-audit-organizer); the research question and the evaluation framing are not.

> Wang et al. (2025). *CVE-Bench: Benchmarking LLM-based Software Engineering Agent's Ability to Repair Real-World CVE Vulnerabilities.* NAACL 2025. https://aclanthology.org/2025.naacl-long.212/

---

## Also on GitHub

- **[ymon-web](https://github.com/Tivo0921/ymon-web)** — 3v3 turn-based battler. Idempotent, race-safe matchmaking, SPD-ordered turn resolution, Dockerised so the whole team gets one environment.
- **[shinkan-search](https://github.com/Tivo0921/shinkan-search)** — club-search app for new students, Next.js App Router over Postgres.
- **[MyOS](https://github.com/Tivo0921/MyOS)** · **[projectC](https://github.com/Tivo0921/projectC)** — an OS written from scratch following *ゼロからのOS自作入門*, and a sugoroku game in C.
- **[pull-shark-farm](https://github.com/Tivo0921/pull-shark-farm)** — a joke, executed with unreasonable rigour: shell automation for the GitHub Pull Shark achievement. Side project, not a portfolio piece.

<div align="center">

<sub><code>Tivo</code> is <code>Shun</code> shifted forward by one letter. The only cipher on this profile, and everyone who has tried has broken it.</sub>

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:7aa2f7,55:414868,100:1a1b27&height=120&section=footer" width="100%"/>

</div>
