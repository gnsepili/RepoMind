# Bootstrap a Repository Context Layer

Use this reference to create or resume a portable `.ai/` context layer. The goal is evidence-backed navigation, not a fixed documentation volume. Inspect the repository before describing it; state whether an assertion is found in code/configuration, inferred from evidence, or unknown. Do not invent technologies, owners, APIs, or history.

Run the installed read-only inventory before and after work when available:

```sh
python3 .agents/skills/repo-context/scripts/context.py inventory --repo .
python3 .agents/skills/repo-context/scripts/context.py validate --repo .
```

Adapt discovery to the repository rather than assuming a language, framework, or one source layout. Inspect project metadata, entry points, dependency manifests, configuration, tests, generated-code markers, and source roots. Preserve existing `.ai/` layouts and filenames: extend a compatible existing alias instead of moving or duplicating it. Context output belongs in `.ai/`; an installed copy of this skill belongs at `.agents/skills/repo-context`.

## Resume safely

Read `.ai/context-map.json`, `.ai/agents.md`, and existing context files first. The map's optional `bootstrap.completed_steps` and `skipped_steps` are the progress record. Resume the first incomplete phase; repair a partial phase rather than treating a file's existence as completion. A phase may be skipped only when inapplicable and its reason is recorded. Never delete user prose solely to regenerate context.

Complete phases 0, 1, 2, 3, and 4 even when discovery finds a small or empty surface;
describe what was actually found. Only phase 1.5 may be skipped wholesale. Optional
outputs such as a data model or pattern documents may be absent while their phase
is complete. Record those absences in `bootstrap.omitted_artifacts`, keyed by a
repository-relative output path (or directory) with the reason as the value.

## Phase 0 — global context and recurring patterns

Create or extend `.ai/agents.md` as the root navigation document. Record the system purpose, consumers, repository/source layout, observed stack, conventions, configuration and development/test workflow, cross-cutting concerns, and a categorized pattern index. Include only facts supported by paths or other evidence.

Inductively discover recurring patterns: shared abstractions and wrappers, repeated control/data/integration structures, cross-cutting practices (errors, logging, persistence, caching, auth, messaging, validation, configuration, concurrency), and repeated dependency usage. Create `.ai/patterns/<name>.md` only when a real reusable pattern exists. A pattern should explain its purpose and rationale, evidence and locations, how it works, applicable scenarios, constraints and anti-patterns, gotchas, and links to related patterns/modules. Prefer representative code or concrete paths over generic tutorial prose. Explain important decisions and consequences deeply enough to guide work, but let the repository's complexity determine length.

## Phase 1 — discover modules

Identify natural ownership boundaries from directories, public interfaces, imports, domain responsibilities, models, route groups, and shared utilities. Do not make every file a module or collapse unrelated responsibilities into one. Cover core, supporting, infrastructure, and utility modules; call out uncertain/circular boundaries instead of concealing them.

Update the Module Map in `.ai/agents.md` with an informative purpose and a stable link for every documented module. Create no module detail files yet unless preserving an existing layout requires it. Use existing naming conventions; otherwise use portable, readable kebab-case filenames under `.ai/modules/`.

## Phase 1.5 — discover features where applicable

This phase is deliberately between module discovery and relationship mapping. If the repository exposes user, operator, or consumer capabilities, create `.ai/features/<feature>.md` and add a Features Map to `agents.md`.

For server/API systems, group routes, RPC methods, schemas, or commands by user capability and map each to its handling modules, data, business rules, errors, dependencies, and flow. For UI systems, group screens/routes by capability and map components, state, navigation, backend calls, loading/error behavior, and performance decisions. For libraries, workers, infrastructure, or repositories without a meaningful user-facing feature surface, skip the phase and record why in `skipped_steps`.

Features link to implementation modules and relevant patterns; modules link back to the features they implement. Do not infer endpoints or screens merely from directory names.

## Phase 2 — map relationships and architecture

Create the canonical `.ai/architecture/module-interactions.md` in new layouts, or update an existing compatible architecture alias in established layouts. Document architecture as observed: entry points, important end-to-end flows, direct calls, asynchronous messages/events, shared state, public cross-module contracts, external systems with evidence, dependency direction, and circular or unclear boundaries. Explain why a synchronous, asynchronous, or shared-state interaction matters when evidence supports that explanation.

Create `.ai/architecture/data-model.md` only for a non-trivial persistent/domain data model. Describe entity ownership, relationships, invariants, state transitions, access rules, integrity/performance considerations, and source evidence. For stateless or minimal-data projects, record the omitted artifact reason under `bootstrap.omitted_artifacts`; mark phase 2 complete after verifying module interactions. Update `agents.md` with architecture and external-system navigation.

## Phase 3 — generate module context

Create or update one `.ai/modules/<module>.md` per Module Map entry. Each file should make the module usable without a full codebase reread: purpose and boundaries; key components and public surface; inbound/outbound dependencies; data/state; important flows; interfaces/endpoints/events; patterns; features; configuration/external systems; testing; and concrete gotchas. Link to actual source locations and other context files. Include examples only when they clarify a non-obvious contract or behavior; do not pad simple modules to meet a line count.

## Phase 4 — cross-reference and finalize

Make context navigable in both directions: patterns list where used modules; modules link dependencies, patterns, and features; features link modules and patterns; architecture links relevant patterns/modules. Verify every relative link, map entry, and source association. Create `.ai/README.md` describing the structure and task-navigation flow: start at `agents.md`, select features/modules, follow patterns and architecture, then update context with the change. Remove placeholders only when replacing them with evidence, not by guessing.

Finish by updating `.ai/context-map.json` using the contract in `document-contract.md`, recording each generated document's sources and `related` links. Mark completed phases only after validation passes. `agents.md` and `README.md` may have an empty `sources` list when they are navigation-only; every other generated context document needs source coverage.

## Completion checks

- `agents.md`, patterns, module map, architecture, modules, and README form a coherent graph.
- Feature documentation is completed or explicitly skipped with a reason.
- `module-interactions.md` exists; `data-model.md` exists only when applicable.
- Every context document has a context-map entry, accurate source globs, and explicit related documents.
- No placeholders, fabricated facts, or broken links remain.
- `python3 .agents/skills/repo-context/scripts/context.py validate --repo .` reports success (or any tool limitation is reported with the manual checks performed).

## Use context for a task

For a feature, bug, refactor, investigation, or documentation task: begin at `.ai/agents.md`; identify the relevant feature, module, pattern, and architecture documents; read their direct `related` links and source evidence as needed; then make a plan that names affected files, contracts, risks, tests, and context updates. Treat context as a navigation aid, not a substitute for reading changed code. Update affected context after the implementation or investigation resolves the facts.

## Map declared cross-repository work

When the task explicitly names multiple repositories or services, read each declared repository's `.ai/agents.md` and relevant documents. Create or update a cross-repository mapping only within the scope the user authorized. Record participating repositories, per-repository modules and changes, shared contracts/data/events, ownership, ordering, compatibility, rollback/failure behavior, and verification. Preserve shared contracts and coordinate their compatible updates; do not assume a particular repository name, database, deployment topology, or shared ownership.
