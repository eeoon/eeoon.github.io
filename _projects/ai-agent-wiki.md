---
layout: page
title: "AI-Agent Work Wiki"
description: "Obsidian knowledge base with raw/wiki/conversations tiers and fixed commands so multiple AI agents work consistently."
importance: 7
category: personal
---

**Period** 2026.06 – present · **Type** Personal project · **Role** Design and operation

## Why I Built It

Robotics R&D mixes activities such as paper searching, source code analysis, report and paper writing, projects, and job-search preparation, and the more AI agents are used, the bigger the **problem of context being lost between sessions** becomes. What was needed was not a personal notepad but a work system that is easy for a person to read and edit while multiple agents execute it consistently.

## Design

- **Three-tier separation (Karpathy's LLM Wiki principle):** `raw/` (originals, immutable) → `wiki/` (verified knowledge only) ← `conversations/` (temporary, handover). Because crossing these boundaries is the main cause of context contamination, the promotion flow is made explicit.
- **Personal/company hybrid:** Shared knowledge lives in one tree with a `context: personal | company` tag; projects and documents are split into separate folders.
- **Fixed command keywords:** `save` (store after passing a five-filter gate) · `reference` (restore context) · `ingest` (process originals into the wiki) · `lint` (audit structure, links, speculation, duplicates, sources, and security). Each maps 1:1 to a natural-language trigger ("save this session's work to Obsidian").
- **Safeguards against context contamination:** Source principle, no speculation, no duplicates, and reversals handled as `superseded`. Saving to the wiki requires satisfying at least one of five filters: reuse, handover, decision traceability, failure risk, or shared rules.
- **Dedicated agents:** A lab notebook organizer, a paper reviewer, and others run as subagents with fixed output formats. The [paper reviews](/blog/category/paper-review/) and project pages on this site were also generated from this wiki.

## What I Learned

From an experience of mistaking examples for real work and over-specializing before the domain was settled, then having to roll it back, I derived the rule "no specialization before the domain is fixed." Like a robot system, an agent system only scales when the **interface conventions are fixed first**.

## Tech Stack

Obsidian · Claude Code · Markdown / YAML front matter · Subagents · MCP
