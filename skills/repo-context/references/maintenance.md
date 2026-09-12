# Maintain Repository Context Incrementally

Use this process after code changes, not a wholesale regeneration. Context is a maintained model of the repository: preserve accurate user-written prose and make the smallest evidence-based change needed to keep it true.

## Start with a diff and impact analysis

1. Read `.ai/context-map.json`, `.ai/agents.md`, and the changed context documents before editing.
2. Inspect the code diff and classify it: behavior, public interface, dependency, feature flow, persistent data, configuration, external integration, convention/pattern, or only non-semantic refactoring.
3. Run the skill's read-only impact support when available: `python3 .agents/skills/repo-context/scripts/context.py impact --repo . --changed path/to/file ...` (or use `--base <commit>`), then inspect direct and transitive dependents. An import, event, API contract, shared table/cache key, generated interface, or renamed/moved source can affect documents beyond the nearest module. The checker identifies mapped files and link/manifest issues; it cannot determine semantic staleness, so make that judgment from the diff and code.
4. Compare the result to the map's `sources` and `related` graph. Update all semantically affected documents, not merely documents whose source glob directly matches the changed file.

Do not update context for formatting-only edits, comments, renames with no externally meaningful change, or test-only edits unless the testing strategy, contract, or an existing documented fact changed.

## Update by semantic effect

| Change | Context to consider |
| --- | --- |
| New or removed module | Module Map, module doc, relationships, affected features/patterns, map entries and links |
| Changed public API/command/event | Module doc, feature docs, module interactions, dependents, external contract references |
| Changed feature behavior or user flow | Feature doc, implementing modules, rules/errors, relevant architecture flow |
| Changed dependency, shared state, queue, cache, or integration | Both endpoints' module docs, interactions, external systems, patterns, data model when relevant |
| Schema/entity/state transition/invariant | Data model, owning module, readers/writers and feature flows |
| New or changed recurring practice | Pattern doc, where-used references, modules using it, agents index |
| New failure mode, incident lesson, or constraint | The affected module/pattern gotcha and linked flow |

When a source moves, update map globs and evidence paths without discarding useful explanation. When a document no longer applies, retain user prose where possible, state its supersession/retirement clearly, and remove links only after updating dependents. Do not silently erase a documented decision because the source evidence changed; flag it for confirmation if the current code is ambiguous.

## Preserve authored context

Treat prose outside generated/evidence sections as user-owned. Keep its wording, structure, and links unless it is demonstrably false, the user requests a rewrite, or a small correction is necessary. Add evidence, corrections, and dated notes around it rather than flattening it into generic text. Never replace a detailed explanation with a terse inventory generated from a diff.

Label uncertain conclusions as inferred and include the paths that support them. If a requested change creates an unresolved architectural choice (ownership, compatibility, transaction boundary, event versioning, or inconsistent dependents), report the ambiguity instead of inventing a policy.

Optionally check the repository's established ADR/decision convention when a change introduces or alters a significant architectural decision (such as an integration provider, durable workflow, infrastructure choice, or cross-module ownership rule). Update an existing decision record when the change is clearly covered; otherwise flag or create a new record only within the repository's documented process.

## Reconcile the map and validate

For every created, moved, retired, or updated context document, update `.ai/context-map.json`: source globs describe the evidence it represents and `related` lists direct navigation/dependency links. Ensure source coverage is not overly broad; exceptions go in `excluded_sources` with a reason. Preserve `bootstrap` progress metadata.

Then run:

```sh
python3 .agents/skills/repo-context/scripts/context.py validate --repo .
```

Also check changed links, that `agents.md` still indexes current patterns/modules/features/architecture, and that dependents retain valid contracts. Report updated files, semantic impacts covered, any intentionally untouched prose, and validation results. Continue with safe, evidence-based repairs; escalate only a genuine blocker, such as missing authority, ambiguous architecture, unavailable required inputs, or a persistent tool/contract failure with the evidence gathered.
