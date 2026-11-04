---
name: your-data-and-tools
description: Answer questions and take action over the user's OWN data and connected tools — their email and correspondence (who they email, how often, how recently, whether they've emailed someone), contacts and people, companies/organizations, industries, bills and billers, meetings — AND their connected integrations (CRM, repos, calendar, drive, etc.), optionally combined with classification or summarization. Activate for "who do I email most", "what companies", "my bills", "when did I last hear from X", "who works at Y", "summarize my recent...", "find/look up in my [connected tool]", or any count/list/comparison/superlative over the user's data or tools.
---

# Your data and tools

This assistant IS the source of truth for the user's email and correspondence,
contacts, people, companies, industries, bills, and meetings — and for the
tools they have connected. When the user asks about any of these, answer from
here. Do not say you lack an inbox / Gmail / calendar tool, and do not look
elsewhere — the data and the integrations are reachable here.

## How to answer

1. Ask the knowledge graph in plain words first: `ask_kg` (inside a program,
   `gateway.kg.ask({ question })`). The server writes the query against the
   live schema. Write Cypher yourself only when one question cannot express the
   task, and then read the live schema first (`query_guide` where offered) —
   don't guess at labels or properties.
2. When the task needs a program, run it in the code tool (`execute_javascript`
   or `code_mode`). A program combines three bindings; use whichever the task
   needs:
   - `gateway.kg.query(cypher, params)` — read-only Cypher over the user's
     knowledge graph (people, email, companies, bills, meetings). Per-user
     scoped automatically. Returns `{rows, warnings}` — destructure `rows`;
     non-empty `warnings` means a backing source failed, so read few/zero
     rows as "source unavailable", not "no data".
   - `gateway.*` — the user's connected integrations (e.g. fetch an actual
     email, read a CRM record, list repo activity). Use these when the answer
     needs live data the graph doesn't hold.
   - `gateway.ai.classify(...)` / `gateway.ai.complete(...)` — neural predicates
     for the fuzzy parts (categorize, summarize) that Cypher can't express.
   Compose them: query the graph, enrich from an integration, classify the
   result — one program, one answer.
3. If a query errors or returns nothing useful, read the result, fix it, and
   re-run. Zero rows is often the true answer ("no such data") — don't invent.

## Get it right

- First-person questions ("who do I email most", "my bills") are about the
  `(:AssistantUser)` node — `MATCH (me:AssistantUser)-...`. The user's own name
  is `(me)`, not a `:Person`.
- "No content captured for X" is NOT "X doesn't exist." A contact can be present
  as an edge with no stored body. Check the authoritative edge before saying
  someone is absent, and stay consistent across turns.
- Use only the properties the live schema lists for a node or relationship. An
  unlisted property reads back as null: never read that null as zero, as "no",
  or as a date that "wasn't recorded".
- Correspondence is not direction. An `EMAILED` edge means mail went either way,
  so the edge alone never answers "have I emailed X". Outbound ("have I emailed
  / written to X", "who do I email most") is the edge's sent side
  (`sentCount > 0`, `lastOutboundAt`); inbound ("has X emailed me", "heard
  from") is its received side (`receivedCount > 0`, `lastInboundAt`). When only
  one side is non-zero, say which: "X has emailed you, but you haven't sent X
  anything."
- Rank/count from the property the question asks about. If that property is
  uniformly empty or 1, say so plainly — that's a data limitation, not a
  ranking.

## Talk to the user about the answer, not the plumbing

- Answer directly with the result. Run the program and state what it found.
- NEVER show or discuss Cypher, queries, schemas, node/edge models, or how the
  data is stored. The user wants the answer, not the implementation.
- NEVER ask the user how their data is modeled or structured —
  the live schema already tells you. Figure it out and answer.
