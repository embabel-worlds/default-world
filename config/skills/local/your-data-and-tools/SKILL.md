---
name: your-data-and-tools
description: Answer questions and take action over the user's OWN data and connected tools — their email and correspondence (who they email, how often, how recently, whether they've emailed someone), contacts and people, companies/organizations, industries, bills and billers, meetings — AND their connected integrations (CRM, repos, calendar, drive, etc.), optionally combined with classification or summarization. Activate for "who do I email most", "what companies", "my bills", "when did I last hear from X", "who works at Y", "summarize my recent...", "find/look up in my [connected tool]", or any count/list/comparison/superlative over the user's data or tools.
---

# Your data and tools

This assistant answers from the user's data and connected tools that are
actually available in this world. Check the exposed tools and integrations
before claiming access. If the requested source is absent or disconnected, say
so; do not claim inbox, Gmail, or calendar access, and do not look elsewhere.

## How to answer

1. Check saved views first: call `view_run` with no arguments to list them;
   run a matching view with its `name` and, if needed, declared `params`. In a
   program, use `gateway.view.run({ name, params })`.
2. For an ad-hoc graph question:
   - If you can write Cypher, read `query_guide` (where exposed) for the live
     schema and conventions first, then use `kg_query` where exposed or, in
     `code_mode`, `gateway.kg.query({ cypher, params })`. The gateway call takes
     one object; `params` is an object of bind values (a JSON string is also
     accepted). Graph queries and asks include `{rows, warnings}`; destructure
     `rows`. If a warning reports a failed backing source, few/zero rows mean
     "source unavailable", not "no data"; for other warnings follow the remedy
     and qualify partial rows. Never guess labels or properties.
   - If you cannot write Cypher effectively, use `kg_ask` where exposed or
     `gateway.kg.ask({ question })` in `code_mode`; it asks in plain English
     and has the server write the query. This is a fallback for callers that
     cannot author Cypher well, not the preferred path for a capable coding
     model.
3. When a task needs a program, run JavaScript via `code_mode({ script })`
   (JavaScript by default). A program can combine:
   - `gateway.*` — the user's connected integrations (e.g. fetch an actual
     email, read a CRM record, list repo activity). Use these when the answer
     needs live data the graph doesn't hold.
   - `gateway.ai.classify(...)` / `gateway.ai.complete(...)` — neural predicates
     for the fuzzy parts (categorize, summarize) that Cypher can't express.
   Compose them: query the graph, enrich from an integration, classify the
   result — one program, one answer.
4. If a query errors or returns nothing useful, inspect the result, fix it,
   and re-run. Zero rows can mean no data; check warnings for source failures
   before concluding, and don't invent.

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
- Correspondence direction comes from the counts, not the `EMAILED` edge alone:
  it links the user's `AssistantUser` to a `Person` and carries sent and
  received activity. Outbound ("have I emailed / written to X", "who do I
  email most") uses `sentCount` and `lastOutboundAt`; inbound ("has X emailed
  me", "heard from") uses `receivedCount` and `lastInboundAt`. A missing/null
  count is unknown, not zero. Say a side is absent only when its count is
  explicitly zero; when only one side is positive, state which direction the
  data supports.
- Rank/count from the property the question asks about. If that property is
  uniformly empty or 1, say so plainly — that's a data limitation, not a
  ranking.

## Talk to the user about the answer, not the plumbing

- Answer directly with the result. Run the program and state what it found.
- NEVER show or discuss Cypher, queries, schemas, node/edge models, or how the
  data is stored. The user wants the answer, not the implementation.
- NEVER ask the user how their data is modeled or structured —
  the live schema and tool descriptions already tell you. Figure it out and answer.
