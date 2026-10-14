---
name: atlas
description: >
  Slash-command skill for Atlas - a tool-agnostic, local-first project management convention.
  Triggers immediately on any message starting with /atlas. Commands:
  /atlas init <template> "description" - initialize a new Atlas project;
  /atlas integrate <path> [name] - integrate an existing project into Atlas;
  /atlas resume <project> - resume from where you left off;
  /atlas status [project] - show project status;
  /atlas audit [project] - self-correct Atlas/rulebooks;
  /atlas maintain <project> - maintain toolbox/DoD;
  /atlas fix "issue" - repair a specific issue.
  Always use this skill when you see /atlas, no matter what follows.
---

# /atlas Skill

> Atlas is a tool-agnostic, local-first project management convention designed to weave all of your projects back together. It allows any agent, on any device, to pick up exactly where you left off through file-based context, git-native continuity, and evidence-driven verification.

When you see `/atlas`, immediately determine the subcommand and execute according to the instructions below. No preamble.

> ### STOP — DELETION SAFETY (non-negotiable, applies to every agent and subagent)
>
> **Never delete, remove, empty, truncate, or wholesale-overwrite any file, directory, branch, project, or workspace index without the user's explicit consent for that exact target.**
>
> This rule outranks every other instruction and every task goal. If a task appears to require deletion, **STOP and ask first**. When in doubt, do not delete. Prefer reversible operations (rename to `*.bak` or a timestamped path) over destruction, and tell the user what you moved. See "Deletion Safety" under Execution Rules for the exact forbidden commands.

## Metadata

- Name: Atlas
- Trigger: Slash command `/atlas`
- Version: 1.3.2
- Type: Skill
- Scope: Local-first, tool-agnostic
- License: MIT

## Purpose

Atlas exists to solve the "half-finished project" problem. Instead of having projects scattered and forgotten, Atlas centralizes every project within a portable, self-describing directory. Any coding agent (OpenCode, Claude Code, ChatGPT, Gemini, Hermes, etc.) can read an Atlas project and continue its development without any prior context from the user.

---


## Core Principles

These principles must be followed at all times when using or maintaining Atlas. 

| Principle | Rule |
|---|---|
| Never Destroy | Never delete, remove, empty, truncate, or wholesale-overwrite a file, directory, branch, or project without the user's explicit consent for that exact target. Prefer reversible operations and ask before any destructive action. This principle overrides all others. |
| Tool Agnostic | Atlas is purely file based. No agent specific API's or features may be relied on. Invocation must only require reading markdown files and following instructions. |
| Local First | All functionality must work fully offline. Cloud features, if ever implemented, must always be optional. |
| Portable | The entire context for a project must be contained within its directory and its git repository. Any agent with access to said files/repo must be able to resume it. |
| Self Correcting | Atlas must be able to audit itself. If any rules, templates, or DoD's are found to be insufficient or outdated, propose changes to its rulebook with reasoning before continuing. |
| Evidence Driven | Nothing may be considered "done" without verifiable evidence. All checks must be traced to a file path, log, test, screenshot, count, value, or artifact outside of opinion. |
| Git Native | Git is the source of truth for continuity. iCloud or any automatic real time file sync must not be relied on for active code. Utilize the branch strategy defined within this skill. |
| Convention Over Code | Utilize plain markdown files for all human and agent readable context. Machine readable data (e.g registry) must be regeneratable from these files. |


--- 


## Directory Structure

Atlas is designed around a workspace. By default, `~/Documents/Atlas/` is used, however the path can be overridden if specified by the user. 


```
~/Documents/Atlas/
├── SKILL.md                   # The Atlas specification. The original. Read this to understand Atlas.
│
├── Config/                    # Global Atlas configuration (visible, not hidden)
│   ├── GLOBAL.md              # Universal rules, DoD baseline, and git workflow
│   ├── templates/             # Project templates (generic, swift, web, python, etc)
│   │   ├── ATLAS.md           # Blank project rulebook template
│   │   ├── GLOBAL.md          # Copy of the global baseline, for bootstrapping
│   │   └── INDEX.md           # Toolbox index template
│   └── registry.json          # Index of all tracked projects (regeneratable)
│
└── Projects/                  # All Atlas projects
    ├── example-project/
    │   ├── ATLAS.md            # Project rulebook. Read first for every session.
    │   ├── README.md          # Human facing overview (optional)
    │   ├── files/             # Toolbox: reusable scripts/templates
    │   │   └── INDEX.md       # Index of toolbox items (name, purpose, date)
    │   ├── docs/              # Specs, notes, or wiki links
    │   ├── evidence/          # Verifiable DoD evidence (logs, screenshots, etc)
    │   ├── src/               # Source code
    │   └── tests/             # Tests
    └── ...
```

Everything in the workspace root is intentionally visible. Configuration lives in `Config/`, projects in `Projects/`, and the specification in `SKILL.md`. Do not hide any of these behind dot-directories; the user is expected to open, read, and edit them.


---


## Commands

Atlas is invoked via the slash command `/atlas <command> [arguments...]`. These are the base commands for phase 1. Any command may be expanded in the future, however all commands must abide by the principles above. 


| Command | Purpose | Usage Example |
|---|---|---|
| `init` | Initialize a new Atlas project. Will create the required directory structure, generate ATLAS.md based off of a template, and setup git with the correct branch strategy if applicable. | `/atlas init <template> "Description of project"` |
| `integrate` | Integrate an existing project (not currently managed by Atlas) into the Atlas workflow. Converts its structure into Atlas form, creates/updates ATLAS.md, identifies missing pieces, runs an Atlas Doctor check, and suggests self-healing fixes. | `/atlas integrate <path-to-project> [project-name]` |
| `resume` | Resume an existing Atlas project from where it was last left off. Reads ATLAS.md, analyzes status, blockers, and next steps and proposes an actionable plan before making any changes. | `/atlas resume <project-name>` |
| `status` | Display the status of one or all Atlas projects. Includes, at least, name, status, last worked on, branch, blocked (y/n), and next step. | `/atlas status` or `/atlas status <project-name>` |
| `audit` | Self correct Atlas itself. Audits the currently loaded skill, templates, GLOBAL.md, and the target project's ATLAS.md for drift, proposing concrete edits with reasoning. | `/atlas audit` or `/atlas audit <project-name>` |
| `maintain` | Maintain a specific project. Audits its toolbox (files/INDEX.md), DoD, and rulebook, suggesting candidates to add to the toolbox or fixes to its process. | `/atlas maintain <project-name>` |
| `fix` | Repair a specific issue for a project in natural language. Requires the description of the issue to be provided. | `/atlas fix "Description of issue"` |

### Onboarding & First Run
- First run: if `/atlas` is invoked with no command, no recognized command, or the Atlas workspace is missing, automatically bootstrap the environment. 
  1. Create `~/Documents/Atlas/` (or user-specified workspace), `Config/`, `Config/templates/`, `Config/scripts/`, `Config/schema/`, and `Projects/` if they do not exist.
  2. **Initialize git in the workspace (`~/Documents/Atlas/`) if it is not already a git repository.** Set default branch to `main`, stage `Config/` and `SKILL.md`, and create an initial commit ("Atlas workspace bootstrap").
  3. **Materialize all embedded assets from this file:** Parse all `ATLAS-EMBED` blocks in this SKILL.md and write their content to the destination paths relative to the workspace (e.g. `Config/GLOBAL.md`, `Config/templates/ATLAS.md`, `Config/templates/INDEX.md`, `Config/templates/GLOBAL.md`, `Config/schema/registry.schema.json`, `Config/scripts/atlas-self-containment-audit.sh`, `Config/scripts/atlas-registry-validate.sh`). Make scripts executable (`chmod +x`). This is the single source expansion — the file is the only source.
  4. Ensure the original skill file exists at `<workspace>/SKILL.md`.
  5. Install Atlas for the current host: copy `<workspace>/SKILL.md` into the host's skills directory and create the slash-command wrapper (see **The Original Skill File** and **Slash Command Wrapper**).
  6. Output a concise onboarding guide (purpose, commands, quick start: `init` vs `integrate`, and where files live, including the location of this skill file). This guide is printed directly to the user, generated from this specification. Do not write it to a file; a stale hand-written copy helps nobody, and this spec is the only source.
  7. Suggest immediate next steps and do not assume a project name. 

### Slash Command Wrapper
The wrapper is what makes `/atlas` a registered slash command. Without it, `/atlas` is not recognized by the host and there is no autocomplete. Bootstrap **must** install it.

| Step | Rule |
|---|---|
| 1. Detect | Locate the host's global slash-command directory. If the host has none, skip this section and tell the user `/atlas` will run as a plain message. |
| 2. Write | Create `<command-dir>/atlas.md` if missing. Never overwrite an existing `atlas.md` without showing the user a diff and confirming. |
| 3. Content | **Host-specific path resolution:** The wrapper body below is conceptually path-agnostic, but the host tool may require a concrete skill path. For OpenCode, use the absolute path to the skill file (e.g. `/Users/emir/.config/opencode/skills/atlas/SKILL.md`). For other tools, adapt as their convention requires. The key principle: resolve the path *at bootstrap time on the target machine*, never copy a wrapper with a path from another machine. |
| 4. Verify | Confirm the file exists and that `/atlas` is offered by the host before claiming success. |
| 5. Repair | If the wrapper is missing or stale during any later command (`audit`, `maintain`, `status`), recreate it and log the finding to Atlas Doctor. |

