# Drasi: A New Take on Change-Driven Architectures

## SCaLE 23x — Allen Jones | 1 Hour

---

## I. The Problem: Reacting to Change is Harder Than It Should Be (10 min)

### A. Open with a relatable scenario
- You have distributed data across multiple systems (databases, APIs, Kubernetes)
- Something changes and downstream systems need to know — fast and precisely
- Sounds simple; in practice it's a mess

### B. How teams typically solve this today
- **Polling** — wasteful, latent, scales poorly
- **Change Data Capture (CDC)** — low-level, row-oriented, producer-defined semantics
- **Event-driven architectures** — powerful, but shift complexity to consumers (parsing, filtering, state management, deduplication)
- **Custom glue code** — brittle, inconsistent, hard to maintain across teams

### C. The core tension
- Producers define what changes look like, but consumers define what changes *matter*
- Every consumer ends up rebuilding the same filtering/state logic
- No standard way to express "tell me when *this specific condition* changes"

---

## II. Change-Driven vs. Event-Driven: A Key Distinction (8 min)

### A. What event-driven gets right
- Decoupled producers and consumers
- Asynchronous, scalable
- Well-understood patterns (pub/sub, event sourcing, CQRS)

### B. Where event-driven falls short
- Events are producer-defined — consumers must parse, filter, correlate
- Consumers need external state to determine if an event is meaningful
- High fan-out of irrelevant events; consumers do heavy lifting
- Semantic meaning is buried in payload parsing logic

### C. The change-driven model
- **Consumer-driven semantics** — the consumer declares what constitutes a meaningful change
- **Query-defined, not schema-defined** — change semantics come from your query, not the source system
- **Result set transitions** — think "items entering and exiting a live result set" not "events arriving on a topic"
- **Less infrastructure, less code** — no polling loops, no custom filters, no state management boilerplate

### D. Analogy
- Event-driven: "Here's everything that happened; figure out what you care about"
- Change-driven: "Tell me exactly what you care about; I'll tell you when it changes"

---

## III. Introducing Drasi (10 min)

### A. What is Drasi?
- Open-source Data Change Processing platform (CNCF Sandbox)
- Simplifies the creation and operation of change-driven solutions
- Works with your existing databases and systems — not a rip-and-replace

### B. Core mental model: Sources → Continuous Queries → Reactions
- **Sources** — connect to existing data (PostgreSQL, MySQL, SQL Server, Cosmos DB, Kubernetes, Event Hubs, etc.)
- **Continuous Queries** — declarative queries (openCypher / GQL) that run perpetually, maintaining a live result set
- **Reactions** — actions triggered when items are added to, updated in, or deleted from the result set

### C. Why this architecture matters
- Queries span multiple sources in a single statement (e.g., join PostgreSQL + Cosmos DB)
- Change semantics are defined by the query author, not the source
- Graph data model under the hood — tables map to nodes, relationships are first-class
- Result change events are typed: added / updated (with before & after) / deleted

### D. Deployment flexibility
- **drasi-lib** — Rust crate, embed the engine in-process (edge, IoT, custom pipelines)
- **Drasi Server** — single binary or Docker container, no Kubernetes required
- **Drasi for Kubernetes** — full platform with scaling, observability, enterprise sources/reactions

---

## IV. How It Works: A Closer Look (7 min)

### A. Continuous Query lifecycle
1. Subscribe to sources and describe expected node/relationship labels
2. Bootstrap — query sources to load initial result set
3. Process source change events continuously
4. Emit result change events (added / updated / deleted)

### B. Expressing change with queries
- Pattern matching with openCypher/GQL — familiar, declarative, graph-native
- Examples:
  - Simple: `MATCH (o:Order) WHERE o.status = 'delayed' RETURN o`
  - Cross-source: `MATCH (o:Order)-[:PLACED_BY]->(c:Customer) WHERE ...`
  - Temporal: detect absence of change with `trueFor` / `trueLater`

### C. Reactions: from detection to action
- Webhook, SignalR (real-time UI), Event Grid, EventBridge, stored procedures, Debezium, Dapr, and more
- Handlebars templating for precise payloads
- One query can drive multiple reactions; one reaction can watch multiple queries

---

## V. Demo 1 (10 min)

> **[PLACEHOLDER — Demo TBD]**
>
> *Suggested positioning: A foundational demo showing the core Source → Query → Reaction flow. Keep it concrete and approachable — e.g., a PostgreSQL source with a continuous query detecting a specific business condition and triggering a reaction. This grounds everything discussed so far.*

---

## VI. Patterns & Advanced Capabilities (5 min)

### A. Multi-source queries
- Join data across heterogeneous systems in a single query
- No need for a shared schema or common event bus

### B. Temporal capabilities
- `trueFor` — "alert me if a sensor reading stays above threshold for 5 minutes"
- `trueLater` — "check back in 30 minutes to see if this order shipped"
- Responding to the *absence* of change

### C. AI-driven workloads
- Keep embeddings, model inputs, and RAG pipelines aligned with live data
- Drasi ensures AI systems react to the *specific* data changes that matter
- No stale context — continuous freshness without polling

### D. Middleware pipeline
- Transform and enrich incoming changes before they reach queries
- Unwind nested arrays, apply jq transforms, decode, relabel

---

## VII. Demo 2 (10 min)

> **[PLACEHOLDER — Demo TBD]**
>
> *Suggested positioning: A more advanced demo that showcases a differentiating capability — e.g., multi-source joins, temporal queries (absence of change), or an AI/RAG freshness scenario. This is your "wow" moment.*

---

## VIII. Practical Takeaways & Getting Started (5 min)

### A. When to reach for Drasi
- You need to react to specific, meaningful data changes — not just raw events
- You're tired of building custom polling/filtering/state management logic
- You want to standardize change detection patterns across your org
- You're building AI systems that need continuously fresh data

### B. Getting started
- `drasi.io` — docs, tutorials, quickstarts
- GitHub: `github.com/drasi-project`
- Try Drasi Server locally with Docker in minutes
- CNCF Sandbox — open governance, Apache 2.0

### C. Call to action
- Try the tutorials (Curbside Pickup, Building Comfort, Risky Containers)
- Contribute — sources, reactions, middleware, docs
- Join the community

---

## Timing Summary

| Section | Duration |
|---|---|
| I. The Problem | 10 min |
| II. Change-Driven vs. Event-Driven | 8 min |
| III. Introducing Drasi | 10 min |
| IV. How It Works | 7 min |
| V. Demo 1 | 10 min |
| VI. Patterns & Advanced Capabilities | 5 min |
| VII. Demo 2 | 10 min |
| VIII. Takeaways & Getting Started | 5 min |
| **Total** | **~65 min (5 min buffer for Q&A / pacing)** |

---

## Notes on Structure

**Why this order works:**

1. **Problem-first** — establishes shared pain with the audience before introducing any solution. Technical audiences respect talks that earn the right to pitch a tool.
2. **Differentiation before introduction** — by drawing the change-driven vs. event-driven distinction *before* introducing Drasi, the audience already understands the paradigm shift. Drasi then lands as an implementation of an idea they've already bought into.
3. **Demo 1 after architecture** — grounds the concepts in something real. The audience has enough mental model to appreciate what they're seeing.
4. **Advanced patterns between demos** — keeps energy up and sets the stage for a more impressive Demo 2.
5. **Demo 2 as the climax** — the audience now has full context to appreciate a sophisticated scenario (multi-source, temporal, or AI).
6. **Short, punchy close** — don't re-explain; give them the "what now" and get out.