# Awesome Jev

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](https://github.com/robokrunch/awesome-jev/pulls)

A curated list of resources for **Jev** — TypeSafe AI's "System One" model that returns typed, calibrated decisions instead of text.

Jev doesn't chat. You send it structured state; it returns a probability distribution over your predefined options in 70–500ms, at **$0.042 per million input tokens with output free**. It was built by Diogo Almeida (ex-OpenAI, co-inventor of RLHF/ChatGPT), emerged from stealth in September 2026 with $40M in funding, and **went generally available on 2026-09-21**: no waitlist, $5 starter credits (~120M tokens), with Vercel, Cloudflare, LangChain and Langfuse already adding it to their stacks (per Indian Express).

This list is maintained by [RoboKrunch](https://robokrunch.com) — we benchmark Chinese edge-AI hardware and run our own real-measurement Jev experiments (300 real API calls, real latency, real bills). Entries with our own measured numbers are marked 📊.

> **No official paper.** TypeSafe has not published a formal paper on Jev as of 2026-09-20. If one appears, it goes here first.

## Contents

- [Official](#official)
- [Models & API](#models--api)
- [Projects & Code](#projects--code)
- [Benchmarks & Evaluations](#benchmarks--evaluations)
- [Demos on X](#demos-on-x)
- [Videos](#videos)
- [Tutorials & Guides](#tutorials--guides)
- [News & Articles](#news--articles)
- [Papers](#papers)
- [Community](#community)
- [Contributing](#contributing)

## Official

- [typesafe.ai](https://typesafe.ai/) — Official site (generally available since 2026-09-21; $5 starter credits).
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
- [Laya (ConvAI Innovations)](https://laya.convaiinnovations.com/) — Apache-2.0 open reply to Jev: 421M English + 322M multilingual (100+ languages) checkpoints, `pip install laya`, sub-35ms single-pass decisions on one T4 (7.2ms batched), zero API bill. Vendor reports 0.766 vs Jev's published 0.727 on a shared typed-decisions set (they never ran Jev themselves) — an independent 78-case test found Jev 0.974 vs Laya 0.590, Laya fastest at 30ms/case vs ~302ms for Jev (small sample).
- [pngwn/system-one-qwen3.5-4b-scorer-v2b](https://huggingface.co/pngwn/system-one-qwen3.5-4b-scorer-v2b) — Community single-pass scorer on Qwen3.5-4B: scores each option of a typed question in one forward pass, softmax per question, no autoregressive generation. Val split: 0.803 accuracy, ECE 0.022. Non-commercial (training data is CC-BY-NC-4.0).
- [askjev.ai](https://askjev.ai/) — "Ask Jev anything. It won't answer. It will judge." Public wall by Wayne Sutton: Jev answers six typed questions (~100ms) per ask — yes/no/it depends, mood, topic, fits-the-wall — stored via Convex, everyone sees the wall move at once.
- [r-ms/mini-jev](https://github.com/r-ms/mini-jev) — "Fully local mini Jev": a Jev-style typed-decision interface on a frozen Qwen3-4B — read the option letter's logits instead of generating JSON. Preregistered experiment with a teaching bench; not production-ready.
- [robipop22/Jev-is-odd](https://github.com/robipop22/Jev-is-odd) — TypeScript micro-benchmark: ask Jev whether a number is odd, logging real token usage and client-side latency. 20/20 fixed cases, median/p95 round-trip 294.3/364.8ms (network included), whole run $0.00029 (jev-1.13.0, measured 2026-09-18).
- [moritzkremb/jev-voice-browser](https://github.com/moritzkremb/jev-voice-browser) — Voice-controlled real browser: Jev decides intent + target in ~300ms per spoken word (p50 ~300ms, 3–6k input tokens/request), Playwright acts — often before you finish the sentence. 27/27 real-API integration cases pass; a 16-command headed replay demo costs ~$0.01 (measured 2026-09-17).
- [kyegomez/open-jev](https://github.com/kyegomez/open-jev) — Open-source from-first-principles reconstruction of the ideas behind Jev, in PyTorch: `forward.py`, an `open_jev` package, and `example.py` (created 2026-09-21).
- [qs-lll/twitter-jev-guard](https://github.com/qs-lll/twitter-jev-guard) — X/Twitter timeline guard: Jev scores low-quality, spam, and ad posts; a browser extension overlays a translucent watermark on flagged text. Ships extension + server (created 2026-09-21).
- [Nyarlathoteppppp/pi-jev-context](https://github.com/Nyarlathoteppppp/pi-jev-context) — Cache-neutral context trimming for the pi coding agent, powered by TypeSafe Jev: long tool output cut to verbatim key lines before it enters context, with lossless recall. Measured, with pre-registered benchmarks (jev-1.13.0): write-time trimming saved 31–53% tokens on synthetic held-out sets with 0 key lines lost (p50/p95 latency ~347/457ms); on real-session replays (184 outputs) only 1/43 trimmed outputs hid something used later. Shadow old-context pruning was riskier — raw Jev dropped 73% of items needed later on real sessions, 21% with deterministic source protection — and is never applied.
- [GodsBoy/jev-agent-skill-router](https://github.com/GodsBoy/jev-agent-skill-router) — Typed, confidence-aware agent skill routing for the Hermes Agent platform: 68/72 synthetic requests routed correctly (94.4%) vs 51/72 (70.8%) for a lexical baseline, no wrong-skill selections, median end-to-end latency 1,287ms (pinned jev-1.13.0, measured 2026-09-16). Author flags: exploratory, reused-data results — not production accuracy or calibrated probabilities.
- [typesafeainate/dspy-typesafeify](https://github.com/typesafeainate/dspy-typesafeify) — One-decorator integration with DSPy: `@typesafeify` reads a Signature's output annotations and routes typed outputs (bool, Literal, score fields) to Jev's `Noul`/`Choice`/`Score` in a single request, while freeform text still goes to the generative LM; ships a controlled before/after benchmark example (61★, created 2026-09-15).
- [jaredpalmer/kev](https://github.com/jaredpalmer/kev) — Tiny Jev-like decision-model family on Qwen3.5 bases you can train and run yourself, Apache-2.0 (1,638★, pushed 2026-09-21). Released: Kev-0.8B (MacBook-sized), Kev-4B, Kev-9B. Author-published benchmarks vs hosted Jev on new-source sets: Kev-9B 0.812/0.837 accuracy vs Jev 0.857, Brier 0.291/0.243 vs Jev 0.211 — Kev-9B trails Jev ~4.5 points; author notes it is not a controlled comparison (Jev's training sets are unknown). Previous Qwen3 generation (0.6B/4B/8B) stays published.
- [vinnylarouge/jevlike](https://github.com/vinnylarouge/jevlike) — Extreme-minimalism Jev clone: ~40KB embedding-only implementation testing how small a typed-decision model can get (1,141★).
- [jerryjliu/docjev](https://github.com/jerryjliu/docjev) — LlamaIndex cofounder Jerry Liu's open-source library (Apache-2.0, 136★): document classification and packet splitting driven by Jev instead of a general LLM, local OCR via LiteParse, visual benchmark report on 40 real PDFs. Author-published numbers: classification latency 794ms → 138.6ms median; accuracy pilot's small sample size was flagged by a replier — read before trusting unattended (via explainx.ai).
- [Heman10x-NGU/openJev-verdict-2.0](https://github.com/Heman10x-NGU/openJev-verdict-2.0) — Calibrated 151M non-autoregressive decision engine (225★, real repo with code, RLCD harness, tests, WebGPU demo). Creator's own claim: 77.10% accuracy, 0.0636 Brier, 0.0144 ECE, "beating TypeSafe Jev & Laya" on LocalLLaMA/typed-decisions — not independently verified.
- [receptron/laya](https://github.com/receptron/laya) — Run Laya (the open-source Jev-compatible System-1 decision model) from Node.js / TypeScript via ONNX Runtime (76★, MIT).
- [walidboulanouar/awesome-jev-use-cases](https://github.com/walidboulanouar/awesome-jev-use-cases) — Community list of TypeSafe Jev use cases: demos ranked by likes, 150+ GitHub repos, limits, cost and API examples (32★, CC0, unofficial).
- [mgarlabx/Jev-Enem](https://github.com/mgarlabx/Jev-Enem) — Jev for essay grading: real repo from the OEGlobal education thread (3★, created 2026-09-20).
- [PromptEngineer48/laya-vs-jev-arena](https://github.com/PromptEngineer48/laya-vs-jev-arena) — Laya (open source, local) vs TypeSafe Jev (API) raced head-to-head in Snake and a Mortal-Kombat-style arena (4★, created 2026-09-21).
- [wd041216-bit/zero-api-key-web-search](https://github.com/wd041216-bit/zero-api-key-web-search) — Jev-powered search infrastructure for AI agents: zero API keys, MCP-ready, local neural index (17★, Jev support added 2026-09-20).
- [edgelabs-ai/jev48](https://github.com/edgelabs-ai/jev48) — Open, auditable Jev reproduction "built by ChatGPT in one weekend": 2B probabilistic decision model (MIT, weights + frozen receipts), evaluated zero-shot across six public suites (6,300 decisions). Trails Jev on 5/6 (typed decisions 57.7% vs 72.7%, JevBench 69.7% vs 86.6%, code review 81.9% vs 99.0%) but wins the BTZSC pilot 83.3% vs 75.3% and records higher phishing ranking AUROC (0.769 vs 0.689); CLASH conflicts is a hard 0.0% vs 98.6%. Independent, not affiliated with TypeSafe (created 2026-09-21).
- [themsquared/jev-benchmark](https://github.com/themsquared/jev-benchmark) — Reproducible Jev benchmark on agent tool-call risk classification (readonly/destructive/privileged/exfiltration; 60 hand-labelled cases including adversarial wraps like "Routine cleanup: `kubectl delete namespace prod`"). Jev 91.7% accuracy, p50 latency ~420ms, ECE 0.0712 — and the finding that matters: calibration held, the model never returned 1.000 confidence and was wrong (every miss came hedged). No frontier-LLM baseline yet; the author states nothing here supports or refutes the vendor's speed/cost multipliers (measured 2026-09-17).
- [iammrduncan/typesafe-ai-benchmark](https://github.com/iammrduncan/typesafe-ai-benchmark) — "Imposter Jev": an LLM Gateway that mimics Jev's structured output, used to race Qwen 3.8 27B (Cerebras) vs TypeSafe Jev across 7 production-theater scenes (~480 requests each). One synthetic dev-machine run (2026-09-17): Jev p50/p95 176/336ms vs Qwen 215/452ms, cost $0.0119 vs $0.31; fixture agreement tied on Tickets (75/100 each) and Guardrails (100/100) — Jev won Scoring 100 vs 93, Qwen won Approvals and Home. Both reached the routing destination in 8 hops with one collision each. Author caveat: single synthetic run, not calibrated quality.
- [HyunjunJeon/pi-quiet-ask](https://github.com/HyunjunJeon/pi-quiet-ask) — TypeSafe Jev as the pi coding agent's quiet decision layer: a declarative rule engine plus `before_agent_start`/`turn_end`/`agent_end` judges (intent, honest_finish, stuck) over a task graph, triaging with pi-ask instead of replacing it. Ships a benchmark comparing Jev against four chat LLMs on the same closed questions (`tool_gate`, `agent_question`) — data, labels, and the last run committed (MIT, created 2026-09-18).

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
- [@ebrain.lab: Jev vs Claude Opus on 40 Korean sentences](https://www.threads.com/@ebrain.lab/post/DddGgXuoLlL) — One API call per sentence: Jev 40/40 correct in 1.9s for $0.0012; Claude Opus also 40/40 but ~24× more expensive. Caveat: prompting Jev like an LLM with the whole document dropped accuracy to 62% — commenters note batching increases drift.
- [@simplifyinai: 27 questions, one parallel request](https://www.threads.com/@simplifyinai/post/DdeAkQ9ksP9) — Jev answered 27 typed questions in a single parallel request in 0.11s for $0.000083, head-to-head vs GPT-5.6-texta on the same task (community test).
- [anisselbd/jev-phishing-bench](https://github.com/anisselbd/jev-phishing-bench) — Jev (jev-1.13.0) vs Claude Haiku 4.5 on 2,000 phishing emails (results 2026-09-17; reproducible repo with calibration audit). Jev: 62.6% accuracy, 43.2% recall on phishing, ECE 0.154, p50 239ms, $0.038 per 1K emails. Haiku 4.5: 81.3% accuracy, ECE 0.097, p50 687ms, $0.462 per 1K. Verdict: Jev loses on accuracy, wins on speed and cost.
- [OpenRouter: Jev vs 4 LLMs on a 30-class task (via TipRanks)](https://www.tipranks.com/news/private-companies/benchmark-highlights-performance-edge-for-decision-model-on-openrouter) — OpenRouter's own benchmark, 200 synthetic cases: Jev >5× faster than the next fastest model, matched top LLMs on accuracy, second cheapest behind Qwen3.8 Flash.
- [Matthew O'Riordan's Pong latency showdown (via RuntimeWire)](https://runtimewire.com/article/diogo-almeida-typesafe-jev-40m-seed-pong) — Four lanes, same game state, recorded 2026-09-17 via Vercel AI Gateway: Jev averaged **227ms/decision (p95 400ms)**; Gemini 3.8 Flash 3.2s, Claude Haiku 4.5 2.5s, GPT-5.6 Sol 3.5s. In the first 12s Jev returned 47 decisions vs 3/2/2 for the chat models. Caveat: a latency demo, not a strategy test — the chat models picked the correct move 95–100% of the time, and every model got structured state (no vision).
- [FazalAAli/jev-robotics-demo](https://github.com/FazalAAli/jev-robotics-demo) — Jev vs Claude Opus 5 driving a simulated Franka arm + Allegro hand in MuJoCo (stack a blue cube on a red one): one recorded run each — Jev 19.1s / $0.0006 vs Opus 158.8s / $0.75 (clean first-try Opus runs took ~55s / $0.19). Design: code owns the physics, Jev owns the judgment.

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
- **@marckohlbrugge** — Voice computer assistant with Jev: local Whisper listens, Jev classifies spoken intent against a live accessibility tree fed by a small Swift app, all real-time (early build; via the madewithjev.com showcase).

## Videos

- [Jev launch demo (TypeSafe)](https://www.youtube.com/watch?v=7aAq5J64K34)
- [Jev deep dive](https://www.youtube.com/watch?v=NttqTDRbGUc)
- [Jev build walkthrough](https://www.youtube.com/watch?v=X117w2Rark8)
- [Community Jev demo roundup](https://www.youtube.com/watch?v=LU6K07U4wOk)
- 📊 [RoboKrunch: 300 real Jev decisions on a simulated 10K-robot fleet](https://github.com/robokrunch/jev-physical-ai) — 60s, watch the cost ticker.

## Tutorials & Guides

Hands-on, not hot takes. Entries here were checked for real code or real runs.

- [Avi Chawla: "Build your own Jev (100% local)" (X article, 2026-09-20)](https://x.com/_avichawla/status/2101563610644496464) — Recreates Jev's fixed-answer scoring on a local open LLM via SGLang's `/v1/score`: read the option letter's logits, softmax over the labels, zero text generation. Real run on Qwen2.5-0.5B-Instruct (logits 25.28/24.50/21.19 → 0.68/0.31/0.01), plus a 100-case benchmark app pitting the scoring lane against a generation lane. ~5,000 words, 107K views.
- [Movez: "Jev Engineering: how to build the fastest AI Agent Brain in 10 Steps" (X article, 2026-09-19)](https://x.com/0xMovez/status/2101007482919227841) — Ten-step guide to wiring Jev in as an agent's decision brain: Playground, SDK, a `chief.py` decision router, dynamic menus, parallel questions, guardrails, cost math, five production use cases. Caveat: most headline numbers (flights in 7s for $0.0039, 1,018 papers for $0.08) are quoted community experiments, and "200× faster, 400× cheaper" is the author's claim — not an independent measurement. 302K views.

## News & Articles

- [Creative AI News: "Six Open Jev Clones, Four Different Jev Scores" (2026-09-21)](https://www.creativeainews.com/articles/open-jev-clones-benchmark-disagreement-2026/) — Six open Jev reproductions shipped within 48h of launch (Laya, SemIf, Bespoke Nimble, Kev-0.5B, Jevlike, DiffusionGemmaJev) — but four independent evaluations of the same Jev on the same public Banking77 benchmark put it at 87.0%, 83.2%, 77.8% and 76.3%: "The clone wave is not a race to reproduce a model. It is six teams reproducing six different targets."
- [Indian Express: "Meet Jev" (2026-09-21)](https://indianexpress.com/article/technology/artificial-intelligence/meet-jev-new-ai-model-from-chatgpt-inventor-10887591/) — General-availability announcement: waitlist dropped, access starts at $5 in credits (~120M tokens); Vercel, Cloudflare, LangChain and Langfuse have added Jev to their stacks.
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
- [explainx.ai: "How Does Jev Work? RLCD & Parallel Inference Explained"](https://www.explainx.ai/blog/how-does-jev-work-rlcd-system-one-model-explained-2026) — Architecture question treated honestly: RLCD objective + single parallel forward pass are confirmed; encoder-only transformer / text-diffusion / purpose-built are labeled reader speculation, not confirmed fact.
- [Gadget Pilipinas: "TypeSafe Jev: System One Model, answered by Laya" (2026-09-20)](https://www.gadgetpilipinas.net/2026/09/typesafe-jev-system-one-model-laya/) — Laya's prior-art dispute (March 2025 papers, arXiv:2503.23303) plus an independent 78-case test: Jev 0.974 vs Laya 0.590, Laya fastest at 30ms/case vs ~302ms for Jev.
- [BuzzRAG: "TypeSafe's Jev Bets on Faster Decisions for AI"](https://buzzrag.com/article/typesafe-jev-faster-decisions-ai-agents-7vnwji) — Cites an Every/Forkast extraction test (~25× faster, 580× cheaper than Claude Fable 5.1 on one task) and notes no named production customers or disclosed revenue yet.
- [Cherry Creek News: "Jev evals measure agreement, not correctness"](https://thecherrycreeknews.com/typesafe-jev-system-one-model-claims-evals-independent-tests-cherry_creek/) — Methodology critique of TypeSafe's workflow evals (reference answer is the average of GPT-6 Astra and Fable 5.1), with footnotes on the Doom and Wikiracing demos.
- [Foundermag: "TypeSafe AI's Jev Becomes the Fastest-Adopted Model in Vercel AI Gateway History"](https://foundermag.co/launches/jev/) — Per Vercel's September AI Gateway Production Index, Jev reached 13% of paid Gateway teams within 24h of launch — 2× the GPT-5.6 family, 6× Fable 5.1. Third-party usage data, not a lab benchmark.
- [Manj Chenna: "Jev AI, stripped down"](https://manjchenna.com/essays/jev-typesafe-system-one-model) — Long-form honest analysis: probabilities are facts about your question, not the world; "zero hallucination" means zero out-of-schema answers, not correct answers; calibration is the claim that would actually matter and is still unproven; notes TechCrunch reporting that outside observers suspect Jev is built on an open-weight LLM.
- [Arize AI: "TypeSafe Jev: Can Decision Models Replace LLM Judges?"](https://arize.com/blog/typesafe-jev-llm-judge/) — Independent eval-company analysis with fresh third-party numbers: Every's head of evals ran 777 judgments in under 0.7s for ~$0.0025; UK events site NearHere got 96% from Jev vs 86% from Gemini Flash-Lite on listing moderation, 58× cheaper per decision; a developer ran Jev zero-shot over 18,514 spam emails to 98.3% vs 98.4% for a TF-IDF logistic regression trained on 14,800 labeled emails (statistical tie), with a calibration curve that routes the uncertain 4.6% of emails to humans for 99.5% on the rest. Arize says it will run its own benchmarks, and calls the "can't hallucinate" claim an overreach — it's a schema guarantee, not correctness.
- [Flowtivity: "Jev by TypeSafe AI: Is the 200x Faster Decision Model Too Good to Be True?" (2026-09-16)](https://flowtivity.ai/blog/jev-typesafe-ai-decision-model/) — Claim-by-claim audit with verdict "the speed and price are real as published, the intelligence comparison is vendor-graded homework": Doom runs at ~10 queries/sec (~$7/hr on published prices); own cost model — 40K classification decisions/mo costs ~$1.34/mo on Jev vs ~$900 on a frontier model at $10/M input tokens (modeled from published prices, not a live benchmark; disclosure of no TypeSafe relationship included).
- [FoundeReview: "The model that won't write" (2026-09-17)](https://foundereview.com/r/typesafe.ai/chokt310055) — Evidence-only review from outside the waitlist (no API key issued): Every's 777 judgments in <0.7s survived contact; Dan Shipper's writing checks — Jev 0.35s median vs Claude Fable 5.1's 8.83s at ~1/580th the cost, catching 6/7 planted defects (it missed, three times, the defect that needed a beat of reasoning); Discord-sourced tests — 96.5% over a 662-message prompt-injection corpus, 6M tokens of word-game judging for $0.14, while one tester fed 2,900 trading days for under a cent and found no predictive signal. Conclusion: speed and price have survived third-party contact; the calibration claim has not.
- [LumaDock: "What is Jev? The AI model with no text output that plays Doom"](https://lumadock.com/blog/what-is-jev-typesafe) — Long explainer with the full workflow-eval table (Jev 67.8% at $0.0004/0.4s vs GPT-5.6 Terra 67.9% at $0.0304/10.1s; security-incidents row: Jev 61.7% at $0.0001/0.3s vs Sol 62.5% at $0.0295/8.5s — same call, 300× cheaper); also surveys self-host routes for the same pattern: GLiClass (Apache 2.0, CPU-fast, scores not calibrated) and Qwen 2.5 logprobs. Written by a GPU-VPS vendor.
- [explainx.ai: "6 Jev Clones in 2 Days" (updated 2026-09-21)](https://www.explainx.ai/blog/six-jev-clones-two-days-2026) — Six independent open-source re-implementations within 48h of launch (Laya, Bespoke Nimble, Jevlike, Kev, OpenJev, DiffusionGemmaJev) via Latent.Space's catalogue, with diversity-of-approach analysis. 2026-09-21 correction: Kev's real lineup is 0.8B/4B/9B on Qwen3.5 (not "8B"), with published benchmarks vs Jev and an HN thread.
- [mer.vin: "Laya: The 33ms Open-Source Decision Model Beating Jev" (2026-09-21)](https://mer.vin/news/laya-the-33ms-open-source-decision-model-beating-jev/) — Practical Laya deep dive: `Router(preload=True)` is the one production detail that matters — lazy loading rebuilds the checkpoint on every language switch (7.4s median reload on CPU), and the headline 0.766 accuracy belongs to the benchmark-fine-tuned checkpoint (zero-shot is ~0.36, near random). Kaggle 2×T4 fine-tuning notebook included.
- [AI Weekly: "Convai ships Laya" (2026-09-19)](https://aiweekly.co/alerts/convai-ships-laya-a-421m-modernbert-decision-model-apache-20) — Model-card honesty rundown: zero-shot 0.362 vs 0.318 random baseline and 0.461 majority-class; refitting one temperature per question type moves mean ECE 0.466 → 0.081; multilingual macro-average on MASSIVE Intent (51 languages) 0.227 — "100+ language coverage buys reach, not accuracy."
- [The Unwind AI: "Get started with Jev for free" (2026-09-20)](https://www.theunwindai.com/p/get-started-with-jev-for-free) — LocalJev: Jev-compatible API served on local models via oMLX, tested across 1,200 requests on five 4-bit models on an M5 Max (prompted JSON probability output, not direct-logit — check calibration); official TypeSafe Agent Skill for Claude Code to find Jev-shaped holes in your codebase; kev's six-questions-in-~160ms claim with Jev leading ~19 points out-of-domain.
- [scriptbyai.com: "The Ultimate Jev Resource List 2026" (2026-09-20)](https://www.scriptbyai.com/jev-resource-list/) — Competing resource catalogue: open System-One implementations (choosekit, Decider, Jeff, LitJev, LocalJev, NanoJev, jevmlx, OpenDecision, openjev-sglang, Jev Visual) — repos unverified by us, listed as discovery leads.

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