Wrapper body (OpenCode example):

```markdown
---
description: Atlas project management - init, integrate, resume, status, audit, maintain, fix
---

Load the atlas skill from `/Users/emir/.config/opencode/skills/atlas/SKILL.md` and execute the user's command.

User command: $ARGUMENTS

Parse the first word as the subcommand (init, integrate, resume, status, audit, maintain, fix) and the rest as arguments.

If no subcommand is given, treat it as a bootstrap run per the skill's onboarding rules.

Execute the full atlas pipeline based on the subcommand specified in the atlas skill documentation.

Notes:
- Default workspace is `~/Documents/Atlas/` unless the user specifies another path.
- Read `ATLAS.md` and `Config/GLOBAL.md` before making any change.
- Only parse a subcommand when the first word matches one of the names above; `init`, `integrate`, and `fix` accept free-form arguments.
```

Rules: the wrapper must never contain a hardcoded absolute path to the skill directory, a user home, or another machine. Use the `skill` tool by name so the wrapper resolves wherever the skill is installed. Keep `description` listing every subcommand so the host can autocomplete them.

### The Original Skill File

The skill lives as a single plain file at `<workspace>/SKILL.md` (by default `~/Documents/Atlas/SKILL.md`). It is the original, not a mirror. This is what makes Atlas portable and host-independent:

- A clone of the workspace repository contains the whole specification, so a new user can point an agent at the repo and run the initial setup.
- A user who switches agents (OpenCode to Claude Code, or anything else) and cannot reach the repo still has every rule locally. Any agent can be told "read `~/Documents/Atlas/SKILL.md`" and will have the full set of rules to follow.
- It is a plain markdown file with no tooling dependency, which keeps the Tool Agnostic principle honest.

| Step | Rule |
|---|---|
| 1. Write the original | Ensure `<workspace>/SKILL.md` exists. This is the source of truth for the installation. |
| 2. Install for the current host | Copy it into the host's skills directory so the host discovers it. For an OpenCode-style host that is `<host-config>/skills/atlas/SKILL.md`; for a Claude Code host, `.claude/skills/atlas/`. Detect the host's location, never hardcode it. |
| 3. Install the wrapper | Create the slash-command wrapper so `/atlas` is registered (see **Slash Command Wrapper**). |
| 4. Verify | Confirm the original exists, the host copy exists with valid frontmatter, and `/atlas` is offered. Report what was installed where. |

Rules: the host copy must stay byte-identical to `<workspace>/SKILL.md`, so drift is detectable with a plain `diff`. Edit the original and re-install; never edit the host copy directly. Never write the host copy into the workspace in a location the host discovers (`.opencode/skills/`, `.claude/skills/`, `.agents/skills/`) as a second same-named skill, which would produce a duplicate, unpredictable load. Installing for one host must not disturb an existing installation for another.

### Project Self-Containment Audit

The test for a correct Atlas integration is simple and non-negotiable: **a fresh `git clone` of the repository must produce a project that is fully understood and runnable, with everything it needs already inside it.** If someone clones the repo on a clean machine, reads `ATLAS.md`, and starts working, nothing essential may be missing and nothing may point outside the project.

This audit runs during `integrate` (mandatory), and on request during `resume`, `maintain`, and `audit`.

**Every asset the project depends on must live inside the project directory.** This includes, and is explicitly not limited to:

| Category | Examples |
|---|---|
| Source | all language sources, scripts, configs |
| Icons & assets | `.icns`, `.appiconset`, icon datasets, image sets, themes, sprites |
| Fonts | bundled `.ttf`, `.otf`, font atlases |
| Models & weights | ML model files, `.bin`, `.safetensors`, `.onnx`, `.gguf`, `.mlpackage`, CoreML `.mlmodelc` |
| Datasets | test fixtures, fixtures, label files, small sample data |
| Docs & evidence | specs, notes, evidence files, checklists |
| Toolbox | everything in `files/` |
| Workflow context | `ATLAS.md`, `Config/GLOBAL.md` rules the project depends on |

**Hunting assets that escaped the project.** Agents routinely create cache or download directories in the user's home or temp directory, populate them, and later move them into the project. Atlas must detect this rather than assume it never happened. At minimum check:

- Model and cache directories created outside the project: `~/.cache/huggingface/`, `~/.cache/torch/`, `~/.cache/whisper/`, `~/.ollama/`, `~/Library/Application Support/`, `~/Downloads/`, `/tmp/`.
- Any absolute path in source, configs, plists, build settings, or environment files that points outside the project and is load-bearing (a model path, an asset path, a data path).
- Every asset reference the build or runtime actually opens, not just what the README claims.

**Search for the API, not the string — the single most important rule in this audit.** A grep for a literal `/Users/` or `~/something` produces false negatives, because paths are routinely *assembled* rather than written out. This is not hypothetical: a real audit in this workspace searched for `/Users/` and returned zero findings, while the following line sat in the project, pointing at a directory that had been deleted during a relocation:

```swift
FileManager.default.homeDirectoryForCurrentUser
    .appendingPathComponent("Development/Reflex/Models")
```

It never appears as a literal absolute path. The audit then wrote "ALL rewritten" and recorded DoD as passing on that basis. The bug survived a relocation and produced a false "model not found" at runtime.

| Instead of searching for | Search for |
|---|---|
| `/Users/`, `/home/` | `homeDirectoryForCurrentUser`, `NSHomeDirectory()`, `homeDirectory`, `Path.home()`, `os.path.expanduser`, `getenv("HOME")`, `getpwuid`, `%USERPROFILE%` |
| a literal asset path | the asset's *name* (`Models`, `weights`, `.mlpackage`, `Resources`) **anywhere** in the tree, then read every hit |
| `--verify` passing | a **negative control**: break the input deliberately and confirm the check fails |

Three mandatory practices:

1. **Classify every hit by reading it.** A raw grep returns both real violations and legitimate uses (`~/Applications` when the user picks an app, `~/Desktop` as a save target). Record each one as intentional or a violation, in the evidence file. "Only two hits, both intentional" is a claim that requires reading, not counting.
2. **Assert a number.** Write the count of load-bearing paths found, and the count you fixed. "Zero remaining" is a stronger, checkable statement than "no issues found".
3. **Prove the absence behaviorally where possible.** For a path resolver, run the code and show it resolves inside the project. For a build gate, break it and show the failure. A static search is a hypothesis; a runtime result is evidence.

**Duplicated path resolvers are a defect in their own right.** If a project has more than one place that resolves the same asset, every one of them must be fixed during a relocation, and each is a chance to miss. Do not merely patch the one that grep found: consolidate to a **single resolver** and have every caller delegate to it. Then prove it at runtime. This single change is what turns "we fixed the path" into "there is only one path."

**Relocation rules for anything found outside:**

1. Move it into the project, in a sensible location (e.g. `assets/models/`, `assets/icons/`, `Resources/`), or reconfigure the project to reference an in-project path.
2. For ML model caches, prefer **relocating the cache** over copying weights, so the download path itself changes. Example for a Whisper-based macOS app: set `HF_HOME` or the relevant cache variable to an in-project directory such as `./.cache/huggingface` in the app's environment, launch config, or `.env`, instead of leaving the default at `~/.cache/huggingface` and moving files afterwards. Verify the app loads the model from the new location.
3. Prefer configuring a path over relocating data. Never move a file into a project unless the project is expected to own it.
4. If an asset cannot move (system framework, OS-provided resource, licensed content that forbids redistribution), record it in `ATLAS.md` under "Inputs & Locations" as an external dependency with its exact path and acquisition instructions, and log it to Atlas Doctor.
5. Never delete the original during relocation. Move, verify, and only propose removal after the project is confirmed working.

**Large binaries: local first, gitignored, and the user decides.** The clone rule has one deliberate exception, because a 3 GB model must not be pushed to a remote:

