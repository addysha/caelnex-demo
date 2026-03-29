# CAELNEX - Mission Continuity Platform

> *The nervous system of a launch organisation. Graph-native. Debt-free. Built to last from the first launch pad on Earth to the first habitat on Mars.*

**Live demo →** `yourusername.github.io/caelnex-demo`

---

## What this is

Caelnex is a configuration intelligence platform for aerospace launch organisations - designed to eliminate the structural flaw every rocket company shares: they built their operating tools after they built their rockets.

The result is organisational amnesia. Configuration drift. Engineers who leave take irreplaceable knowledge with them. A part revision changes in engineering and nobody tells launch ops. A software build gets loaded onto hardware it was never validated against. A supplier falsifies 19 years of material certifications and nobody sees the statistical signature because no system is looking at all the batches at once.

Caelnex fixes this at the architecture level - not with better processes or stricter culture, but with a graph-native data model where every physical component, engineering decision, software build, supplier certificate, and human responsible for them is a node. Every relationship between them is an edge. Change propagates automatically. Nothing is isolated in a CAD file, an inbox, or someone's memory.

This repository contains the Phase 0 interactive prototype - a fully functional mission dashboard, three real-world failure simulations, a hardware sensor grid, and an AI-powered graph intelligence layer.

---

## The three failures this would have prevented

These are not hypotheticals. All three are documented, investigated, and publicly reported.

### NASA OCO + Glory - $697 million lost (2009, 2011)
Sapa Profiles Inc. falsified aluminium tensile certifications for 19 years. Nobody noticed because nobody was looking at all batches simultaneously. Every certificate sat at the exact specification boundary - the statistical signature of a system gaming a pass/fail threshold. Caelnex Organ 4's cross-batch AI analysis detects this pattern within the first three to four batches.

**→ [`caelnex_simulation.html`](caelnex_simulation.html) - interactive walkthrough, node-by-node**

### Ariane 5 Flight 501 - $370 million in 40 seconds (1996)
Reused Ariane 4 navigation software was never validated against Ariane 5's higher velocity profile. An unhandled integer overflow destroyed the vehicle 37 seconds after launch. Caelnex Organ 2's `VALIDATED_AGAINST` constraint makes it structurally impossible to load a software build onto hardware it has never been validated against.

### Mars Climate Orbiter - $327 million lost (1999)
Lockheed Martin's navigation software output pound-force seconds. NASA's systems expected newton-seconds. The unit mismatch was noticed informally months before loss of signal - but informal flags had no escalation mechanism. Caelnex Organ 7's activity zone monitor surfaces unresolved flags with a 72-hour escalation clock.

**→ [`caelnex_simulation_2_and_3.html`](caelnex_simulation_2_and_3.html) - both cases, interactive**

---

## What's in this repo

| File | What it is |
|------|-----------|
| `index.html` | Full mission dashboard - all 7 organs, live countdown, AI-powered graph intelligence via Claude API |
| `caelnex_simulation.html` | SPI fraud simulation - $697M case, 7-step interactive walkthrough |
| `caelnex_simulation_2_and_3.html` | Ariane 5 + Mars Climate Orbiter - 6 steps each, organ-mapped interventions |
| `caelnex_defence_grid.html` | Hardware sensor grid - 6 zone coverage, camera + LiDAR + thermal + XRF stack |
| `nexus_scenarios.html` | 4 operational triage scenarios across engineering, supply chain, compliance |

---

## The seven organs

Caelnex models a launch organisation as seven connected domains - all sharing one configuration graph.

| # | Organ | Mission |
|---|-------|---------|
| O1 | Engineering | Every design change propagates automatically to every downstream stakeholder |
| O2 | Flight Software | No build can be loaded onto hardware it was never validated against |
| O3 | Manufacturing | Every bolt, every torque, every technician - a live attributed build record |
| O4 | Supply Chain | Part genealogy from purchase order to installed component, fraud detection built in |
| O5 | Launch Ops | Live readiness score computed from graph state - exception-driven go/no-go |
| O6 | Compliance | Cryptographically chained audit trail - ITAR, CMMC, one-click export |
| O7 | Compliance Index Engine | Pre-submission validation across all organs - Poka-Yoke at organisational scale |

---

## Technical architecture

This prototype is pure HTML/CSS/JS - no build step, no framework, deployable anywhere.

The production system being built uses:

```
Graph database     Neo4j              - directed property graph, Cypher query language
Audit store        PostgreSQL         - append-only, pgcrypto cryptographic chaining
API layer          GraphQL / Apollo   - schema mirrors graph structure
Backend            Node.js + TypeScript
Frontend           React + TypeScript + React Flow
Auth               Keycloak           - ITAR citizenship attributes, role-based access
Offline sync       Yjs (CRDT)         - conflict-free replication for air-gapped sites
AI layer           Claude API         - natural language graph query, anomaly recall
Monorepo           Turborepo + pnpm
Deployment         Docker → Kubernetes
```

---

## Academic foundation

Caelnex's core claims are grounded in documented primary sources - not assumptions.

- **Configuration drift as a failure cause** - Columbia Accident Investigation Board Report (2003), Ch. 7: "lack of institutional memory" named as an organisational cause
- **Drift into failure mechanism** - Rasmussen (1997): migration toward the boundary of acceptable performance under production pressure
- **Safety-critical systems theory** - Leveson, MIT STAMP framework: accidents result from inadequate constraints, not just component failures
- **Institutional knowledge loss** - Nonaka & Takeuchi (1995): SECI model, systematic loss of tacit knowledge at engineer departure
- **Digital thread mandate** - US DoD Digital Engineering Strategy (2018): authoritative source of truth across the system lifecycle
- **Poka-Yoke (Organ 7)** - Shigeo Shingo, Toyota Production System (1986): mistake-proofing at the point of action, not downstream

Four claims in the founding documents are designated original hypotheses - not yet substantiated by literature - and are being tracked as formal research questions as the system accumulates mission data.

---

## Status

| Milestone | Status |
|-----------|--------|
| Founding documents - vision, technical brief, research foundation, IP brief | ✓ Complete |
| Seven organ specifications with full data models | ✓ Complete |
| Interactive prototype - all 7 organs, AI layer | ✓ Complete |
| Three failure simulations - documented cases | ✓ Complete |
| Hardware defence grid specification | ✓ Complete |
| Phase 0 - Neo4j schema, Postgres audit store, GraphQL API | In progress |
| Phase 1 - First vehicle campaign, live data | Planned |

---

## About

Caelnex is being built by a father and son founding team - a domain strategist and a software engineer. The founding documents were signed in March 2026. The provisional IP brief, trademark filing, and IP assignment agreement are in preparation.

The system was designed with one constraint: no technical debt. The data model is domain-agnostic by architecture - the same graph schema that manages an Electron campaign at Mahia Peninsula is the schema that will manage a habitat build at the lunar south pole. The graph travels with the hardware. The system never forgets why.

---

*Built with care. Grounded in evidence. Designed to last.*
