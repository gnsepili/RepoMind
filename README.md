# RepoMind

Give your repository a memory.

RepoMind is a portable AI-agent skill that discovers a project's structure,
generates a navigable `.ai` context layer, and helps agents maintain that context
as the code evolves. It adapts to the languages, frameworks, modules, and features
found in each repository.

The project is called **RepoMind**; the installed skill is named **`repo-context`**.

## Complete original prompts

The full original `.ai/prompts` system is bundled byte-for-byte under
[`skills/repo-context/prompts/`](skills/repo-context/prompts/). The skill reads each
applicable prompt in full before executing it. All original templates, examples,
walkthroughs, depth requirements, and verification criteria are included.

| Purpose | Full original prompt |
| --- | --- |
| Orchestration | [master-context-generator.md](skills/repo-context/prompts/master-context-generator.md) |
| Sequence guide | [sequence-guide.md](skills/repo-context/prompts/sequence-guide.md) |
| Step 0 | [step-0-extract-global-context.md](skills/repo-context/prompts/step-0-extract-global-context.md) |
| Step 1 | [step-1-discover-modules.md](skills/repo-context/prompts/step-1-discover-modules.md) |
| Step 1.5 backend | [step-1.5-discover-features-backend.md](skills/repo-context/prompts/step-1.5-discover-features-backend.md) |
| Step 1.5 frontend | [step-1.5-discover-features-frontend.md](skills/repo-context/prompts/step-1.5-discover-features-frontend.md) |
| Step 2 | [step-2-map-relationships.md](skills/repo-context/prompts/step-2-map-relationships.md) |
| Step 3 | [step-3-generate-module-contexts.md](skills/repo-context/prompts/step-3-generate-module-contexts.md) |
| Step 4 | [step-4-cross-referencing.md](skills/repo-context/prompts/step-4-cross-referencing.md) |
| Updates | [update-context.md](skills/repo-context/prompts/update-context.md) |
| Task template | [task-prompt.md](skills/repo-context/prompts/task-prompt.md) |
| Task guide | [task-prompt-guide.md](skills/repo-context/prompts/task-prompt-guide.md) |
| Cross-service tasks | [cross-service-prompt.md](skills/repo-context/prompts/cross-service-prompt.md) |

The short `references/` files handle portable routing and tooling. They do not
replace the full prompts. Historical filenames inside the originals are resolved
by the routing adapter without editing the original text. Full-stack projects run
both feature prompts. Example systems are adapted to actual repository evidence.

The initial publication used condensed instructions; this version restores the
complete originals. The same prompt guidance is preserved, though generated
results still vary with the model, repository, and execution.

## Quick start

Requirements: Python 3.10 or newer, Git for cloning and change detection, and a
coding agent that reads skills or project instructions. The helpers use only the
Python standard library. You can also use explicit changed paths without Git.

```sh
git clone https://github.com/gnsepili/RepoMind.git
cd RepoMind
python3 skills/repo-context/scripts/install.py --repo /absolute/path/to/your-project
```

For a project using Claude Code, also install its instruction bridge:

```sh
python3 skills/repo-context/scripts/install.py --repo /absolute/path/to/your-project --claude
```

Then open the target project in your coding agent and ask:

> Read `.agents/skills/repo-context/SKILL.md` and set up the AI context system for this repository, starting at Step 0 and completing every applicable phase.

If your agent exposes the skill by name, you can instead use:

> Use $repo-context to set up the AI context system for this repository.

The installer copies a self-contained skill into your project and adds a bounded
instruction block to `AGENTS.md` (and optionally `CLAUDE.md`). Existing instructions
are preserved. **The installer does not generate context by itself:** your agent
reads the repository and completes the generation workflow.

## What gets generated

```text
your-project/
├── AGENTS.md                         # Context loading and maintenance rule
├── .agents/skills/repo-context/       # Portable skill and helper scripts
└── .ai/
    ├── agents.md                     # Context entry point and navigation
    ├── README.md                     # How to use the context
    ├── context-map.json              # Source mapping and generation progress
    ├── architecture/                 # Interactions and applicable data models
    ├── features/                     # User or consumer capabilities
    ├── modules/                      # Responsibilities, interfaces, and gotchas
    └── patterns/                     # Recurring implementation practices
```

The content and number of documents follow the actual project. Small projects
do not need invented patterns or data models. Existing context layouts and useful
authored explanations are preserved when adopting RepoMind.

## Generation workflow

| Phase | Purpose |
| --- | --- |
| 0 | Extract global context, conventions, and recurring patterns |
| 1 | Discover module boundaries and responsibilities |
| 1.5 | Discover features and their implementing modules |
| 2 | Map relationships, flows, integrations, and relevant data models |
| 3 | Generate detailed module context |
| 4 | Cross-reference, validate, and finalize navigation |

The agent completes the applicable sequence in one setup task and records progress
for safe resumption. Optional artifacts can be omitted with a reason. Claims must
be grounded in the repository; inferred or unknown details should be labelled.

## Keeping context current

After adoption, the project instructions ask agents to load task-relevant context
before editing and reconcile affected documents before completing code changes.
The source map identifies directly affected documents and related candidates;
the agent decides which explanations actually need to change.

Run these commands from the target project:

```sh
# Inspect maintained files without reading their contents.
python3 .agents/skills/repo-context/scripts/context.py inventory --repo .

# Include committed changes since a base plus staged, unstaged, and untracked work.
python3 .agents/skills/repo-context/scripts/context.py impact --repo . --base <commit>

# Or inspect explicit task-owned paths, including removed files.
python3 .agents/skills/repo-context/scripts/context.py impact --repo . --changed src/example.py

# Validate map structure, source coverage, and local file links.
python3 .agents/skills/repo-context/scripts/context.py validate --repo .
```

`validate` returns `0` on success, `1` for structural problems, and `2` for input or
execution errors. The checker does not prove that prose is semantically accurate
and does not validate link anchors. Agent review remains part of maintenance.

There is no background LLM service, hidden upload, automatic commit, or automatic
PR. Ongoing maintenance depends on the agent reading the installed instructions
or discovering the skill. Changes made outside an agent task can be reconciled by
asking the agent to update context against a commit or a list of changed paths.

## Installation behavior

- Running the installer again with the same files and rules is idempotent.
- A conflicting skill file or modified managed instruction block is reported for
  reconciliation; it is not overwritten automatically.
- The installed project copy is portable and has no dependency on the original
  clone's absolute path.
- Updates to the RepoMind clone do not silently replace adopted project copies.
  Review and reconcile newer skill versions in those projects deliberately.

## Development

The maintained skill lives in [`skills/repo-context`](skills/repo-context/SKILL.md).
Its full generation and maintenance prompts are in `prompts/`. Portable routing
and the supplementary source-map contract are in `references/`.

```sh
python3 skills/repo-context/scripts/test_context.py -v
python3 skills/repo-context/scripts/verify_prompts.py
```

Tests run in temporary repositories and cover installation preservation and
conflicts, Git change detection, mapping validation, exclusions, and phase state.
The prompt verifier checks every original file against the committed SHA-256 and
byte-count manifest. Add `--source /path/to/original/.ai/prompts` to compare the
entire file set and contents directly with an original prompt directory. This
checks preservation, not the semantic quality of generated context.

## Working with Superpowers

RepoMind maintains repository knowledge. It can be used alongside Superpowers or
another development workflow for planning, implementation, testing, and review.
RepoMind does not replace those workflows or override the project's instructions.