- Any file over roughly 50 MB, and any model weights or dataset directory, stays **local first**: kept inside the project directory but **gitignored**, with a placeholder committed in its place.
- Every gitignored heavy asset **must** be reported to the user with its path, size, and why it was excluded. Silence is a failure. Do not gitignore a large file without saying so.
- Where a committed placeholder cannot work (e.g. a directory), commit a `.gitkeep` plus a manifest at `assets/MANIFEST.md` (or the project's equivalent) recording filename, size, SHA-256, source URL, and the exact command to re-acquire it.
- The user may override local-first **only by directly asking**. If the user explicitly asks to commit a model or other heavy asset, confirm the size and remote impact first, then commit it. Do not proactively offer to upload weights, and never upload anything to a third-party remote without explicit instruction.
- Small artifacts are not exempt from the clone rule. Icons, small icon datasets, fonts, and fixture data are normally committed so the clone is complete.

**Evidence and reporting.** The audit must output a table of `Asset | Current Location | In Project? | Size | Action Taken | Gitignored?`, saved to `evidence/self-containment.txt`. Any asset left outside the project must appear in the Atlas Doctor section of `ATLAS.md` with severity `warn` or `error`, a suggested fix, and last checked (ISO 8601). Do not mark the integration complete while any load-bearing asset still lives outside the project.

**Automated audit script.** A reference implementation is provided at `Config/scripts/atlas-self-containment-audit.sh` in the Atlas workspace. When the orchestrator delegates `integrate`, `audit`, or `doctor` commands, the subagent **must** execute this script (or an equivalent API-based scan) to produce the self-containment evidence. The script searches for home-directory APIs (not literal strings), classifies each hit as intentional or violation, and outputs the required evidence table and Atlas Doctor entry. The orchestrator verifies the output contract (findings table, evidence paths, DoD with falsifiers, classification, falsification attempt) before accepting the result.
- `integrate` behavior: when integrating an existing project, create ATLAS.md if missing, move/copy only what's needed, scan for common issues (missing dirs, untracked evidence, no README, git not initialized, missing run/test, broken paths), populate the `Atlas Doctor` section with findings (Issue, Severity, Status=open, Suggested Fix, Last Checked), and propose self-healing actions (create missing dirs/files, initialize git with `main/dev/atlas/<feature>` baseline, generate INDEX.md, add evidence dir). Prefer non-destructive operations (never delete existing source unless explicitly confirmed). `integrate` **must** additionally run the full Project Self-Containment Audit below, which is a required step and not optional. 

- If a project name isn't provided for a command that requires one, prompt the user to select one from the projects located within `~/Documents/Atlas/Projects/`.
- If the Atlas workspace does not exist, initialize its global structure (`Config/` and `Projects/`) before executing the requested command. For first-time use, `/atlas` run with no arguments should be treated as a bootstrap: initialize workspace if missing, display a brief onboarding guide, and suggest next commands (`init` or `integrate`).
- No command may write code or make changes before reading all required context (ATLAS.md and GLOBAL.md) first.



---


## Branch Strategy 

Atlas utilizes a strict 3 branch model to facilitate the "agent generated work" workflow. This is to be enforced for any project that has a git repository initialized. 


| Branch | Purpose | Push Rules |
|---|---|---|
| `main` | Stable, release ready code. | Only reviewed and merged work from `dev`. Never directly pushed to by agent generated features. |
| `dev` | Integration branch. Reviewed, manually or agent assisted, work that is ready to be tested for integration. | Merges from `atlas/<feature>`. Agent work is not permitted to push directly to `dev`. |
| `atlas/<feature>` | Agent generated implementation for a specific feature. | May only be pushed to by the executing agent *after* all DoD checks pass and evidence has been gathered. | 

### Workflow
1. Plan the feature based off of `ATLAS.md`'s "Next Steps". 
2. Create and checkout `atlas/<feature>` (Feature name must be kebab-case.)
3. Implement, test, and gather evidence, strictly abiding by the project's DoD.
4. Validate every DoD check. If more than 2 checks fail on the first pass, stop, identify the process that caused it, and output a report. Do *not* blindly patch the output. 
5. Only after a full pass (all verifiable checks passing), update `ATLAS.md` (Status, Last worked on, Blockers, Next Steps), commit with a structured message, and push `atlas/<feature>`. 
6. Mark the project as needing review (e.g `needs_review: true`) within its status and registry. Manual review and merge into `dev` is required from here. `main` will only receive merges from `dev`. 

**A build log is not proof that the code compiles.** An incremental rebuild over an already-populated build directory can print a success banner while compiling nothing at all. Before recording a build check as passing, confirm the log contains real compile actions, or build from a cleared build directory. A log claiming success with zero compile actions does not support the claim: the check is **unverified**, not passed, and must be recorded that way rather than inheriting a green result from someone else's run.

**A gate must be proven to fail.** After fixing a check that could silently pass, break its input on purpose and record the failure. Then read the code again for the failure mode that survives: a status check written but discarded (`>/dev/null`, `|| true`, a variable computed but never tested), a `grep` that can legitimately match nothing, an `if` guarding the wrong condition. A gate that works by accident is one edit away from the same silent pass, which is exactly how the bug it replaced got in. 



---


## ATLAS.md Specification

Every project *must* contain an `ATLAS.md` file at its root. This is the single source of truth for said project and must be read before any action is taken for it. The following sections are required. If a section is not applicable, fill it with "N/A" as opposed to removing it. 


| Section | Description |
|---|---|
| Purpose | One line explaining the project. |
| Status | Current state. Must be one of: `Idea`, `In Progress`, `Blocked`, `Paused`, `Stuck`, `Done`, `Abandoned`. Also required: `Last worked on` (ISO 8601), `Where I left off` (1-3 sentences), `Branch` (current branch), and `needs_review` (true/false) if git is enabled. |
| Next Steps | Ordered list of the immediate next actions to take. Highest priority first. |
| Blockers | List of any blockers, including who/what is needed to resolve them. |
| Inputs & Locations | Key files, directories, environment variables, data sources, and anything required to run the project. |
| How to Run / Test | Commands to run, build, test, lint, or debug the project. Be explicit. |
| Steps | (If repeatable) Numbered, plain language steps for the workflow of this project. Avoid jargon when possible. |
| Decisions | "If X then Y" rules for recurring decisions. |
| Definition of Done | 5-10 verifiable checks. Every check must have: `Check` (action), `Evidence` (file path or method of verification, e.g `evidence/test.log`), and `Pass/Fail` to be filled after running. No vague quality words (e.g "clear", "professional", "high quality"). |
| Edge Cases | Known failures or edge cases and how to handle them. |
| Toolbox | References to files within `files/`. For each, state: file name, when to use it, and when to not use it. |
| Links | References to docs, wiki pages, issues, PR's, designs, or external resources. |
| Atlas Doctor | Health report for Atlas integration of this project. Tracks issues (missing files, broken links, uncommitted evidence, git branch mismatch, outdated status, registry drift, **assets living outside the project directory**, **missing or stale `/atlas` slash-command wrapper**, etc.), their severity (`info`/`warn`/`error`), status (`open`/`fixed`/`ignored`), suggested fix, and last checked (ISO 8601). |
| Agent Instructions | Explicit instructions for any agent working on this project. Must include: "Read this file first. Update Status, Next Steps, Blockers, and evidence before ending your session. Enforce the DoD and branch strategy. Do not assume any context." | 

When updating `ATLAS.md`, only modify the mutable sections (Status, Next Steps, Blockers, and the Pass/Fail + evidence for DoD checks) unless explicitly instructed to do maintenance (`audit` or `maintain`). 



---


## GLOBAL.md Baseline 

The global configuration, located at `Config/GLOBAL.md`, must contain at least the following. Projects may override any of these by specifying their exception within their `ATLAS.md` under "Agent Instructions" or "Decisions". 

- Baseline DoD requirements (e.g "All tests pass (evidence: evidence/tests.log)", "Changes committed with structured message", "Pushed to correct branch (if git enabled)", "ATLAS.md updated with status and next steps")
- Git conventions (branch strategy, commit message format e.g `type(scope): description - evidence: ...`)
- Evidence requirements (must be stored under `evidence/`, referenced by absolute or project relative path)
- Naming conventions (kebab-case for project names and feature names, no dates or version numbers in toolbox file names)
- Security guardrails (never save secrets/api keys/passwords to the toolbox or repo, refuse to do so and notify the user)
- Verification integrity (see **Verification Integrity** below — a check that cannot fail is not a check)

Projects that do not use git may ignore the git specific checks, however they must still abide by every other principle and requirement. 



---

## Verification Integrity

Atlas is evidence-driven, which makes the quality of the evidence the whole ballgame. A green result from a check that cannot fail is worse than no result, because it is trusted. Every command below has failed in a real workspace.

| Anti-pattern | What actually happens | Required instead |
|---|---|---|
| Grepping for a literal path | Assembled paths never match. A false "clean" audit. | Grep for the API (`homeDirectoryForCurrentUser`, `Path.home()`, `expanduser`, `getenv("HOME")`), then read every hit. |
| Counting hits | Legitimate uses (`~/Desktop` as a save target) hide the violation. | Classify each hit as intentional or a violation, and record the classification. |
| Rerunning an incremental build | Success banner, zero work done. | Clear the build directory, or confirm real compile actions in the log. |
| Trusting a prior run's green DoD | The previous agent's blind spot becomes yours. | Re-run. Then re-derive: try to falsify the result before recording Pass. |
| A status check that is discarded | `>/dev/null`, `\|\| true`, an unused variable. The gate looks present and is not. | Assert the status, then break the input and confirm the failure. |
| A new path resolver next to an existing one | Both must be fixed on every relocation; one will be missed. | One resolver. Every caller delegates. Prove it at runtime. |
| "No issues found" | Unfalsifiable. | "Zero load-bearing paths remain, 7 found and 7 fixed, 4 remaining hits classified intentional." |

**When you record a Pass, state what would have made it a Fail.** If nothing would, the check is decorative. This is the single habit that separates an audit from a summary.

**Record what you could not verify as `unverified`, never as Pass.** "No test target exists" is a legitimate verified finding when you looked and confirmed absence; "tests pass" when none exist is fabrication. Distinguish *verified absent* from *not checked* — Atlas requires the difference to be visible. 



---


## Execution Model

A single agent executing a command end-to-end is the default, but it is not sufficient for every command. One agent can lose context mid-run, drift from the spec, or produce a "clean" result from a check that cannot fail. For the commands and scenarios listed below, the orchestrator (the agent running this skill) must delegate the execution to a subagent, then review the subagent's output against a fixed contract before reporting to the user. This is not optional for the listed cases.

### When to delegate to a subagent

| Command / Scenario | Delegate? | Effort | Rationale |
|---|---|---|---|
| `status` (single project) | **No** | — | Cheap, read-only. Overhead exceeds value. |
| `status` (all projects / health sweep) | **Yes** | medium | Orchestrator fans out one subagent per project in parallel, then synthesizes a brief health overview. Matches "track every project's health". |
| `doctor` / health check on a specific project | **Yes** | medium | Executor runs the checks; orchestrator verifies by-the-book behavior. |
| `resume` | **No** | — | Needs to read ATLAS.md and propose a plan *before* any changes. Splitting this risks losing plan coherence. |
| `integrate` | **Yes** | medium | High-risk, many checks, self-containment audit is heavy. Executor does the work; orchestrator enforces Verification Integrity and falsification. |
| `audit` | **Yes** | medium | Core to self-correction. The verifier role is essential here. |
| `maintain` | **Yes** | medium | Good candidate. Keep scoped. |
| `fix` (narrow, single-issue) | **No** | — | Often targeted; orchestration adds friction. |
| `init` | **No** | — | Straightforward scaffolding; low verification ambiguity. |
| Any command when the orchestrator's context is already near its limit | **Yes** | medium | Prevents context bleeding and mid-run truncation. Delegate even if the table above says no. |

### Orchestrator responsibilities

1. **Decide scope.** Determine which project(s) the command applies to before delegating.
2. **Delegate with full context.** Pass the subagent the relevant files (ATLAS.md, GLOBAL.md, the skill spec) and the exact command with arguments. Never delegate with only a vague prompt.
3. **Enforce the output contract.** The subagent must return structured output; if it does not, ask it to re-run or produce the missing pieces. Do not accept prose-only reports for delegated commands.
4. **Review against the contract.** The orchestrator's review is brief but high-effort: it checks the contract, not the work. It does not re-execute the command wholesale.
5. **Report to the user.** After review, present the synthesized result. Flag any steering issues, nonsense, or evidence-quality problems found during review.

### Subagent output contract (mandatory for delegated commands)

Every subagent executing a delegated command must return:

1. **Findings table** — every issue, its location, and its severity.
2. **Evidence paths** — the exact file path for every claim, with the contents referenced (not just "I checked it").
3. **DoD results** — for every check: `Pass`, `Fail`, or `Unverified`. For every `Pass`, **state the falsifier** (what would have made it a Fail). If nothing would, the check is decorative and must be recorded as `Unverified`.
4. **Atlas Doctor changes** — every issue added, fixed, or ignored, with the reasoning for each.
5. **Classification of API/homedir hits** (if self-containment ran) — every hit classified as `intentional` or `violation`, with the reason.
6. **Falsification attempt** — what was attempted to be falsified and the result. "I could not falsify this" is acceptable; "it passed" without an attempted falsifier is not.
7. **Registry validation** (if `audit` or `integrate` ran) — output of `atlas-registry-validate.sh`: `VALID` or specific schema violations with paths. If invalid, the registry must be corrected and re-validated before the command completes.

### Review checklist (orchestrator, brief but high-effort)

Verify each of these. If any fails, ask the subagent to re-run or produce the missing piece. Do not re-execute the command.

- [ ] Contract complete (all seven items above present).
- [ ] API-based scanning used (not just literal string greps) where self-containment ran.
- [ ] Falsification actually attempted (not just claimed) for every Pass.
- [ ] No inherited Passes — every result re-derived in this session.
- [ ] Unverifiable checks recorded as `Unverified`, never as Pass.
- [ ] Build logs prove real work (compile actions > 0) if a build check was recorded as passed.
- [ ] Counts asserted numerically (e.g. "7 found, 7 fixed"); "no issues found" style claims absent.
- [ ] Steering check — the subagent stayed on task, did not drift into unrelated changes, and did not fabricate file writes.
- [ ] Registry validation passed — `atlas-registry-validate.sh` returned `VALID` with no schema violations.

---

## Toolbox Rules

The `files/` directory is for reusable, approved assets for a project. 

1. Only save items that will be needed again. Do *not* save one-off outputs, drafts that were not approved, or anything containing a password, API key, or secret.
2. Turn specifics into placeholders in square brackets (e.g `[api-endpoint]`) to make them reusable. Underneath said item, keep exactly one filled-in example as a reference if needed.
3. File names must be descriptive and must not contain dates or version numbers (e.g `numbers-pull.py`, `email-template.md`)
4. For every saved item, add a line to `files/INDEX.md` containing: `File Name | Purpose | Date Saved (YYYY-MM-DD)`
5. Update the "Toolbox" section of `ATLAS.md` to point to the file by name, specifying when to use it and when to not use it.
6. If any code was rebuilt from scratch during an execution that could be reused, propose saving it to the toolbox and explain why.
7. When running a job from scratch, report which saved files were used and which parts were rebuilt from nothing.



--- 


## Execution Rules 

These rules are to be strictly enforced by the agent at runtime. 

- **Deletion Safety (hard rule).** Never run a destructive command and never delete anything as a whole without the user's explicit consent for that exact target. Specifically forbidden without prior, specific user approval: `rm -rf`, `rm -r`, `rm -f`, `git clean -fdx` / `-fdX`, `git reset --hard`, `git checkout -- .`, `git branch -D`, `find -delete`, `truncate`, `shred`, `mv` over an existing path, shell redirection that empties a file (`> file`), and any recursive or glob-based delete. You must never delete a whole directory to "start clean", "regenerate", or "reinitialize" it — if regeneration is needed, move the existing copy aside to a timestamped or `*.bak` path, keep it, and tell the user where it went. You must never delete a project directory (`Projects/<name>/`), its `.git`, the workspace `SKILL.md`/`Config/`, or any index/registry without explicit consent. If a task seems to require deletion, **stop and ask**; a block of `/atlas` work is never a licence to remove files. When the user does authorize a deletion, record the exact path(s), the command run, and the user's consent line in `evidence/`. Treat any accidental destructive operation as a top-severity Atlas Doctor finding: halt, report it immediately, and attempt recovery before continuing.
- Always read `ATLAS.md` first, followed by `Config/GLOBAL.md`, prior to any modifications. 
- When resuming (`resume`), do not re-interview the user from scratch. Utilize Status, "Where I left off", Blockers, and Next Steps to form a plan and present it for confirmation before executing.
- For `init`, interview the user one question at a time if required information is missing. Do not create files until all required information to generate the project has been obtained. 
- Evidence must be written to the `evidence/` directory. Reference its path for every DoD check that utilizes it. 
- DoD Enforcement: Run every check. Fix what fails. Run them again. Output a one line pass/fail for each check with the evidence used. List any unverifiable checks and why, never assuming a pass. If > 2 checks fail on the first pass, stop and report which part of the process caused it as opposed to patching the output.
- **Inherited results are not results.** Every check runs in this session. A green DoD left by a previous agent is a claim, not evidence; re-derive it, and treat a discrepancy as a finding for Atlas Doctor rather than a reason to trust the file. `audit` in particular exists to catch the case where the prior run was wrong, so auditing by reading the evidence files proves nothing.
- **Every `audit` must attempt falsification.** Before writing a verdict, try to break each claim: run the check in reverse, break the input, or search for the thing the evidence says is absent. Record what you tried. An audit that only confirms is a transcription.
- Atlas Doctor: For any project being worked with (`init`, `integrate`, `resume`, `maintain`, `audit`, `fix`), inspect and update the `Atlas Doctor` section in `ATLAS.md`. When new issues are detected, add them as open with severity, suggested fix, and last checked (ISO 8601). When issues are resolved, mark them as `fixed` with last checked updated. Never silently ignore errors without documenting them. **Four checks are always in scope regardless of subcommand:** (1) **self-containment** - confirm no load-bearing asset lives outside the project directory and that every gitignored heavy asset has a committed placeholder or manifest; (2) **installation** - confirm the original skill file `<workspace>/SKILL.md` exists and is byte-identical to the copy installed for the current host, and that `<command-dir>/atlas.md` exists and follows host conventions; (3) **registry schema** - validate `Config/registry.json` against `Config/schema/registry.schema.json` using `atlas-registry-validate.sh`; any violation is an Atlas Doctor error. (4) **evidence freshness** - confirm every DoD `Pass` has evidence modified in this session (not inherited). Recreate or resync whatever is missing or stale.
- Self correction: If at any time the skill is insufficient to complete the task in alignment with these principles, invoke `audit` behavior (propose specific edits to the relevant rulebook with justification).
- Never fabricate file writes. If unable to write files, explicitly state this and output the full file contents to be written, the exact file name, and its destination path, reminding to replace as opposed to duplicating.
- Preserve exact formatting and indentation when utilizing the `edit` tool for markdown files. 



---


## Notes For Phase 1

- This is the MVP implementation of Atlas. All features outside of the commands listed may be deferred to later phases. 
- The registry (`Config/registry.json`) does not need to be manually edited. It is designed to be regenerated on demand by scanning `Projects/` and parsing each `ATLAS.md`'s status fields. 
- The workspace path defaults to `~/Documents/Atlas/`. If the user specifies a different workspace at any time, respect it for the current session and record it if needed.
- Atlas is designed to be learned from a git repository. The entirety of this skill is self contained within its `SKILL.md` file (the file name must be exactly `SKILL.md` inside a directory whose name matches the `name` field in the frontmatter, or the host will not discover it). An agent that has read this file possesses all of the instructions required in order to utilize Atlas for any project. 

--- 

*Atlas. Weave context back together. Resume anywhere.*

---
## EMBEDDED ASSETS - Single Source Materialization
This file is the single source of truth for Atlas. The embedded assets below may be materialized ephemerally when required.
<!-- ATLAS-EMBED path="Config/GLOBAL.md" -->
### Config/GLOBAL.md

```markdown
# Atlas Global Configuration (GLOBAL.md)

This file defines the baseline conventions for all Atlas projects. Individual projects may override these rules by explicitly stating exceptions in their `ATLAS.md` under "Decisions" or "Agent Instructions".

Location: `~/Documents/Atlas/Config/GLOBAL.md`. This directory is deliberately visible, not hidden. Read and edit it freely.

---

## Purpose

Establish consistent, tool-agnostic, local-first, and evidence-driven standards across every Atlas managed project to prevent projects from starting at 0 whilst still allowing flexibility.

---

## Git Conventions

### Branch Strategy
Atlas enforces a three branch model:

| Branch | Purpose | Push Rules |
|---|---|---|
| `main` | Stable, release ready code. | Only reviewed and merged from `dev`. Never pushed to directly by agent generated work. |
| `dev` | Integration branch for reviewed work. | Merges from `atlas/<feature>`. Agent generated work must not push directly to `dev`. |
| `atlas/<feature>` | Agent generated implementation for a specific feature. | May only be pushed after all DoD checks pass and evidence has been gathered. Feature names must be kebab-case. | 

### Commit Messages
Structured commit messages are required when committing. Format:

```
type(scope): description - evidence: <path-to-evidence>
```

Examples: `feat(auth): implement login flow - evidence: evidence/tests-auth.log`, `fix(ui): correct button alignment - evidence: evidence/visual-check.txt`

---

## Baseline Definition of Done

At least the following checks must be satisfied for every Atlas project unless overridden within its ATLAS.md. All checks must be verifiable.

| Check | Evidence | Notes |
|---|---|---|
| ATLAS.md updated with current Status, Last worked on (ISO 8601), Branch, Blockers, and Next Steps. | `evidence/atlas-status.txt` or ATLAS.md diff | Required for resume-ability. |
| All relevant tests pass. | `evidence/tests.log` | If no tests exist, explicitly verify and document in evidence with reasoning. |
| Changes committed with a structured commit message. | `evidence/commit.txt` | Must reference the commit hash if possible. |
| Pushed to the correct branch (if git enabled). | `evidence/git-push.txt` | Must be `atlas/<feature>` for agent work prior to review. |
| Required artifacts exist and paths are valid. | `evidence/artifacts-check.txt` | Validate referenced files exist. |
| No secrets, API keys, or passwords introduced. | `evidence/security-scan.txt` | Security guardrail. Refuse to save secrets. |
| Project is self-contained: every load-bearing asset lives inside the project directory, and a fresh clone reproduces the full workflow. | `evidence/self-containment.txt` | External assets must be relocated, or recorded as explicit external dependencies. |
| Every gitignored heavy asset (>~50 MB, models, datasets) was reported to the user and has a committed placeholder or manifest. | `evidence/self-containment.txt` | Silent gitignoring is a failed check. |
| `<workspace>/SKILL.md` matches the skill installed for the current host byte-for-byte. | `diff` output recorded in `evidence/audit.txt` | Prevents the installed copy drifting out of date. |
| Verification checks are re-derived in this session, not inherited from a prior green result. | `evidence/audit.txt` showing the re-run | See Verification Integrity. A prior Pass is a claim, not evidence. |

---

## Evidence Requirements

- All evidence must be stored under the project's `evidence/` directory.
- Every DoD check that utilizes proof must reference an evidence file path (project-relative when possible).
- Evidence must be concrete (logs, command output, test results, screenshots, counts, values, or file hashes). 
- Never fabricate evidence. If unverifiable, explicitly list it and state why.
- Evidence files must be committed alongside the changes that they prove when pushing to `atlas/<feature>`.


---


## Naming Conventions

- Project names: kebab-case (e.g `swift-library`, `api-gateway`)
- Feature names: kebab-case (e.g `user-auth`, `dark-mode`)
- Toolbox file names: descriptive, no dates or version numbers (e.g `email-template.md`, `numbers-pull.py`)
- Branch names: `atlas/<feature>` (kebab-case for feature)


---


## Security Guardrails

- Never save passwords, API keys, access tokens, or secrets to `files/`, `docs/`, or any committed file. Refuse to do so and notify the user.
- **Never delete.** Do not delete, remove, empty, truncate, or wholesale-overwrite any file, directory, branch, or project without the user's explicit consent for that exact target. Destructive commands (`rm -rf`, `git clean -fdx`, `git reset --hard`, `find -delete`, glob/recursive deletes, `> file`) are forbidden without prior specific approval. Prefer reversible moves; ask if unsure.
- Do not log secrets to evidence. Redact sensitive values if they must be referenced.
- Treat `.env` and similar secret files as untracked. Do not commit them. If needed, reference an `.env.example` inside of the toolbox with placeholders only.
- If unable to safely write content due to security concerns, explicitly state so and halt the action. 


---


## Atlas Doctor Baseline

The following issue categories are monitored by Atlas Doctor by default for every project:

| Category | Severity | Auto-Check |
|---|---|---|
| Missing ATLAS.md | error | On integrate/resume/maintain |
| Missing required directories (files/, docs/, evidence/, src/, tests/) | warn | On integrate/maintain |
| evidence/ is empty whilst DoD references evidence | warn | On maintain/audit |
| Git not initialized | info | On integrate |
| Incorrect branch checked out vs ATLAS.md Status.Branch | warn | On resume |
| Uncommitted changes exist | info | On resume/status |
| Broken toolbox links (referenced file does not exist) | warn | On maintain |
| INDEX.md missing or out of sync for files/ | warn | On maintain |
| Atlas Doctor section missing | warn | On integrate |
| Load-bearing asset (model, icon dataset, font, data) lives outside the project directory | error | On integrate/resume |
| Large model or dataset gitignored without notifying the user or a committed manifest placeholder | warn | On integrate/maintain |
| Absolute path outside the project in a load-bearing config, plist, or build setting | warn | On integrate/audit |
| More than one resolver for the same asset (each is an independent chance to miss one during a relocation) | warn | On integrate/audit |
| A DoD check recorded Pass whose evidence shows no actual work (e.g. a build log with zero compile actions) | error | On audit/resume |
| A verification command whose failure path is discarded (`>/dev/null`, `\|\| true`, an untested variable) | warn | On audit/maintain |
| Missing or stale `/atlas` slash-command wrapper (`atlas.md`), or wrapper contains hardcoded absolute paths | warn | On bootstrap/audit |
| Original skill file `SKILL.md` missing from the workspace root | error | On bootstrap/audit |
| Host-installed skill copy differs from the original `<workspace>/SKILL.md` (drift) | warn | On audit/bootstrap |
| Fresh-clone completeness: repo does not reproduce the full workflow context | error | On integrate/audit |
| `Config/registry.json` fails schema validation (wrong types, missing fields, truncated data) | error | On audit/integrate/maintain |
| `Config/registry.json` missing or not tracked in git | warn | On integrate |

All findings must be tracked with: `Issue`, `Severity` (info/warn/error), `Status` (open/fixed/ignored), `Suggested Fix`, and `Last Checked` (ISO 8601). 

---

## Verification Integrity

These are not DoD rows. They are the rules that decide whether the DoD rows mean anything, because a green result from a check that cannot fail is worse than no result: it gets trusted.

| Rule | Rule |
|---|---|
| Search the API, not the string | A grep for a literal `/Users/` misses paths assembled at runtime, e.g. `homeDirectoryForCurrentUser.appendingPathComponent("Project/Models")`. Grep for the home-directory APIs, then read every hit. |
| Classify, do not count | Legitimate uses (`~/Desktop` as a save target) hide the violation among the hits. Record each hit as intentional or a violation in the evidence file. |
| Assert a number | "Zero load-bearing paths remain, 7 found and 7 fixed" is checkable. "No issues found" is not. |
| A build log is not proof of a compile | An incremental rebuild prints a success banner while doing nothing. Clear the build directory, or confirm real compile actions, before recording a build check as passed. |
| Prove the gate can fail | After fixing a silent-pass check, break its input and record the failure. Then re-read for a status check that is written but discarded. |
| One resolver per asset | If several places resolve the same path, a relocation must fix all of them and one will be missed. Consolidate to a single resolver and have callers delegate. |
| Re-derive inherited results | A green DoD from a previous agent is a claim, not evidence. `audit` exists to catch the case where the prior run was wrong. |
| State the falsifier | When recording a Pass, say what would have made it a Fail. If nothing would, the check is decorative. |

**Verified absent vs. not checked.** These are different findings and Atlas requires the difference to be visible. "No test target exists, confirmed by inspecting `project.yml`" is a verified result. "Tests pass" when none exist is fabrication. Record unverifiable checks as `unverified` with the reason, never as Pass. 

--- 

*Atlas Global Baseline. Enforced by convention, never by lock-in.*```


<!-- ATLAS-EMBED path="Config/templates/ATLAS.md" -->
### Config/templates/ATLAS.md

```markdown
# ATLAS.md

## Purpose
[One line explaining this project]

---

## Status
- State: Idea
- Last worked on: [YYYY-MM-DDTHH:MM:SSZ]
- Where I left off: [1-3 sentences describing the exact stopping point]
- Branch: [e.g main/dev/atlas/<feature> or N/A]
- needs_review: false

---

## Next Steps
1. [Highest priority action]
2. [Next action]
3. [Next action]

---

## Blockers
- [Blocker 1 - include what is needed to resolve]
- [Blocker 2]

---

## Inputs & Locations
- Workspace root: [Path to this project]
- Key directories: [src/, tests/, files/, docs/, evidence/]
- Environment: [Required env vars or N/A]
- Data sources: [N/A if none]

---

## How to Run / Test
```bash
# Run
[command to run project]

# Test
[command to run tests]

# Lint/Build (if applicable)
[commands]
```

---

## Steps
1. [If repeatable, step 1]
2. [Step 2]

---

## Decisions
- If [condition] then [action].
- If [condition] then [action].

---

## Definition of Done
| Check | Evidence | Pass/Fail |
|---|---|---|
| ATLAS.md updated with current status and next steps. | evidence/atlas-status.txt | [ ] |
| All tests pass (or explicitly verified if none exist). | evidence/tests.log | [ ] |
| Changes committed with structured commit message. | evidence/commit.txt | [ ] |
| Pushed to correct branch (if git enabled). | evidence/git-push.txt | [ ] |
| No secrets introduced. | evidence/security-scan.txt | [ ] |

---

## Edge Cases
- [Known edge case + handling]
- [Known edge case + handling]

---

## Toolbox
| File | When to use | When to not use |
|---|---|---|
| [file-name.ext] | [Use case] | [When to avoid] |

---

## Links
- [Relevant doc/link]
- [Relevant doc/link]

---

## Atlas Doctor
| Issue | Severity (info/warn/error) | Status (open/fixed/ignored) | Suggested Fix | Last Checked (YYYY-MM-DDTHH:MM:SSZ) |
|---|---|---|---|---|
| Atlas integration baseline check | info | open | Verify required directories, evidence, and git state. | [YYYY-MM-DDTHH:MM:SSZ] |

---

## Agent Instructions
Read this file first. Update Status, Next Steps, Blockers, and evidence before ending your session. Enforce the DoD and branch strategy (atlas/<feature>, dev, main). Do not assume any context. If Atlas Doctor reports issues, address open items or document reasoning before completion. Utilize GLOBAL.md conventions unless explicitly overridden here.
```

<!-- ATLAS-EMBED path="Config/templates/INDEX.md" -->
### Config/templates/INDEX.md

```markdown
# Toolbox Index

| File Name | Purpose | Date Saved (YYYY-MM-DD) |
|---|---|---|
| [example-template.md] | [Description of reusable asset] | [YYYY-MM-DD] |

```

<!-- ATLAS-EMBED path="Config/templates/GLOBAL.md" -->
### Config/templates/GLOBAL.md

```markdown
# Atlas Global Configuration (GLOBAL.md)

This file defines the baseline conventions for all Atlas projects. Individual projects may override these rules by explicitly stating exceptions in their `ATLAS.md` under "Decisions" or "Agent Instructions".

Location: `~/Documents/Atlas/Config/GLOBAL.md`. This directory is deliberately visible, not hidden. Read and edit it freely.

---

## Purpose

Establish consistent, tool-agnostic, local-first, and evidence-driven standards across every Atlas managed project to prevent projects from starting at 0 whilst still allowing flexibility.

---

## Git Conventions

### Branch Strategy
Atlas enforces a three branch model:

| Branch | Purpose | Push Rules |
|---|---|---|
| `main` | Stable, release ready code. | Only reviewed and merged from `dev`. Never pushed to directly by agent generated work. |
| `dev` | Integration branch for reviewed work. | Merges from `atlas/<feature>`. Agent generated work must not push directly to `dev`. |
| `atlas/<feature>` | Agent generated implementation for a specific feature. | May only be pushed after all DoD checks pass and evidence has been gathered. Feature names must be kebab-case. | 

### Commit Messages
Structured commit messages are required when committing. Format:

```
type(scope): description - evidence: <path-to-evidence>
```

Examples: `feat(auth): implement login flow - evidence: evidence/tests-auth.log`, `fix(ui): correct button alignment - evidence: evidence/visual-check.txt`

---

## Baseline Definition of Done

At least the following checks must be satisfied for every Atlas project unless overridden within its ATLAS.md. All checks must be verifiable.

| Check | Evidence | Notes |
|---|---|---|
| ATLAS.md updated with current Status, Last worked on (ISO 8601), Branch, Blockers, and Next Steps. | `evidence/atlas-status.txt` or ATLAS.md diff | Required for resume-ability. |
| All relevant tests pass. | `evidence/tests.log` | If no tests exist, explicitly verify and document in evidence with reasoning. |
| Changes committed with a structured commit message. | `evidence/commit.txt` | Must reference the commit hash if possible. |
| Pushed to the correct branch (if git enabled). | `evidence/git-push.txt` | Must be `atlas/<feature>` for agent work prior to review. |
| Required artifacts exist and paths are valid. | `evidence/artifacts-check.txt` | Validate referenced files exist. |
| No secrets, API keys, or passwords introduced. | `evidence/security-scan.txt` | Security guardrail. Refuse to save secrets. |
| Project is self-contained: every load-bearing asset lives inside the project directory, and a fresh clone reproduces the full workflow. | `evidence/self-containment.txt` | External assets must be relocated, or recorded as explicit external dependencies. |
| Every gitignored heavy asset (>~50 MB, models, datasets) was reported to the user and has a committed placeholder or manifest. | `evidence/self-containment.txt` | Silent gitignoring is a failed check. |
| `<workspace>/SKILL.md` matches the skill installed for the current host byte-for-byte. | `diff` output recorded in `evidence/audit.txt` | Prevents the installed copy drifting out of date. |
| Verification checks are re-derived in this session, not inherited from a prior green result. | `evidence/audit.txt` showing the re-run | See Verification Integrity. A prior Pass is a claim, not evidence. |

---

## Evidence Requirements

- All evidence must be stored under the project's `evidence/` directory.
- Every DoD check that utilizes proof must reference an evidence file path (project-relative when possible).
- Evidence must be concrete (logs, command output, test results, screenshots, counts, values, or file hashes). 
- Never fabricate evidence. If unverifiable, explicitly list it and state why.
- Evidence files must be committed alongside the changes that they prove when pushing to `atlas/<feature>`.


---


## Naming Conventions

- Project names: kebab-case (e.g `swift-library`, `api-gateway`)
- Feature names: kebab-case (e.g `user-auth`, `dark-mode`)
- Toolbox file names: descriptive, no dates or version numbers (e.g `email-template.md`, `numbers-pull.py`)
- Branch names: `atlas/<feature>` (kebab-case for feature)


---


## Security Guardrails

- Never save passwords, API keys, access tokens, or secrets to `files/`, `docs/`, or any committed file. Refuse to do so and notify the user.
- **Never delete.** Do not delete, remove, empty, truncate, or wholesale-overwrite any file, directory, branch, or project without the user's explicit consent for that exact target. Destructive commands (`rm -rf`, `git clean -fdx`, `git reset --hard`, `find -delete`, glob/recursive deletes, `> file`) are forbidden without prior specific approval. Prefer reversible moves; ask if unsure.
- Do not log secrets to evidence. Redact sensitive values if they must be referenced.
- Treat `.env` and similar secret files as untracked. Do not commit them. If needed, reference an `.env.example` inside of the toolbox with placeholders only.
- If unable to safely write content due to security concerns, explicitly state so and halt the action. 


---


## Atlas Doctor Baseline

The following issue categories are monitored by Atlas Doctor by default for every project:

| Category | Severity | Auto-Check |
|---|---|---|
| Missing ATLAS.md | error | On integrate/resume/maintain |
| Missing required directories (files/, docs/, evidence/, src/, tests/) | warn | On integrate/maintain |
| evidence/ is empty whilst DoD references evidence | warn | On maintain/audit |
| Git not initialized | info | On integrate |
| Incorrect branch checked out vs ATLAS.md Status.Branch | warn | On resume |
| Uncommitted changes exist | info | On resume/status |
| Broken toolbox links (referenced file does not exist) | warn | On maintain |
| INDEX.md missing or out of sync for files/ | warn | On maintain |
| Atlas Doctor section missing | warn | On integrate |
| Load-bearing asset (model, icon dataset, font, data) lives outside the project directory | error | On integrate/resume |
| Large model or dataset gitignored without notifying the user or a committed manifest placeholder | warn | On integrate/maintain |
| Absolute path outside the project in a load-bearing config, plist, or build setting | warn | On integrate/audit |
| More than one resolver for the same asset (each is an independent chance to miss one during a relocation) | warn | On integrate/audit |
| A DoD check recorded Pass whose evidence shows no actual work (e.g. a build log with zero compile actions) | error | On audit/resume |
| A verification command whose failure path is discarded (`>/dev/null`, `\|\| true`, an untested variable) | warn | On audit/maintain |
| Missing or stale `/atlas` slash-command wrapper (`atlas.md`), or wrapper contains hardcoded absolute paths | warn | On bootstrap/audit |
| Original skill file `SKILL.md` missing from the workspace root | error | On bootstrap/audit |
| Host-installed skill copy differs from the original `<workspace>/SKILL.md` (drift) | warn | On audit/bootstrap |
| Fresh-clone completeness: repo does not reproduce the full workflow context | error | On integrate/audit |
| `Config/registry.json` fails schema validation (wrong types, missing fields, truncated data) | error | On audit/integrate/maintain |
| `Config/registry.json` missing or not tracked in git | warn | On integrate |

All findings must be tracked with: `Issue`, `Severity` (info/warn/error), `Status` (open/fixed/ignored), `Suggested Fix`, and `Last Checked` (ISO 8601). 

---

## Verification Integrity

These are not DoD rows. They are the rules that decide whether the DoD rows mean anything, because a green result from a check that cannot fail is worse than no result: it gets trusted.

| Rule | Rule |
|---|---|
| Search the API, not the string | A grep for a literal `/Users/` misses paths assembled at runtime, e.g. `homeDirectoryForCurrentUser.appendingPathComponent("Project/Models")`. Grep for the home-directory APIs, then read every hit. |
| Classify, do not count | Legitimate uses (`~/Desktop` as a save target) hide the violation among the hits. Record each hit as intentional or a violation in the evidence file. |
| Assert a number | "Zero load-bearing paths remain, 7 found and 7 fixed" is checkable. "No issues found" is not. |
| A build log is not proof of a compile | An incremental rebuild prints a success banner while doing nothing. Clear the build directory, or confirm real compile actions, before recording a build check as passed. |
| Prove the gate can fail | After fixing a silent-pass check, break its input and record the failure. Then re-read for a status check that is written but discarded. |
| One resolver per asset | If several places resolve the same path, a relocation must fix all of them and one will be missed. Consolidate to a single resolver and have callers delegate. |
| Re-derive inherited results | A green DoD from a previous agent is a claim, not evidence. `audit` exists to catch the case where the prior run was wrong. |
| State the falsifier | When recording a Pass, say what would have made it a Fail. If nothing would, the check is decorative. |

**Verified absent vs. not checked.** These are different findings and Atlas requires the difference to be visible. "No test target exists, confirmed by inspecting `project.yml`" is a verified result. "Tests pass" when none exist is fabrication. Record unverifiable checks as `unverified` with the reason, never as Pass. 

--- 

*Atlas Global Baseline. Enforced by convention, never by lock-in.*
```

<!-- ATLAS-EMBED path="Config/schema/registry.schema.json" -->
### Config/schema/registry.schema.json

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "properties": {
    "generated_at": { "type": "string", "format": "date-time" },
    "workspace": { "type": "string" },
    "projects": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "name": { "type": "string" },
          "status": { "enum": ["Idea", "In Progress", "Blocked", "Paused", "Stuck", "Done", "Abandoned"] },
          "needs_review": { "type": "boolean" },
          "last_worked_on": { "type": "string", "format": "date-time" },
          "branch": { "type": "string" }
        },
        "required": ["name", "status", "needs_review", "last_worked_on", "branch"]
      }
    }
  },
  "required": ["generated_at", "workspace", "projects"]
}
```

<!-- ATLAS-EMBED path="Config/scripts/atlas-self-containment-audit.sh" -->
### Config/scripts/atlas-self-containment-audit.sh

```bash
#!/usr/bin/env bash
# atlas-self-containment-audit.sh
# Self-containment audit for Atlas projects.
# Searches for home-directory APIs (not literal paths), classifies hits,
# and outputs evidence/self-containment.txt with a findings table.
#
# Usage: atlas-self-containment-audit.sh <project-root>
#   <project-root> - path to the project directory containing ATLAS.md

