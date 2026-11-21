# Types

Dynamic type schemas that define data structures the assistant understands. All `.yml` files in this directory are loaded. Each file contains a list of type definitions with named properties.

Types are used as inputs and outputs for actions, and are automatically inferred for webhook payloads.

```yml
- name: OrderEvent
  description: An incoming order from the e-commerce system
  properties:
    orderId: The unique order identifier
    customerName: Name of the customer
    total: Order total amount
```

## Shared labels

A **shared label** (the engine calls it a parent, or super-label) is a type other types parent onto with
`parents:`. `Memory` is one: every memory system — the assistant's own `DiceMemory` here, `ZepMemory`,
`GraphitiMemory` and the rest in their realms — declares `parents: [Memory]`, so a query, view, app or
agent written against `:Memory` is answered by every system and never names a vendor.

```yml
- name: ZepMemory          # in realm-zep-memory/types/
  parents: [Memory]
  visibility: internal     # restated — not inherited
  userAnchor: false        # restated — not inherited
  description: "…"        # restated — not inherited
  properties:
    id: { identity: true, description: "…" }   # the child's OWN identity — not inherited
  virtualJoins: [ … ]      # the child's own producer, over the parent's edge names
```

What `parents:` does:

- **Inherits properties, labels and lifecycle.** A child's rows carry the parent's label physically
  (`LABELS_AND_COMPOSITION.md` §2), the parent's properties are part of the child's contract (a child adds,
  never drops), and a `lifecycle:` on the parent (`Memory`'s `active: [ACTIVE]`) holds every child's
  rows too, whichever system fetched them.
- **Does not inherit** `visibility`, `userAnchor`, `description` or identity. A child restates each; a
  child with `virtualJoins` needs its own `identity: true` property.
- **A join on a child answers the parent.** `(:Concept {value:$t})-[r:RELEVANT_TO]->(m:Memory)` fans out
  to every child that declares that edge, so one query reads every system and `m.source` says which
  answered. Name the child instead (`:DiceMemory`) to read one system.
- **A bare parent MATCH is refused unless something reaches a child.** `MATCH (m:Memory) RETURN m` is
  refused with a FIX naming the edge to write, because a virtual child's rows only exist when a query
  reaches them through their join. A parent with stored rows of its own (`Todo`) answers the bare form
  from those rows alone — it never reaches a virtual child that way.

Two kinds of parent, and only one is a shared label in this sense:

- **Realm parents** — declared in YAML, here or in a realm, with the label stamped on every child's rows:
  `Memory`, `Todo` and `LogEvent` here; the code family (`Source`, `SourceDeclaration`, `CallSite`,
  `CodeType`, `Method`, `Call`, …), the artifact family (`Artifact`, `Dependency`, `Licence`) and
  `StackFrame` / `ThrownException` in the vocabulary realm `realm-code-model`, which exists to be
  parented onto.
- **Host parents** — `Signal`, `Notification`, `Person`, `Contact`: JVM types the engine owns. Parenting
  onto them registers a child with the host (a realm signal type, a contact mirror); no parent label is
  stamped, and they are not part of the ladder below. A **spine** (`spine:` / `hub:`) is a third thing
  again — a shared identity a record attaches to, not a label it carries.

### The ladder

A shared label turns a change of system into four steps, and nothing above the label changes at any of
them — that is what it is for:

| Rung | What you do | Where |
|---|---|---|
| **Mirror** | Install the realm that brings a system. Its child joins the parent and answers beside the rest. | `realms.yml`, `realm_install` |
| **Compare** | Run the bakeoff view for the label — one row per item each installed system holds, `system` naming which. | `memory_bakeoff`, `memory_systems` (`../views/ladder.yml`) |
| **Promote** | Copy what an incumbent holds into the assistant's own system. The original is marked `PROMOTED` and leaves plain reads, so the fact answers once. | `gateway.memory.promote`; `promoted_memories` reads the ledger |
| **Retire** | Uninstall the realm. The parent keeps answering from what remains. | `realm_remove` |

`Memory` has all four rungs. `Todo` has Mirror and Compare (`my_todos` here; `my_github_todos` and the
comparison `todo_bakeoff` — a statement-level UNION of both lists — ship with realm-github, since a view
over a realm's labels lives in that realm): there is no native todo write tool yet, so nothing to promote
into. The code and artifact families have Mirror only — they are contracts for reading, not stores.

### The tag convention

A realm that brings a child of shared label `X` carries the tag `x` in its `realm.yml` — `memory`,
`todo`, `code`. The catalogue row of a realm that is not installed carries no types, so the tag is how
"Add a system" under a label finds the realms that could stand behind it.
