# Awesome Jev

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](https://github.com/robokrunch/awesome-jev/pulls)

A curated list of resources for **Jev** — TypeSafe AI's "System One" model that returns typed, calibrated decisions instead of text.

Jev doesn't chat. You send it structured state; it returns a probability distribution over your predefined options in 70–500ms, at **$0.042 per million input tokens with output free**. It was built by Diogo Almeida (ex-OpenAI, co-inventor of RLHF/ChatGPT), emerged from stealth in September 2026 with $40M in funding, and is currently in waitlist-only early access.

This list is maintained by [RoboKrunch](https://robokrunch.com) — we benchmark Chinese edge-AI hardware and run our own real-measurement Jev experiments (300 real API calls, real latency, real bills). Entries with our own measured numbers are marked 📊.

> **No official paper.** TypeSafe has not published a formal paper on Jev as of 2026-09-20. If one appears, it goes here first.

## Contents

- [Official](#official)
- [Models & API](#models--api)
- [Projects & Code](#projects--code)
- [Benchmarks & Evaluations](#benchmarks--evaluations)
- [Demos on X](#demos-on-x)
- [Videos](#videos)
- [News & Articles](#news--articles)
- [Papers](#papers)
- [Community](#community)
- [Contributing](#contributing)

## Official

- [typesafe.ai](https://typesafe.ai/) — Official site, waitlist signup.
- [Quickstart](https://docs.typesafe.ai/introduction/quickstart) — Official docs: `POST /v1/systemone`, question types (`choice` up to 255 options, `noul` yes/no, `score`).
- [@typesafeai on X](https://x.com/typesafeai) — Official announcements.
- [Founder launch thread](https://x.com/CompleteSkeptic/status/2099925682726002904) — Diogo Almeida introduces Jev and RLCD.
- [Jev on OpenRouter](https://openrouter.ai/typesafe/jev-1.13) — `typesafe/jev-1.13`, $0.042/M input, $0/M output, 32K context. Note: OpenRouter exposes Jev via its decisions endpoint, not `/chat/completions`.
- [Jev on Cloudflare](https://developers.cloudflare.com/ai/models/typesafe/jev/) — Workers AI model page.
- [$40M stealth emergence (Business Wire)](https://www.businesswire.com/news/home/20260915525333/en/TypeSafe-AI-Emerges-From-Stealth-With-%2440M-in-Funding-With-New-Model-for-Composable-AI) — Funding announcement, September 2026.

## Models & API

- **Native API**: `POST https://api.typesafe.ai/v1/systemone` — structured DOM/state in, `{operation, target}` style typed decisions out. One call can carry many questions in parallel.
- **Question primitives**: `choice` (pick from up to 255 options, per-option probabilities + `confidence`), `noul` (yes/no as a single probability), `score` (numerical).
- **Pricing**: $0.042 / 1M input tokens, $0 output. No tokens wasted on explanations.
- **Latency envelope**: TypeSafe quotes 70–500ms; independent testers measure p50 ~0.2–0.8s depending on task and route (see [Benchmarks](#benchmarks--evaluations)).

## Projects & Code

Browser agents, mobile agents, guardrails — the fastest-moving category. Starred entries have verified repos.

- [browser-use/jev-ultrafast](https://github.com/browser-use/jev-ultrafast) — Browser agent with a dynamic indexed action space. Jev picks the operation + element; a small LLM writes text only for `TYPE_TEXT`. Zürich → London on Google Flights in ~7.1s.
- [droidrun/mobile-jev](https://github.com/droidrun/mobile-jev) — Mobile agent for Mobilerun: Jev drives a real Android phone, opens Uber, enters SFO → Golden Gate Bridge route, reaches payment selection. 9 actions in ~21s (no booking completed).
- [ClemensSchartmueller/jev-guard](https://github.com/ClemensSchartmueller/jev-guard) — High-speed safety gate for Claude Code, Codex CLI, and Antigravity. Intercepts tool calls, does sub-ms local checks, then asks Jev to score blast radius / reversibility / destructive potential.
- [jkudish/jev-browser](https://github.com/jkudish/jev-browser) — Browser automation with Jev.
- [jkudish/jev-mcp](https://github.com/jkudish/jev-mcp) — MCP server for Jev decisions.
- [SadiqOnGithub/jev-lab](https://github.com/SadiqOnGithub/jev-lab) — Experiments playground.
- [tumf/jev-cli](https://github.com/tumf/jev-cli) — CLI for Jev.
- [steventsao/jevzen](https://github.com/steventsao/jevzen) — Jev experiments.
- [zhuyansen/jev-search-rerank-eval](https://github.com/zhuyansen/jev-search-rerank-eval) — Search rerank evaluation with Jev.
- [nekuda-ai/WindTunnel](https://github.com/nekuda-ai/WindTunnel) — Agent testing harness with Jev.

**Spotted in the wild** (seen on X / in roundups, repo link wanted — PRs welcome):

- `jevmeter` — scores every sentence of a debate for ~$0.05.
- `Typewriter` (Steve Krouse) — updates 16 judgments live as you type.
- `heist-one` — Jev runs the guards in a heist game; also completed StarCraft's first combat mission.
- `pg-jev` — plain-language filters for Postgres.
- `HA-Jev` — turns Jev answers into Home Assistant entities.
- `sift` — Chrome extension labeling every X timeline post (substance, humour, promo, junk, AI-written) with Jev decisions.
- `jev-block-android-ad` — Android notification/SMS noise gate; fails open on OTP codes.
- `jev-cvss` — CVE description → CVSS v3.1 metric predictions.

## Benchmarks & Evaluations

Numbers with sources. Vendor claims are labeled as such.

- 📊 [RoboKrunch: 10,000-robot fleet triage](https://github.com/robokrunch/jev-physical-ai) — 300 real decisions API calls against a simulated warehouse AMR fleet (bilingual CN/EN incidents), 3 simultaneous judgments per call (human escalation, owning team, urgency 0–2). **300/300 succeeded, p50 0.53s, p95 0.81s, $0.0000246/decision ($24.57 per million)**. Fleet-scale model (10K robots × 48 decisions/day × 30d): **$354/mo vs $1,814/mo for GPT-4o-mini — 5.1×**. Caveats: incidents are simulated (template agreement 91.3%, not production accuracy); GPT-4o-mini cost is estimated, not measured.
- 📊 [RoboKrunch: Jev vs self-hosted ModernBERT](https://github.com/robokrunch/jev-physical-ai) — `ModernBERT-base` (149M params) on a 2-core CPU: p50 169ms vs Jev's 527ms (~3× faster) but one label vs three judgments. Infra crossover ≈ **977K decisions/month** (~678 robots at 48/day) before self-hosting beats Jev on cost. Conclusion: Jev's edge is zero training, zero labeling, zero ops — not raw speed.
- [Aman Kumar: "Testing Jev on public and private data — classifier or filter?"](https://amankumar.ai/blogs/jev-measured) — ~16,000 real calls over two days. On short-text classification, level with or ahead of gpt-5.4-mini and gpt-5.6-luna on 3 of 4 public sets, at **5–56× lower cost, median under a second**. Probabilities hold near 0 and 1 (confident answers right 90–100%); the middle is a coin flip. Verdict: *"a filter, not a replacement"* — Jev takes the calls it's confident about, the rest fall back to your existing model.
- [TypeSafe's own workflow evals (via MarkTechPost)](https://www.marktechpost.com/2026/09/19/typesafe-ai-releases-jev/) — Jev 0.114s / $0.000081 vs GPT-5.6 Terra 8.566s / $0.013880 (claimed 193.6× faster, 444.6× cheaper). Fine print: reference answer is the average of GPT-6 Astra and Fable 5.1; TypeSafe's own team wrote the workflows; TypeSafe says these gains sit at the high end and it cannot prove the price is unsubsidized.
- [Vercel: command safety](https://www.marktechpost.com/2026/09/19/typesafe-ai-releases-jev/) — Guillermo Rauch reported Jev up to **18× faster at p95** than GPT Luna for command-safety review, and more accurate (via engineer Pranit Sharma's benchmark).
- [Bryo AI: email triage](https://www.marktechpost.com/2026/09/19/typesafe-ai-releases-jev/) — CTO Nikhil Mudholkar found Gemini slightly more accurate but **10–20× more expensive** than Jev.

## Demos on X

Single-post links rot fast; handles + what they showed. PRs with direct links welcome.

- **@gregpr07** — Browser Use + Jev, Google Flights in ~7s.
- **@CompleteSkeptic** — Jev plays Doom.
- **@faadilhshaik** — Super Mario Bros with Jev.
- **@nutlope** — 1,018 AI papers classified for **$0.08**.
- **@iannuttall** — 3,282 X posts, 4.25M tokens, **$0.1282**.
- **@TheMattBerman** — 724 ads analyzed in 40s for **$0.09**.
- **@rileybrown** — 500 emails triaged for **$0.035**.
- **@tdinh_me** — YouTube sponsor-skip browser extension.
- **@0xidanlevin** — WebMCP benchmark with Jev.
- **@Steve8708** — "Jev is awesome but for the love of god please STOP posting fake demos" — the thread that forced the fake-demo vs real-measurement debate (148K views).

## Videos

- [Jev launch demo (TypeSafe)](https://www.youtube.com/watch?v=7aAq5J64K34)
- [Jev deep dive](https://www.youtube.com/watch?v=NttqTDRbGUc)
- [Jev build walkthrough](https://www.youtube.com/watch?v=X117w2Rark8)
- [Community Jev demo roundup](https://www.youtube.com/watch?v=LU6K07U4wOk)
- 📊 [RoboKrunch: 300 real Jev decisions on a simulated 10K-robot fleet](https://github.com/robokrunch/jev-physical-ai) — 60s, watch the cost ticker.

## News & Articles

- [MarkTechPost: "TypeSafe AI Releases Jev" (2026-09-19)](https://www.marktechpost.com/2026/09/19/typesafe-ai-releases-jev/) — Best single roundup: pricing fine print, community projects, Vercel/Bryo/Droidrun data points.
- [The Rundown: "TypeSafe Jev AI decisions software"](https://www.therundown.ai/news/typesafe-jev-ai-decisions-software)
- [RuntimeWire: "TypeSafe Jev System One AI model early access"](https://runtimewire.com/article/typesafe-jev-system-one-ai-model-early-access)
- [The AI Insider: "TypeSafe AI emerges from stealth with $40M" (2026-09-17)](https://theaiinsider.tech/2026/09/17/typesafe-ai-emerges-from-stealth-with-40m-to-build-machine-native-ai-models/)
- [ByteIota: "TypeSafe Jev Launches: ChatGPT Pioneer's Non-LLM AI Model"](https://byteiota.com/typesafe-jev-launches-chatgpt-pioneers-non-llm-ai-model/)
- [Latent Space: "Jev: a System One Model" (AI News)](https://www.latent.space/p/ainews-jev-a-system-one-model-that)
- [OctoMind: "Jev System One model for AI agents"](https://octomind.run/blog/jev-system-one-model-ai-agents)
- [DataCamp: "System One models: Jev"](https://www.datacamp.com/blog/system-one-models-jev)
- [dev.to: "Jev, the ChatGPT co-creator's System One model can't talk"](https://dev.to/lukeocodes/jev-the-chatgpt-co-creators-system-one-model-cant-talk-3774)
- [dev.to: "How to use Jev: a practical guide"](https://dev.to/valyuai/how-to-use-jev-a-practical-guide-to-typesafes-system-one-model-g5e)
- [ThursdAI (2026-09-17)](https://sub.thursdai.news/p/typesafes-jev-changes-everything) — "A ChatGPT moment for decisions."

## Papers

None. TypeSafe has not published a formal paper on Jev or RLCD as of 2026-09-20. This section will not be padded with loosely-related arXiv links — when the paper drops, it goes here.

## Community

- [HN: Jev launch discussion](https://news.ycombinator.com/item?id=49761730)
- [HN: Jev technical discussion](https://news.ycombinator.com/item?id=49762040)
- [madewithjev.com](https://madewithjev.com/) — Demo hub.

## Contributing

PRs welcome. Rules:

1. One line of description per entry, with a real link. No link, no merge (post it in the issue first and we'll find it).
2. Numbers need sources. "10× faster" with no methodology gets cut.
3. No affiliate links, no waitlist-farming, no fake demos — the [@Steve8708 rule](https://x.com/typesafeai): if the demo isn't real, it doesn't belong here.

## License

This list is released under [CC0 1.0](LICENSE) — do whatever you want with it.

---

Maintained by [RoboKrunch](https://robokrunch.com) — we benchmark Chinese edge-AI hardware and measure what AI actually costs in the physical world. Our Jev experiments live at [robokrunch/jev-physical-ai](https://github.com/robokrunch/jev-physical-ai).