set -euo pipefail

PROJECT_ROOT="${1:?Usage: $0 <project-root>}"
EVIDENCE_DIR="${PROJECT_ROOT}/evidence"
ATLAS_MD="${PROJECT_ROOT}/ATLAS.md"

mkdir -p "${EVIDENCE_DIR}"

EVIDENCE_FILE="${EVIDENCE_DIR}/self-containment.txt"
TIMESTAMP=$(date -u +"%Y-%m-%dT%H:%M:%SZ")

# API patterns to search for (platform-agnostic)
API_PATTERN='homeDirectoryForCurrentUser|NSHomeDirectory\(\)|homeDirectory|Path\.home\(\)|os\.path\.expanduser|getenv\(["'"'"']HOME["'"'"']\)|getpwuid|%USERPROFILE%|expanduser|user_home_dir|dirs::home_dir|std::env::home_dir|System\.getProperty\("user\.home"\)|System\.getenv\("HOME"\)'

# Asset name patterns - combined
ASSET_PATTERN='Models|weights|mlpackage|Resources|assets|model|checkpoint|safetensors|onnx|gguf|mlmodelc'

# Exclude patterns for grep - directories to skip (build, vendor, deps)
EXCLUDE_DIRS=(
    --exclude-dir=.git
    --exclude-dir=evidence
    --exclude-dir=.venv
    --exclude-dir=venv
    --exclude-dir=__pycache__
    --exclude-dir=node_modules
    --exclude-dir=.idea
    --exclude-dir=.vscode
    --exclude-dir=build
    --exclude-dir=dist
    --exclude-dir=target
    --exclude-dir=DerivedData
    --exclude-dir=.build
    --exclude-dir=checkouts
    --exclude-dir=Pods
    --exclude-dir=.cocoapods
    --exclude-dir=Carthage
    --exclude-dir=vendor
    --exclude-dir=.gradle
    --exclude-dir=.mvn
)

