---
solution: PRD Generator — AI-Powered Multi-Agent PRD System
live_url: [FILL IN — Vercel deployment URL]
tags: Multi-Agent AI · Full Stack · Solo Build
---

## Problem
Writing a PRD from scratch takes hours of research, competitive analysis, persona work, and prioritisation — and most of it follows the same structure every time. The bottleneck isn't thinking; it's the grunt work of assembling information from 10 different places. PRD Generator automates that entire research and synthesis layer so you can go from a problem statement to a publication-ready document in minutes.

## Who it's for
PMs, founders, and product builders who need to produce structured, research-backed PRDs quickly — without spending hours on market sizing, competitive mapping, and persona definition before writing a single word.

## Tech stack

**AI / Agents**
- Claude Sonnet 4.6 (Anthropic SDK `@anthropic-ai/sdk` v0.105.0) — core LLM for all 6 agents, with streaming
- Tavily Search API — real-time web search for market research and competitive intelligence
- 6 specialised agents: Research, User Research, Feature Strategy, Metrics, Writer, Critic
- Agent pipeline: Phase 1 (Research + User in parallel) → Phase 2 (Feature → Metrics sequential) → Phase 3 (Writer synthesis) → Phase 4 (Critic review)
- Server-Sent Events (SSE) for real-time agent progress streaming to the frontend

**Frontend**
- Next.js (App Router) + TypeScript + React 19
- React Markdown + remark-gfm for rendering structured PRD output
- Custom PRDRenderer component with specialised displays: market stats cards, RICE prioritisation chart, KPI cards by category, competitive landscape table, persona cards
- Tailwind CSS v4

**Backend / Database**
- Supabase (PostgreSQL) — persists all generated PRDs with full content
- Next.js API routes with `maxDuration: 300` for long-running agent chains

**Deployment**
- Vercel (Next.js)
- Supabase Cloud

## What I built — 6-agent pipeline end-to-end

1. **Research Agent** — Web search via Tavily; TAM/SAM sizing, competitive landscape, market trends
2. **User Research Agent** — Generates user personas, pain points, jobs-to-be-done; runs in parallel with Research
3. **Feature Strategy Agent** — Synthesises research into MVP features with RICE prioritisation scores (P0/P1/P2 breakdown)
4. **Metrics Agent** — Defines North Star metric, KPIs by category (growth / engagement / retention / revenue), counter-metrics
5. **Senior Writer Agent** — Synthesises all outputs from all 4 prior agents into a complete, publication-ready PRD in structured markdown
6. **Critic Agent** — Independently reviews the PRD for gaps, risks, weak assumptions, and open questions
7. **PRD Library** — All generated documents saved to Supabase; browsable library at `/prds`; individual PRD view at `/prd/[id]`
8. **Section Regeneration** — Any individual PRD section can be regenerated independently without rerunning the full pipeline

## Challenges
The hardest part was orchestrating 6 agents with the right parallelism — Research and User agents can run at the same time, but Feature agent needs Research output, and Writer needs everything. Getting the sequencing right while streaming real-time SSE updates to the frontend for each agent's completion required careful coordination of async flows. The PRDRenderer was also non-trivial: the Writer agent outputs custom code blocks (`market-stats`, `rice-chart`, `kpi-chart`, `competitor-chart`) that the renderer has to parse and transform into styled UI components, not just raw markdown.

## Learnings
Running 6 LLM calls in a chain takes time — the `maxDuration: 300` limit exists because the full pipeline can run 3–5 minutes on complex problems. The real leverage was parallelising Phase 1: cutting the wall-clock time by running Research and User agents simultaneously shaved a full minute off the p50 latency. The Critic agent was a late addition but turned out to be the most valuable — it caught vague success metrics and missing risk sections that the Writer would have glossed over.

## Metrics / Outcome
- 6 specialised agents in a single pipeline
- Phase 1 agents run in parallel (Research + User) — reduces total time
- Custom markdown renderer with 4 specialised chart/card components
- Persistent PRD library via Supabase
- Real-time streaming of agent progress via SSE

## LinkedIn post angle ideas
- Multi-agent architecture angle: why 6 agents, not 1 — what each one actually does and why the order matters
- Parallelism angle: the one change that cut pipeline time by a third — running two agents at once
- Critic agent angle: why the last agent (who just finds problems) turned out to be the most useful one