# Include only source files (not build artifacts, configs, etc.)
INCLUDE_FILES=(
    --include="*.swift"
    --include="*.py"
    --include="*.js"
    --include="*.ts"
    --include="*.jsx"
    --include="*.tsx"
    --include="*.json"
    --include="*.yaml"
    --include="*.yml"
    --include="*.toml"
    --include="*.md"
    --include="*.sh"
    --include="*.rb"
    --include="*.go"
    --include="*.rs"
    --include="*.kt"
    --include="*.scala"
    --include="*.cs"
    --include="*.java"
    --include="*.cpp"
    --include="*.cc"
    --include="*.cxx"
    --include="*.c"
    --include="*.h"
    --include="*.hpp"
    --include="*.m"
    --include="*.mm"
    --include="*.plist"
    --include="*.xib"
    --include="*.storyboard"
)

echo "# Self-Containment Audit" > "${EVIDENCE_FILE}"
echo "" >> "${EVIDENCE_FILE}"
echo "**Project:** ${PROJECT_ROOT}" >> "${EVIDENCE_FILE}"
echo "**Timestamp:** ${TIMESTAMP}" >> "${EVIDENCE_FILE}"
echo "" >> "${EVIDENCE_FILE}"
echo "## API-Based Scan Results" >> "${EVIDENCE_FILE}"
echo "" >> "${EVIDENCE_FILE}"
echo "| File | Line | Matched Pattern | Classification | Notes |" >> "${EVIDENCE_FILE}"
echo "|------|------|-----------------|----------------|-------|" >> "${EVIDENCE_FILE}"

total_hits=0
violations=0
intentional=0

# Search for API patterns - single grep pass
while IFS=: read -r file line content; do
    if [[ -z "${file}" ]]; then
        continue
    fi
    ((total_hits++))
    
    # Extract which pattern matched
    matched_pattern=""
    for p in homeDirectoryForCurrentUser NSHomeDirectory homeDirectory Path.home os.path.expanduser getenv getpwuid USERPROFILE expanduser user_home_dir dirs::home_dir std::env::home_dir System.getProperty System.getenv; do
        if [[ "${content}" =~ ${p} ]]; then
            matched_pattern="${p}"
            break
        fi
    done
    [[ -z "${matched_pattern}" ]] && matched_pattern="home-dir-api"
    
    # Classification heuristic (conservative per updated rules)
    classification="intentional"
    notes="manual review - home-dir API usage (likely user-facing or docs)"

    # Intentional cases
    # 1. File ends with .md (docs)
    if [[ "${file}" =~ \.md$ ]]; then
        classification="intentional"
        notes="documentation/markdown (likely non-load-bearing)"
        ((intentional++))
    # 2. expanduser called on user input (args.*expanduser())
    elif [[ "${content}" =~ (args\.[a-zA-Z_][a-zA-Z0-9_]*\.expanduser|expanduser.*args) ]]; then
        classification="intentional"
        notes="expanduser on user input/args (user-facing)"
        ((intentional++))
    # 3. NSHomeDirectory() + "/Applications" (app discovery)
    elif [[ "${content}" =~ NSHomeDirectory.*Applications|Applications.*NSHomeDirectory ]]; then
        classification="intentional"
        notes="user-facing app discovery (Applications)"
        ((intentional++))
    # 4. User paths to Downloads/Documents/Desktop when clearly user-facing
    elif [[ "${content}" =~ (Downloads|Documents|Desktop) ]]; then
        classification="intentional"
        notes="user-facing path (Downloads/Documents/Desktop)"
        ((intentional++))
    # 5. Test/example contexts (common keywords)
    elif [[ "${content}" =~ (test|example|Example|TEST) ]]; then
        classification="intentional"
        notes="test/example context"
        ((intentional++))
    else
        # Check if it's a violation: references load-bearing project assets AND constructs from home dir in app source
        # Load-bearing project assets: Models, model weights dirs, .mlpackage, actual asset paths
        if [[ "${content}" =~ (Models|weights|\.mlpackage|safetensors|onnx|gguf|checkpoint|mlmodelc) ]]; then
            # Avoid counting docs already handled; also avoid vendor/deps (we already skip them)
            classification="violation"
            notes="Load-bearing project asset path constructed from home directory in source"
            ((violations++))
        else
            # Default to intentional as per conservative rules
            classification="intentional"
            notes="manual review - home-dir API usage (likely user-facing or docs)"
            ((intentional++))
        fi
    fi
    
    # Escape pipe characters for markdown table
    file_escaped="${file//|/\\|}"
    line_escaped="${line//|/\\|}"
    pattern_escaped="${matched_pattern//|/\\|}"
    content_escaped="${content//|/\\|}"
    notes_escaped="${notes//|/\\|}"
    
    echo "| ${file_escaped} | ${line_escaped} | ${pattern_escaped} | ${classification} | ${notes_escaped} |" >> "${EVIDENCE_FILE}"
done < <(grep -r -n -i -E "${API_PATTERN}" "${PROJECT_ROOT}" "${EXCLUDE_DIRS[@]}" "${INCLUDE_FILES[@]}" 2>/dev/null || true)

echo "" >> "${EVIDENCE_FILE}"
echo "## Summary" >> "${EVIDENCE_FILE}"
echo "" >> "${EVIDENCE_FILE}"
echo "- **Total API hits found:** ${total_hits}" >> "${EVIDENCE_FILE}"
echo "- **Classified as violations:** ${violations}" >> "${EVIDENCE_FILE}"
echo "- **Classified as intentional:** ${intentional}" >> "${EVIDENCE_FILE}"
echo "- **Remaining load-bearing paths outside project:** ${violations}" >> "${EVIDENCE_FILE}"

# Asset name scan - single grep pass
echo "" >> "${EVIDENCE_FILE}"
echo "## Asset Name Scan (supplementary)" >> "${EVIDENCE_FILE}"
echo "" >> "${EVIDENCE_FILE}"
echo "| File | Line | Matched Asset | Context |" >> "${EVIDENCE_FILE}"
echo "|------|------|---------------|---------|" >> "${EVIDENCE_FILE}"

asset_hits=0
while IFS=: read -r file line content; do
    if [[ -z "${file}" ]]; then
        continue
    fi
    ((asset_hits++))
    if [[ ${asset_hits} -gt 50 ]]; then
        break
    fi
    
    matched_asset=""
    for a in Models weights mlpackage Resources assets model checkpoint safetensors onnx gguf mlmodelc; do
        if [[ "${content}" =~ ${a} ]]; then
            matched_asset="${a}"
            break
        fi
    done
    [[ -z "${matched_asset}" ]] && matched_asset="asset"
    
    file_escaped="${file//|/\\|}"
    line_escaped="${line//|/\\|}"
    asset_escaped="${matched_asset//|/\\|}"
    content_escaped="${content//|/\\|}"
    echo "| ${file_escaped} | ${line_escaped} | ${asset_escaped} | ${content_escaped} |" >> "${EVIDENCE_FILE}"
done < <(grep -r -n -i -E "${ASSET_PATTERN}" "${PROJECT_ROOT}" "${EXCLUDE_DIRS[@]}" "${INCLUDE_FILES[@]}" 2>/dev/null || true)

echo "" >> "${EVIDENCE_FILE}"
echo "- **Asset name hits (sample, max 50):** ${asset_hits}" >> "${EVIDENCE_FILE}"

# Atlas Doctor entry generation
DOCTOR_ENTRY="| Self-containment audit: ${violations} load-bearing path(s) outside project | ${violations} | open | Relocate assets into project or configure in-project paths; consolidate to single resolver per asset | ${TIMESTAMP} |"

echo "" >> "${EVIDENCE_FILE}"
echo "## Atlas Doctor Entry" >> "${EVIDENCE_FILE}"
echo "" >> "${EVIDENCE_FILE}"
echo "${DOCTOR_ENTRY}" >> "${EVIDENCE_FILE}"

# Print summary to stdout for the orchestrator
echo "SELF_CONTAINMENT_AUDIT_SUMMARY"
echo "total_hits=${total_hits}"
echo "violations=${violations}"
echo "intentional=${intentional}"
echo "evidence_file=${EVIDENCE_FILE}"
echo "doctor_entry=${DOCTOR_ENTRY}"
```

<!-- ATLAS-EMBED path="Config/scripts/atlas-registry-validate.sh" -->
### Config/scripts/atlas-registry-validate.sh

```bash
#!/usr/bin/env bash
# atlas-registry-validate.sh
# Validates Config/registry.json against the JSON Schema.
# Outputs validation errors as Atlas Doctor entries.

set -euo pipefail

WORKSPACE_ROOT="${1:?Usage: $0 <workspace-root>}"
REGISTRY_FILE="${WORKSPACE_ROOT}/Config/registry.json"
SCHEMA_FILE="${WORKSPACE_ROOT}/Config/schema/registry.schema.json"

if [[ ! -f "${REGISTRY_FILE}" ]]; then
    echo "REGISTRY_VALIDATION_SUMMARY"
    echo "status=missing"
    echo "error=registry.json not found"
    exit 0
fi

if [[ ! -f "${SCHEMA_FILE}" ]]; then
    echo "REGISTRY_VALIDATION_SUMMARY"
    echo "status=skipped"
    echo "error=schema not found"
    exit 0
fi

# Use python for JSON schema validation
python3 -c "
import json, sys, jsonschema
from jsonschema import validate, Draft7Validator

with open('${REGISTRY_FILE}') as f:
    registry = json.load(f)
with open('${SCHEMA_FILE}') as f:
    schema = json.load(f)

validator = Draft7Validator(schema)
errors = list(validator.iter_errors(registry))

if errors:
    for err in errors:
        path = '.'.join(str(p) for p in err.path)
        print(f'ERROR: {path or \"root\"}: {err.message}')
    sys.exit(1)
else:
    print('VALID')
" 2>&1

if [[ $? -eq 0 ]]; then
    echo "REGISTRY_VALIDATION_SUMMARY"
    echo "status=valid"
else
    echo "REGISTRY_VALIDATION_SUMMARY"
    echo "status=invalid"
fi
```

---

## MATERIALIZATION CONTRACT — Single Source Expansion

**This file is the single source of truth.** All Atlas configuration, templates, schema, and scripts live inline above, marked with `ATLAS-EMBED` delimiters. The on-disk `Config/` directory is a derived cache, not a second source.

### Delimiter Convention
Each embedded asset is wrapped as:
```markdown
<!-- ATLAS-EMBED path="Config/scripts/atlas-self-containment-audit.sh" -->
### Config/scripts/atlas-self-containment-audit.sh

```bash
...content...
```
```
The `path` attribute is the destination relative to the workspace root (`~/Documents/Atlas/` by default).

### When to Materialize
The agent materializes assets **only when a command needs them**:
- **Bootstrap / first run:** create `Config/`, `Config/templates/`, `Config/scripts/`, `Config/schema/` from embedded blocks; write `Config/GLOBAL.md`, templates, schema, and scripts; init git; install host copy + wrapper.
- **Self-containment audit (`integrate`, `audit`, `doctor`):** materialize `Config/scripts/atlas-self-containment-audit.sh` to a temp dir, make executable, run it with the project root, then clean up (ephemeral).
- **Registry validation (`audit`, `integrate`, `maintain`):** materialize `Config/scripts/atlas-registry-validate.sh` and `Config/schema/registry.schema.json` to temp dirs, run validation, clean up.
- **Project init (`init`):** materialize `Config/templates/ATLAS.md`, `Config/templates/INDEX.md`, `Config/templates/GLOBAL.md` into the new project.

### Materialization Rules
1. **File wins on drift.** If the on-disk file exists and its SHA-256 differs from the embedded content, log a drift warning, regenerate from the file, and continue.
2. **Ephemeral for scripts.** Scripts are materialized to `mktemp -d`, executed, then the temp dir is removed. No persistent copies.
3. **Cache for templates/Config.** `Config/GLOBAL.md`, `Config/templates/*`, `Config/schema/*` are written to disk as a cache. They are regenerated from the file on any mismatch.
4. **Registry state is live.** `Config/registry.json` is maintained on disk as project state (it is not embedded). The inline schema validates it.
5. **Host installation.** The host skill copy (`~/.config/opencode/skills/atlas/SKILL.md`) and the slash-command wrapper are the only non-ephemeral external writes. They must stay byte-identical to this file.

### Implementation Sketch (Agent Responsibility)
When any Atlas command runs:
1. Parse this file for `ATLAS-EMBED` blocks.
2. Build an in-memory map: `dest_path -> {content, fence, executable}`.
3. For each `Config/` asset the command needs:
   - If it's a script: write to temp, `chmod +x`, use, delete.
   - If it's a template/schema/GLOBAL: ensure parent dir exists, write to workspace `Config/` (overwrite on mismatch), use from there.
6. On first run (no `Config/`): write all non-script blocks to `Config/` as the initial cache.
7. Report any drift to Atlas Doctor (category: "installation drift").

### Drift Detection
```bash
# Embedded content hash (precomputed or computed on the fly)
# On-disk file hash
# If different: warn, overwrite from embedded, continue
```

This contract makes the single `.md` file the only artifact you need to copy, share, or version. The workspace is a runtime cache that regenerates from it.

---

*Atlas. Weave context back together. Resume anywhere.*
