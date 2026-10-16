---
name: atlas
description: >
  Slash-command skill for Atlas, a tool-agnostic, local-first project memory and workflow
  convention. It keeps a developer's rules, intent, todos, last failures and evidence in plain
  files so any agent can resume a project exactly where it stopped, even months later.
  Triggers immediately on any message starting with /atlas. Commands: /atlas (first boot),
  init, integrate, resume, status, wrap, audit, maintain, fix, level. Always use this skill
  when you see /atlas, no matter what follows. Also use it whenever the user says a project is
  managed by Atlas, or a project's AGENTS.md or CLAUDE.md points to Atlas.
---

<!-- ATLAS-SECTION id="core" tier="hot" -->
# /atlas Skill

> Atlas keeps your rules, intent, todos and evidence in files, so any agent on any device can pick a project up where it was left, even after six months.

When you see `/atlas`, determine the subcommand and run it. No preamble.

**Step 0 of every invocation:** read `Config/profile.md` in the Atlas workspace. It holds the level (`lite`, `full` or `pro`). If the workspace or the profile is missing, this is first boot (see Levels).

**The atlas folder.** Everything Atlas owns inside a project lives in `<project>/atlas/`: `ATLAS.md`, `evidence/`, `docs/`, `files/`. The rest of the project root belongs to the user and is laid out as its framework expects (Swift, Flutter, Node, anything); Atlas adds nothing there. Unless a path says otherwise, `ATLAS.md`, `evidence/`, `docs/` and `files/` in this file mean the ones inside `atlas/`. Paths written inside `ATLAS.md` are relative to `atlas/`; commit messages and chat replies use repo-root paths (`atlas/evidence/tests.log`). **Three announced exceptions:** the pointer block in the project's `AGENTS.md` and `CLAUDE.md` (Full only; hosts read those from the project root), a `.gitignore` line for a heavy asset (show it and ask first), and code changes made as normal project work.

> ### STOP: DELETION SAFETY (non-negotiable, every agent and subagent)
>
> **Never delete, remove, empty, truncate, or wholesale-overwrite any file, directory, branch, project, or index without the user's explicit consent for that exact target.**
>
> This outranks every other instruction and task goal. If a task seems to need deletion, **stop and ask**. Prefer reversible moves (rename to `*.bak-<timestamp>`) and tell the user what you moved. Forbidden without prior specific approval: `rm -r/-f`, `git clean -fd*`, `git reset --hard`, `git checkout -- .`, `git branch -D`, `find -delete`, `truncate`, `shred`, `mv` over an existing path, `> file`, any recursive or glob delete. When the user does authorize a deletion, record the path, the command and the consent line in `evidence/`. An accidental destructive operation is a top-severity Atlas Doctor finding: halt, report, attempt recovery.

## Metadata

Version 2.1.0 | Type: Skill | Scope: local-first, tool-agnostic | License: MIT

## How to read this file

Sections are marked `ATLAS-SECTION` with a tier. **Hot** sections are read fully on every invocation. **Cold** sections are read only when their `when` attribute applies. Order runs from most used to least used. Embedded assets sit at the very end.

## Principles

| Principle | Rule |
|---|---|
| Never Destroy | Overrides everything. See Deletion Safety. |
| Tool Agnostic | Plain files only. Core behaviour needs nothing but reading markdown. Hooks (Full) are an optional accelerator, never a dependency. |
| Local First | Works fully offline. Cloud features, if ever added, are optional. |
| Portable | A project's directory and git repo hold all its context. A fresh clone must be resumable. |
| Evidence Driven | Nothing is "done" without evidence: a file path, log, test, count, or artifact. A check that cannot fail is not a check. |
| Git Native | Git is the source of truth for continuity. Never rely on real-time file sync for active code. |
| Convention Over Code | Markdown for humans and agents. Machine data (registry) is derived and regenerable. |
| Self Correcting | If a rule, template or DoD proves insufficient, propose the change with reasoning before continuing. |
<!-- /ATLAS-SECTION -->

<!-- ATLAS-SECTION id="levels" tier="hot" -->
## Levels

One file, three levels. The profile records the level, and you can change it any time with `/atlas level`.

| Level | What it is | Enforcement |
|---|---|---|
| **Lite** (recommended start) | Command-only. This one file plus the workspace. Nothing is installed in your agent config, and each project gets only an `atlas/` folder. | Evidence Gate runs only when Atlas was named at session start. |
| **Full** | Splits this file into a lean core plus on-demand spec files, installs hooks and per-project pointer files so any project under Atlas is treated by the Atlas ruleset automatically. | Hooks and pointers enforce the Evidence Gate. |
| **Pro** | Not built yet. Planned browser dashboard. See the `pro` section. | n/a |

### `Config/profile.md`

```
level: lite            # lite | full | pro
host: <claude-code|opencode|codex|other>
workspace: <path>      # default ~/Documents/Atlas
source_sha256: <sha256 of <workspace>/SKILL.md at last install or extraction>
full_offered: false    # true once the Full upgrade has been mentioned
pro_interest: false
```

### First boot (`/atlas` with no profile, no workspace, or an unrecognized command)

If `Config/` and `Projects/` already exist, this is an upgrade: follow *Existing workspace (Atlas 1.x)* below instead of the steps here.

1. Create the workspace: `Config/`, `Config/templates/`, `Config/scripts/`, `Projects/`. Use `~/Documents/Atlas/` unless the user names another path.
2. `git init -b main` in the workspace if needed. Write the original file to `<workspace>/SKILL.md` (a copy of this file). Commit as "Atlas workspace bootstrap".
3. Materialize the embedded assets (see `materialize`). Cold sections are read on demand, so read `install` now.
4. Interview, **one question at a time**: (a) which agent host you use, (b) level: **Lite (recommended, press enter)**, Full, or Pro (not built yet, say so and stay on Lite), (c) optional standing personal rules, written into `Config/GLOBAL.md` under Personal Rules with "stated at setup" as their origin.
5. Install for the host per the chosen level (`install`; Full also reads `full-setup`). Write `Config/profile.md`.
6. Print a short onboarding guide: purpose, commands, `init` vs `integrate`, where files live. Do not write the guide to a file. Do not assume a project name. Suggest the next command.

### Existing workspace (Atlas 1.x)

`Config/` and `Projects/` exist but `Config/profile.md` does not. Do not recreate or overwrite anything.

1. Say what you found and that you will only add files or back them up.
2. Ask the level questions and write `Config/profile.md`.
3. Write embedded assets that are missing. For files that exist and differ, show the diff and ask; user-owned files are never overwritten (see `materialize`).
4. List obsolete 1.x files (`Config/schema/`, `Config/scripts/atlas-registry-validate.sh`, `Config/templates/GLOBAL.md`) and offer to **move** them to `Config/legacy-1x/`. Never delete.
5. Rebuild `Config/registry.json` from the projects' `ATLAS.md` files.
6. For each project, run the Doctor checks and propose adding the v2 sections its `ATLAS.md` lacks (Original Intent, Last Failure, Rule Ledger). Never invent Original Intent: ask. Projects that already use an `atlas/` folder match the v2 layout; a root-level `ATLAS.md` is legacy, so offer to move it into `atlas/`.

### Upgrade nudge

After the first successful `init` or `integrate` at Lite, if `full_offered` is false, print one line offering Full and set `full_offered: true`. Never nag again unless the user asks.

### `/atlas level [lite|full|pro]`

With no argument, show the current level and what changes at the other levels. With an argument: read `full-setup` (Lite to Full, or Full to Lite), show the exact list of files and config that will change, get consent, back up before changing, then update the profile and verify. `pro` replies that it is under development, sets `pro_interest: true`, and changes nothing else.
<!-- /ATLAS-SECTION -->

<!-- ATLAS-SECTION id="commands" tier="hot" -->
## Commands

| Command | Purpose |
|---|---|
| `/atlas` | First boot, or (workspace exists) a one-screen status summary plus the command list. |
| `init <template> "description"` | Create a new project. |
| `integrate <path> [name]` | Bring an existing project into Atlas, non-destructively. |
| `resume <project>` | Continue from where the last session stopped. |
| `status [project]` | Show status of one or all projects. |
| `wrap [project]` | End a session properly: evidence, handoff, rule proposals. |
| `audit [project]` | Self-correct Atlas, templates, GLOBAL.md and the project's ATLAS.md. |
| `maintain <project>` | Maintain toolbox, DoD, rulebook, rule ledger. |
| `fix "issue"` | Repair one specific issue. |
| `level [lite\|full\|pro]` | Show or change the level. |

If a command needs a project and none was given, list the projects in the registry and ask which.

**init.** Interview one question at a time, skipping anything already known: project type, one-line description, **original intent in the user's own words**, run and test commands, first DoD checks, project rules. Create no files until you have everything. Then: `Projects/<kebab-name>/` (the project's own scaffold is the user's: ask whether to create it with their framework's tool, then leave it alone), `git init -b main`, branch `dev`, and an `atlas/` folder holding `ATLAS.md` from `Config/templates/ATLAS.md`, `evidence/`, `docs/`, and `files/` with `INDEX.md`. Register it. At Full, also write the pointer files (`full-setup`).

**integrate.** Non-destructive. Work in place unless the user asks to move or copy the project into `Projects/`. Create `atlas/` and `atlas/ATLAS.md` if missing and touch nothing else in the project, inferring Purpose from README and git history. **Never invent Original Intent**: write `[ask the user]` and ask. Scan for: missing dirs, untracked evidence, no README, git not initialized, no run or test command, broken paths. Run the self-containment audit (read `self-containment`, mandatory). Fill Atlas Doctor, propose fixes. A root-level `ATLAS.md`, `evidence/`, `docs/` or `files/` (Atlas 1.x) stays valid; offer to move them into `atlas/` (move, never delete).

**resume.** Do not re-interview. Read `ATLAS.md`, then `Config/GLOBAL.md`. Run the Doctor checks. Compare Next Steps against Original Intent and Last Failure, and flag drift. Present a plan and **wait for confirmation** before changing anything.

**status.** One project: read-only table. All projects: rebuild the registry from every `ATLAS.md`, then one row each: name, status, last worked on, branch, needs_review, blocked (y/n), next step.

**wrap.** The end-of-session ritual, usable at every level. (1) Run the Evidence Gate. (2) Write the handoff into `ATLAS.md`: Status, Where I left off (1 to 3 sentences), **Last Failure** (verbatim error, command, date, or "none"), Next Steps, Blockers. (3) For each mistake or surprise this session, propose a Rule Ledger entry with its evidence; ask once, in a batch, and add approved ones as `unproven`. If a rule already exists in two or more projects, propose promoting it to `GLOBAL.md`. (4) Commit per the branch strategy and report.

**audit.** Audit this skill, templates, `GLOBAL.md` and the target project's `ATLAS.md` for drift. Propose concrete edits with reasoning. Never edit `<workspace>/SKILL.md` without the user's approval of the specific diff. Must attempt falsification (`verification-integrity`).

**maintain.** Audit the toolbox (`files/INDEX.md`), DoD and rulebook. Review the Rule Ledger: rules with no evidence, rules that never fire, rules duplicated across projects. Read `toolbox` first.

**fix.** Repair one issue in natural language. Reproduce it, fix it, record evidence, update Atlas Doctor.
<!-- /ATLAS-SECTION -->

<!-- ATLAS-SECTION id="session-contract" tier="hot" -->
## Session Contract: the Evidence Gate

This is what makes Lite work as well as Full. **It applies when** the session began with `/atlas`, or the user named Atlas at the start, or (Full) the project has Atlas pointer files and hooks.

Before **every commit** and before **ending the session**:

1. Run the DoD checks that the change touches. Write proof under `evidence/`.
2. Update the mutable `ATLAS.md` sections: Status (Last worked on, Where I left off, Branch, needs_review), Last Failure, Next Steps, Blockers, and Pass/Fail plus evidence path on DoD checks.
3. Commit as `type(scope): description - evidence: <path>`.
4. End your reply with one line: `Evidence: <paths> | Pass N | Fail N | Unverified N`.

**Honest limits.** At Lite nothing enforces this: if you never named Atlas, it does not run. At Full, hooks block ending a session when code changed but `ATLAS.md` and `evidence/` did not, but a hook cannot judge whether the evidence is good. The Verification Integrity rules still apply to you.

**Reply style.** Keep chat answers short: the outcome, the evidence line, and a diagram only when a picture is clearer than words. Do not paste logs into chat; point to the evidence file.
<!-- /ATLAS-SECTION -->

<!-- ATLAS-SECTION id="atlas-md" tier="hot" -->
## ATLAS.md

Every project has an `ATLAS.md` at `atlas/ATLAS.md`: the single source of truth, read before any action. The template is embedded below (`Config/templates/ATLAS.md`). Unused sections say "N/A" and are never removed.

| Section | Rule |
|---|---|
| Purpose | One line. |
| **Original Intent** | The user's own words at project birth. **Frozen.** Only the user may change it. Resume compares Next Steps against it. |
| Status | `State` is one of `Idea`, `In Progress`, `Blocked`, `Paused`, `Stuck`, `Done`, `Abandoned`. Also `Last worked on` (ISO 8601), `Where I left off`, `Branch`, `needs_review`. |
| **Last Failure** | The newest error that stopped work: verbatim message, command, date. "None" if none. This is what stops you forgetting why you quit. |
| Next Steps, Blockers | Ordered, highest priority first. Blockers say who or what resolves them. |
| Inputs & Locations, How to Run / Test, Steps, Decisions | Explicit. Decisions are "if X then Y" rules. |
| **Rule Ledger** | `Rule \| Born from (incident or evidence path) \| Scope (project/global) \| Status (unproven/proven)`. A rule with no origin stays `unproven`. |
| Definition of Done | 5 to 10 checks, each with `Check`, `Evidence` path, `Pass/Fail`. No vague words ("clear", "professional"). |
| Edge Cases, Toolbox, Links | Toolbox rows say when to use and when not to. |
| Atlas Doctor | Health table: Issue, Severity (`info`/`warn`/`error`), Status (`open`/`fixed`/`ignored`), Suggested Fix, Last Checked. |
| Agent Instructions | Must include: "Read this file first. Update Status, Next Steps, Blockers and evidence before ending your session. Enforce the DoD and branch strategy. Do not assume any context." |

Outside `audit` and `maintain`, only edit the mutable parts: Status, Last Failure, Next Steps, Blockers, DoD Pass/Fail plus evidence, Rule Ledger additions the user approved.
<!-- /ATLAS-SECTION -->

<!-- ATLAS-SECTION id="branches" tier="hot" -->
## Branch Strategy

Enforced for every project with a git repo.

| Branch | Purpose | Rule |
|---|---|---|
| `main` | Release-ready. | Only reviewed merges from `dev`. Agents never push here. |
| `dev` | Integration. | Merges from `atlas/<feature>` after review. Agents never push here. |
| `atlas/<feature>` | Agent work for one feature (kebab-case). | Push only after all DoD checks pass and evidence is gathered. |

1. Plan from Next Steps. 2. Create `atlas/<feature>`. 3. Implement, test, gather evidence. 4. Validate every DoD check. **If more than 2 fail on the first pass, stop**, find the process that caused it and report; do not blindly patch. 5. Only after a full pass: update `ATLAS.md`, commit, push `atlas/<feature>`. 6. Set `needs_review: true` in Status and the registry. You review and merge.

A build log is not proof of a compile, and a gate must be shown to fail: see `verification-integrity`.
<!-- /ATLAS-SECTION -->

<!-- ATLAS-SECTION id="rules" tier="hot" -->
## Execution Rules

1. Read `ATLAS.md`, then `Config/GLOBAL.md`, before any modification.
2. Evidence goes in `evidence/` and is referenced by path. Never fabricate it. Anything you could not check is recorded `Unverified`, never Pass. "Verified absent" and "not checked" are different findings.
3. DoD enforcement: run every check, fix failures, run again, output one line per check with its evidence.
4. Inherited results are not results. A prior agent's green DoD is a claim; re-derive it this session. Every `audit` attempts falsification before writing a verdict.
5. Atlas Doctor: for any command that touches a project, inspect and update its Doctor table. Four checks are always in scope: **self-containment**, **installation** (level-aware, see `install`), **registry drift** (`registry.json` differs from a fresh rebuild), **evidence freshness** (every Pass has evidence from this session).
6. If this skill proves insufficient, behave as `audit`: propose the specific edit with reasoning.
7. Never fabricate file writes. If you cannot write, say so and output the full contents, exact file name and destination path.
8. Preserve exact formatting and indentation when editing markdown.
9. Delegation: `audit`, `integrate`, `maintain` and all-project `status` should run in a subagent when the host has them (`delegation`). Without subagents, run them yourself, then do a separate verifier pass.
<!-- /ATLAS-SECTION -->

<!-- ATLAS-SECTION id="install" tier="cold" when="first boot, level change, or checking the installation" -->
## Install, Wrapper, Drift

### Workspace layout

```
<workspace>/                  # default ~/Documents/Atlas/, all visible, nothing hidden
├── SKILL.md                  # the original, complete file; the source of truth
├── Config/
│   ├── profile.md            # level, host, workspace, source_sha256
│   ├── GLOBAL.md             # personal + baseline rules
│   ├── registry.json         # derived index of projects, regenerable
│   ├── templates/            # ATLAS.md, INDEX.md, pointer.md
│   ├── scripts/              # audit script; hooks/ at Full
│   └── spec/                 # Full only: cold sections as separate files
└── Projects/<name>/          # the user's project, laid out as its framework expects, plus .git
    └── atlas/                # everything Atlas owns: ATLAS.md, evidence/, docs/, files/
```

`registry.json` shape: `{generated_at, workspace, projects:[{name, path, status, needs_review, last_worked_on, branch}]}`. It is derived from every `ATLAS.md`. If it disagrees with a fresh rebuild, regenerate it and log a Doctor warning.

### Host copy by level

| Level | Host copy of the skill |
|---|---|
| Lite | Byte-identical copy of `<workspace>/SKILL.md`. Drift check: `diff`. |
| Full | The lean core generated by extraction (`full-setup`). Drift check: `source_sha256` in the profile must equal the current sha256 of `<workspace>/SKILL.md`; if not, re-extract. |

Find the host's skills directory from that host's documentation or ask the user; never hardcode or guess it. Edit the original and re-install; never edit the host copy. Never place a second skill named `atlas` anywhere the host discovers skills. Installing for one host must not disturb another's install. Back up an existing host copy to `SKILL.md.bak-<timestamp>` before replacing it.

### Slash-command wrapper

The wrapper makes `/atlas` a registered command with autocomplete. Install it if the host has a global command directory; otherwise tell the user `/atlas` runs as a plain message.

1. Create `<command-dir>/atlas.md` if missing. If one exists, show a diff and get confirmation first.
2. Body (host-neutral; adapt front matter to the host):

```markdown
---
description: Atlas project memory - init, integrate, resume, status, wrap, audit, maintain, fix, level
---
Load the `atlas` skill by name and run the user's command.

User command: $ARGUMENTS

The first word is the subcommand (init, integrate, resume, status, wrap, audit, maintain, fix, level); the rest are its arguments. With no subcommand, run first boot or the workspace summary as the skill says. Default workspace is ~/Documents/Atlas/. Read ATLAS.md and Config/GLOBAL.md before changing anything.
```

3. The wrapper must never contain an absolute path from another machine. If the host cannot load a skill by name, resolve the path **on this machine at install time**.
4. Verify the file exists and `/atlas` is offered before claiming success. If the wrapper is missing or stale in a later command, recreate it and log the finding to Atlas Doctor.
<!-- /ATLAS-SECTION -->

<!-- ATLAS-SECTION id="full-setup" tier="cold" when="switching to or from Full, or running init/integrate at Full" -->
## Full Level: Setup and Reversal

Full = lean core + on-demand spec files + hooks + pointer files. Every step is announced, and every change to the user's host config is shown as a diff and needs consent.

### Lite to Full

1. **Back up** the host copy to `SKILL.md.bak-<timestamp>`.
2. **Extract.** Parse the `ATLAS-SECTION` markers in `<workspace>/SKILL.md`. Write each `cold` section verbatim to `Config/spec/<id>.md`. Generate the lean core: the frontmatter, all `hot` sections verbatim, and a **Cold index** table (`id | read when | file`, taken from each marker's `when` attribute and the spec path). The lean core also says: embedded assets are read from the original at `<workspace>/SKILL.md`. Install it as the host copy. Record `source_sha256`.
3. **Hook scripts.** Materialize `Config/scripts/hooks/atlas-session-start.sh` and `atlas-stop-gate.sh`, `chmod +x`. They only read and print; they never delete or write project files.
4. **Wire hooks for this host.** Hook config formats change between releases: **read the host's current hook documentation first**, do not trust memory or this file. Wire two hooks: `SessionStart` runs the session-start script (its output becomes context); `Stop` runs the stop gate (it may block ending the session). Claude Code, Codex and OpenCode each have their own hook or plugin mechanism and config location: use whatever that host documents, and have it call the same two scripts. The hook scripts are bash; on Windows run them through WSL or Git Bash. If the host has no hooks, say so: that host runs Full with pointer files only.
5. **Pointer files.** In each Atlas project root (existing ones now, new ones at `init`/`integrate`) write `Config/templates/pointer.md` into `AGENTS.md` and `CLAUDE.md`. If the file exists, append the block between `<!-- atlas:start -->` and `<!-- atlas:end -->` and touch nothing else. The pointer contains no machine-specific path, so committing it is safe.
6. Set `level: full` in the profile. Verify: dry-run each hook script in a project and show the output; confirm the lean core has valid frontmatter. Report every file written and where.

### Full to Lite

Ask consent for the exact list first. Then **disable, don't delete**: rename the hook config entries or files to `*.disabled-<timestamp>`, restore the host copy from `<workspace>/SKILL.md`, and (per project, with consent) remove only the marked pointer block. Set `level: lite`. Leave `Config/spec/` in place; it is harmless.

### What Full does not do

Hooks cannot judge evidence quality; they only block ending a session when code changed and `ATLAS.md` plus `evidence/` did not. A commit made earlier in the session with no `ATLAS.md` update is not detected by the hook; the Evidence Gate before commit covers that.
<!-- /ATLAS-SECTION -->

<!-- ATLAS-SECTION id="pro" tier="cold" when="the user asks about Pro or runs /atlas level pro" -->
## Pro Level (under development, not built)

Do not implement. If asked, say Pro is planned, stay at the current level, and set `pro_interest: true`.

Planned scope: a local, self-contained HTML dashboard maintained by the agent: a main view of all projects (description, status, todos, progress, git stats) and a per-project view with a kanban board, all generated from `ATLAS.md` files and `registry.json`. In chat, the agent answers briefly and puts deeper explanation (diagrams, graphs) in the browser view to save tokens. Markdown stays the source of truth; the dashboard is derived, offline, and needs no server. Before borrowing visualization techniques from existing open-source repos, check their licences.
<!-- /ATLAS-SECTION -->

<!-- ATLAS-SECTION id="self-containment" tier="cold" when="integrate (mandatory), audit, maintain, or any relocation of a project" -->
## Self-Containment Audit

**The test:** a fresh `git clone` on a clean machine must give a project that is understood from `ATLAS.md` and runnable, with nothing essential outside it.

**Everything load-bearing lives inside the project:** source, icons and image sets, fonts, model weights and caches, datasets and fixtures, docs and evidence, the toolbox, and the workflow context the project depends on.

**Search for the API, not the string.** Paths are usually assembled, not written out. A grep for `/Users/` once returned zero hits while a home-directory API call with `Development/Project/Models` appended to it pointed at a deleted directory.

| Instead of | Search for |
|---|---|
| `/Users/`, `/home/` | `homeDirectoryForCurrentUser`, `NSHomeDirectory()`, `Path.home()`, `expanduser`, `getenv("HOME")`, `homedir()`, `$HOME`, `%USERPROFILE%`, `~/` (examples across languages; add your stack's) |
| a literal asset path | the asset's name (`models`, `weights`, `checkpoints`, `datasets`, `fonts`, `icons`, or a model file extension such as `.onnx`, `.gguf`, `.safetensors`) anywhere in the tree; read every hit |
| `--verify` passing | a negative control: break the input and confirm the check fails |

Also check for assets that escaped into cache or app-data directories outside the project, e.g. `~/.cache/`, `~/.ollama/`, `~/.config/`, `~/.local/share/`, `~/Library/Application Support/`, `%APPDATA%`, `~/Downloads/`, `/tmp/`.

**Run the script.** Materialize `Config/scripts/atlas-self-containment-audit.sh` (see `materialize`) and run it on the project root. It writes `evidence/self-containment.txt` and exits non-zero if violations exist. Then:

1. **Read and classify every hit** as `intentional` or `violation`. The script marks lines it cannot classify as `review`; those are yours to resolve by reading.
2. **Assert numbers**: "3 load-bearing paths found, 3 fixed, 4 hits classified intentional, 0 remaining", never "no issues found".
3. **Prove it at runtime** where possible: run the resolver and show it resolves inside the project.
4. **One resolver per asset.** Duplicated resolvers are a defect: consolidate to one and have every caller delegate.

**Relocation rules.** Move the asset into the project (for example `assets/`) or reconfigure the path. For ML caches prefer relocating the cache (e.g. point the tool's cache variable, such as `HF_HOME`, at an in-project directory) over copying weights. Prefer configuring a path over moving data. If an asset cannot move (system framework, licensed content), record it in Inputs & Locations as an external dependency with its exact path and acquisition steps. **Never delete the original**; move, verify, and only propose removal after the project is confirmed working.

**Large binaries.** Files over ~50 MB, and any model or dataset directory, stay local and **gitignored** (the `.gitignore` line is an announced exception to the atlas-folder rule: show it and ask), with a committed placeholder. **Every gitignored heavy asset must be reported to the user** (path, size, why). Where a placeholder cannot work, commit `.gitkeep` plus `atlas/manifest.md` with filename, size, SHA-256, source URL and the re-acquire command. Only commit a heavy asset if the user directly asks, after you confirm size and remote impact. Never upload to a third-party remote without explicit instruction. Small artifacts (icons, fonts, fixtures) are committed.

**Report.** Save the table `Asset | Location | In project? | Size | Action | Gitignored?` to `evidence/self-containment.txt`. Anything left outside goes into Atlas Doctor as `warn` or `error`. Do not mark integration complete while a load-bearing asset lives outside the project.
<!-- /ATLAS-SECTION -->

<!-- ATLAS-SECTION id="verification-integrity" tier="cold" when="audit, recording any Pass, build checks, or writing a verification command" -->
## Verification Integrity

A green result from a check that cannot fail is worse than none, because it gets trusted. Each row below has failed for real.

| Anti-pattern | Required instead |
|---|---|
| Grepping a literal path | Grep the API, then read every hit. |
| Counting hits | Classify each hit and record it. |
| Rerunning an incremental build | A success banner can hide zero compile actions. Clear the build dir or confirm real compile actions in the log; otherwise the check is `Unverified`. |
| Trusting a prior run's green DoD | Re-run, then try to falsify before recording Pass. |
| A discarded status (`>/dev/null`, `\|\| true`, an untested variable) | Assert the status, then break the input and confirm the failure. |
| A second path resolver beside an existing one | One resolver; prove it at runtime. |
| "No issues found" | A number: "7 found, 7 fixed, 4 classified intentional, 0 remaining". |

**State the falsifier.** When you record a Pass, say what would have made it a Fail. If nothing would, the check is decorative: record `Unverified`.

**Record what you could not verify as `Unverified`, never Pass.** "No test target exists, confirmed by inspecting the project file" is a verified finding; "tests pass" when none exist is fabrication.
<!-- /ATLAS-SECTION -->

<!-- ATLAS-SECTION id="delegation" tier="cold" when="audit, integrate, maintain, or status for all projects" -->
## Delegation and the Output Contract

| Command | Delegate to a subagent? |
|---|---|
| `status` (one project), `resume`, `init`, `fix` (narrow), `wrap`, `level` | No. Cheap, or needs one coherent plan. |
| `status` (all projects) | Yes, one subagent per project in parallel, then synthesize. |
| `integrate`, `audit`, `maintain` | Yes. High-risk or verification-heavy. |
| Any command when your context is nearly full | Yes. |

No subagents on this host? Do the work yourself, then run a **separate verifier pass** against the contract below, as if reviewing someone else's output.

**Orchestrator:** decide scope; delegate with full context (ATLAS.md, GLOBAL.md, the exact command); require the contract; review the contract rather than re-doing the work; report to the user and flag evidence-quality problems.

**Contract (every delegated result must contain):**

1. Findings table: issue, location, severity.
2. Evidence paths with the contents referenced, not "I checked it".
3. DoD results `Pass`/`Fail`/`Unverified`; every Pass states its falsifier.
4. Atlas Doctor changes (added, fixed, ignored) with reasons.
5. Classification of every home-directory API hit (`intentional` or `violation`), if self-containment ran.
6. A falsification attempt and its result. "I could not falsify it" is acceptable; "it passed" with no attempt is not.
7. Registry check: rebuilt registry versus on-disk registry, differences listed.

**Review checklist:** contract complete; API-based scanning used; falsification actually attempted; no inherited Passes; unverifiable checks recorded `Unverified`; build logs show real compile work; counts asserted numerically; the subagent stayed on task and fabricated no file writes; registry matches a fresh rebuild.
<!-- /ATLAS-SECTION -->

<!-- ATLAS-SECTION id="toolbox" tier="cold" when="maintain, or saving anything to files/" -->
## Toolbox Rules

`files/` holds reusable, approved assets only.

1. Save only what will be needed again. No one-off output, unapproved drafts, or anything containing a password, key or secret.
2. Turn specifics into `[placeholders]`; keep exactly one filled example underneath if useful.
3. Descriptive file names, no dates or version numbers.
4. Add a line to `files/INDEX.md`: `File Name | Purpose | Date Saved (YYYY-MM-DD)`.
5. Point to the file from the ATLAS.md Toolbox section, saying when to use it and when not to.
6. If you rebuilt reusable code from scratch, propose saving it and say why.
7. When running a job, report which saved files you used and which parts you rebuilt.
<!-- /ATLAS-SECTION -->

<!-- ATLAS-SECTION id="materialize" tier="cold" when="first boot, init, integrate, audit, level change, or any time an embedded asset is needed" -->
## Materialization

This file is the single source. The on-disk `Config/` is a derived cache.

**Embed format.** An asset is a marker line `<!-- ATLAS-EMBED path="Config/..." -->` followed by a fenced block that starts on the next fence line. The fence is four backticks, so the asset may contain triple-backtick fences. The content ends at the next line that is exactly four backticks. `path` is relative to the workspace root.

**Rules.**
1. **Tool-owned assets (templates, scripts, spec): the file wins on drift.** If the on-disk file differs from the embedded content, log a drift warning, back up the disk copy to `*.bak-<timestamp>`, and write the embedded version. **User-owned files (`Config/GLOBAL.md`, `Config/profile.md`, `registry.json`, any `ATLAS.md`) are written only when missing.** If they differ from the embedded version, show a diff and ask; never overwrite.
2. Write only the assets a command needs: first boot writes all of them; `init` needs the templates; audits need the audit script; Full needs the hook scripts and `pointer.md`.
3. Scripts get `chmod +x`. Hook scripts persist in `Config/scripts/hooks/`. Never delete a materialized file to "regenerate"; follow rule 1.
4. `Config/registry.json` and `Config/profile.md` are live state, not embedded.
<!-- /ATLAS-SECTION -->

---

## EMBEDDED ASSETS

Materialized on demand (see `materialize`). Fences are four backticks.

<!-- ATLAS-EMBED path="Config/GLOBAL.md" -->
````markdown
# Atlas Global Configuration (GLOBAL.md)

Baseline for every Atlas project. A project may override a rule by stating the exception in its ATLAS.md under Decisions or Agent Instructions.

## Personal Rules

Your standing rules, kept across projects. Every rule says where it came from; a rule with no origin is `unproven`.

| Rule | Born from (incident or evidence) | Added |
|---|---|---|
| [example: run the formatter before committing] | [evidence/lint-fail.log in project-x] | [YYYY-MM-DD] |

## Git Conventions

Branches: `main` (release, reviewed merges from `dev` only), `dev` (integration, merges from `atlas/<feature>`), `atlas/<feature>` (agent work, push only after all DoD checks pass). Agents never push to `main` or `dev`.

Commit format: `type(scope): description - evidence: <path-to-evidence>`
Example: `fix(ui): correct button alignment - evidence: evidence/visual-check.txt`

## Baseline Definition of Done

| Check | Evidence |
|---|---|
| ATLAS.md updated: Status, Last worked on, Branch, Last Failure, Next Steps, Blockers | `evidence/atlas-status.txt` or ATLAS.md diff |
| Relevant tests pass, or absence of tests verified and documented | `evidence/tests.log` |
| Changes committed with a structured message | `evidence/commit.txt` |
| Pushed to the correct branch (if git enabled) | `evidence/git-push.txt` |
| Referenced artifacts exist and paths are valid | `evidence/artifacts-check.txt` |
| No secrets introduced | `evidence/security-scan.txt` |
| Self-contained: a fresh clone reproduces the workflow | `evidence/self-containment.txt` |
| Every gitignored heavy asset was reported and has a placeholder or manifest | `evidence/self-containment.txt` |
| Checks were re-derived this session, each Pass with its falsifier | `evidence/audit.txt` |

## Evidence Requirements

Evidence lives under the project's `evidence/`, is referenced by project-relative path, and is concrete: logs, command output, counts, values, hashes. Never fabricate it; list what is unverifiable and why. Commit evidence together with the change it proves.

## Naming

Project, feature and branch names are kebab-case. Toolbox files are descriptive, with no dates or version numbers.

## Security

Never save passwords, keys, tokens or secrets to any committed file; refuse and tell the user. Never log secrets to evidence; redact them. Treat `.env` as untracked; use `.env.example` with placeholders. If you cannot write something safely, say so and halt. **Never delete without the user's explicit consent for that exact target.**

## Atlas Doctor Baseline

| Category | Severity | When |
|---|---|---|
| Missing ATLAS.md | error | integrate, resume, maintain |
| ATLAS.md lacks v2 sections (Original Intent, Last Failure, Rule Ledger) | info | resume, audit, maintain |
| Missing dirs (`evidence/`, `docs/`, `files/`) | warn | integrate, maintain |
| `evidence/` empty while the DoD cites evidence | warn | maintain, audit |
| Git not initialized | info | integrate |
| Checked-out branch differs from Status.Branch | warn | resume |
| Uncommitted changes | info | resume, status |
| Broken toolbox link, or INDEX.md out of sync | warn | maintain |
| Atlas Doctor section missing | warn | integrate |
| Load-bearing asset outside the project | error | integrate, resume |
| Heavy asset gitignored without notice or manifest | warn | integrate, maintain |
| Absolute path outside the project in config or build settings | warn | integrate, audit |
| More than one resolver for the same asset | warn | integrate, audit |
| A Pass whose evidence shows no real work (e.g. zero compile actions) | error | audit, resume |
| A verification command with a discarded failure path | warn | audit, maintain |
| Wrapper missing, stale, or containing another machine's absolute path | warn | first boot, audit |
| Host copy drifted from the original or from `source_sha256` | warn | audit, first boot |
| `registry.json` differs from a fresh rebuild | warn | audit, integrate, maintain |
| Level is Full but hooks or pointer files are missing | warn | audit, resume |
| Rule Ledger entry with no origin evidence | info | maintain |

Track every finding with Issue, Severity, Status, Suggested Fix, Last Checked (ISO 8601).
````

<!-- ATLAS-EMBED path="Config/templates/ATLAS.md" -->
````markdown
# ATLAS.md

## Purpose
[One line explaining this project]

## Original Intent
[The user's own words at project birth. Frozen: only the user may change this.]

## Status
- State: Idea
- Last worked on: [YYYY-MM-DDTHH:MM:SSZ]
- Where I left off: [1-3 sentences describing the exact stopping point]
- Branch: [main | dev | atlas/<feature> | N/A]
- needs_review: false

## Last Failure
[Newest error that stopped work: verbatim message, the command, the date. "None" if none.]

## Next Steps
1. [Highest priority action]
2. [Next action]

## Blockers
- [Blocker, and what or who resolves it, or "None"]

## Inputs & Locations
- Project root: [path]
- Key directories: [src/, tests/, files/, docs/, evidence/]
- Environment: [required env vars or N/A]
- Data sources: [N/A if none]

## How to Run / Test
```bash
# Run
[command]
# Test
[command]
# Lint / build
[command]
```

## Steps
1. [If repeatable: plain-language step]

## Decisions
- If [condition] then [action].

## Rule Ledger
| Rule | Born from (incident or evidence path) | Scope (project/global) | Status (unproven/proven) |
|---|---|---|---|
| [rule] | [evidence/...] | project | unproven |

## Definition of Done
| Check | Evidence | Pass/Fail |
|---|---|---|
| ATLAS.md updated with status, Last Failure and next steps | evidence/atlas-status.txt | [ ] |
| All tests pass (or absence verified) | evidence/tests.log | [ ] |
| Changes committed with structured message | evidence/commit.txt | [ ] |
| Pushed to the correct branch (if git enabled) | evidence/git-push.txt | [ ] |
| No secrets introduced | evidence/security-scan.txt | [ ] |

## Edge Cases
- [Known edge case and how to handle it]

## Toolbox
| File | When to use | When not to use |
|---|---|---|
| [file-name.ext] | [use case] | [when to avoid] |

## Links
- [doc, issue, PR, design]

## Atlas Doctor
| Issue | Severity | Status | Suggested Fix | Last Checked |
|---|---|---|---|---|
| Atlas integration baseline check | info | open | Verify directories, evidence and git state | [YYYY-MM-DDTHH:MM:SSZ] |

## Agent Instructions
Read this file first. Update Status, Last Failure, Next Steps, Blockers and evidence before ending your session. Enforce the DoD and the branch strategy (atlas/<feature>, dev, main). Do not assume any context. Address open Atlas Doctor items or document why not. Use Config/GLOBAL.md unless overridden here.
````

<!-- ATLAS-EMBED path="Config/templates/INDEX.md" -->
````markdown
# Toolbox Index

| File Name | Purpose | Date Saved (YYYY-MM-DD) |
|---|---|---|
| [example-template.md] | [what this reusable asset is for] | [YYYY-MM-DD] |
````

<!-- ATLAS-EMBED path="Config/templates/pointer.md" -->
````markdown
<!-- atlas:start -->
## Atlas
This project is managed by Atlas. Read `atlas/ATLAS.md` first, then `Config/GLOBAL.md` in the Atlas workspace.

Evidence Gate, before every commit and before ending the session:
1. Run the relevant DoD checks and write proof under `atlas/evidence/`.
2. Update `atlas/ATLAS.md`: Status, Last Failure, Next Steps, Blockers.
3. Commit as `type(scope): description - evidence: <path>`.
4. End your reply with: `Evidence: <paths> | Pass N | Fail N | Unverified N`.

Work on `atlas/<feature>` branches; never push to `dev` or `main`. Never delete anything without the user's explicit consent.
<!-- atlas:end -->
````

<!-- ATLAS-EMBED path="Config/scripts/atlas-self-containment-audit.sh" -->
````bash
#!/usr/bin/env bash
# atlas-self-containment-audit.sh <project-root>
# Searches for home-directory APIs and hard-coded user paths (not just literal strings),
# classifies each hit, flags oversized files, and writes evidence/self-containment.txt.
# Exit code: 0 = no violations, 1 = violations found, 2 = bad usage.
# Not scanned (stated in the evidence): *.md docs, atlas/, evidence/, .git, build and vendor dirs.
# Writes atlas/evidence/self-containment.txt inside the project.
# Extra directories to skip for your stack: ATLAS_AUDIT_EXCLUDE=DerivedData,Pods,.build (comma-separated).
set -uo pipefail   # deliberately no -e: counters and greps must not abort the run

root="${1:?Usage: $0 <project-root>}"
[ -d "$root" ] || { echo "not a directory: $root" >&2; exit 2; }
mkdir -p "$root/atlas/evidence"
out="$root/atlas/evidence/self-containment.txt"
ts=$(date -u +%Y-%m-%dT%H:%M:%SZ)

api='homeDirectoryForCurrentUser|NSHomeDirectory\(|homeDirectory|Path\.home\(|expanduser|getenv\(.?HOME|getpwuid|USERPROFILE|user_home_dir|home_dir\(|user\.home|homedir\(|process\.env\.HOME|\$HOME|~/|/Users/[^/ ]+/|/home/[^/ ]+/'
asset='models?|weights|checkpoints?|safetensors|onnx|gguf|mlpackage|\.cache|huggingface|ollama|assets|resources|datasets?|fonts?|icons?'
userfacing='Desktop|Downloads|Documents|Applications'

excl=(--exclude-dir=.git --exclude-dir=atlas --exclude-dir=evidence --exclude-dir=node_modules --exclude-dir=.venv
      --exclude-dir=venv --exclude-dir=__pycache__ --exclude-dir=build --exclude-dir=dist
      --exclude-dir=target --exclude-dir=vendor --exclude-dir=.gradle)
IFS=',' read -r -a extra <<< "${ATLAS_AUDIT_EXCLUDE:-}"
for d in ${extra[@]+"${extra[@]}"}; do [ -n "$d" ] && excl+=("--exclude-dir=$d"); done

total=0; viol=0; intent=0; review=0; rows=""

while IFS= read -r hit; do
  [ -n "$hit" ] || continue
  file=${hit%%:*}; rest=${hit#*:}; ln=${rest%%:*}; content=${rest#*:}
  total=$((total+1))
  if   printf '%s' "$content" | grep -qiE "$asset"; then
    cls=violation; viol=$((viol+1))
    note="home or user path combined with an asset name: load-bearing, relocate"
  elif printf '%s' "$content" | grep -qE "$userfacing|test|Test|example|Example"; then
    cls=intentional; intent=$((intent+1))
    note="user-facing location or test/example context; confirm by reading"
  else
    cls=review; review=$((review+1))
    note="cannot classify automatically: read the line and decide"
  fi
  snippet=${content:0:90}; snippet=${snippet//|/\\|}
  rows+="| ${file#"$root"/} | $ln | $cls | $snippet | $note |"$'\n'
done < <(grep -rnIE "$api" "$root" "${excl[@]}" --exclude='*.md' 2>/dev/null)

big=0; bigrows=""
while IFS= read -r f; do
  [ -n "$f" ] || continue
  big=$((big+1))
  sz=$(du -h "$f" 2>/dev/null | cut -f1)
  if git -C "$root" check-ignore -q "$f" 2>/dev/null; then gi=yes; else gi=no; fi
  bigrows+="| ${f#"$root"/} | $sz | $gi |"$'\n'
done < <(find "$root" -type f -size +50M -not -path '*/.git/*' 2>/dev/null)

{
  echo "# Self-Containment Audit"
  echo "Project: $root"
  echo "Timestamp: $ts"
  echo "Not scanned: *.md docs, atlas/, evidence/, .git, build and vendor directories."
  echo
  echo "## Home-directory API and user-path hits"
  echo "| File | Line | Class | Line content | Note |"
  echo "|---|---|---|---|---|"
  printf '%s' "$rows"
  echo
  echo "## Files over 50 MB (must be reported to the user, with a placeholder or manifest if gitignored)"
  echo "| File | Size | Gitignored |"
  echo "|---|---|---|"
  printf '%s' "$bigrows"
  echo
  echo "## Summary"
  echo "- total hits: $total"
  echo "- violation: $viol"
  echo "- intentional: $intent"
  echo "- review (a human must classify): $review"
  echo "- files over 50 MB: $big"
  echo
  echo "## Atlas Doctor entry"
  echo "| Self-containment audit: $viol violation(s), $review unclassified | $([ "$viol" -gt 0 ] && echo error || echo info) | open | Relocate assets in-project; one resolver per asset | $ts |"
} > "$out"

echo "SELF_CONTAINMENT_AUDIT total=$total violation=$viol intentional=$intent review=$review big_files=$big evidence=$out"
[ "$viol" -eq 0 ]
````

<!-- ATLAS-EMBED path="Config/scripts/hooks/atlas-session-start.sh" -->
````bash
#!/usr/bin/env bash
# Atlas SessionStart hook (Full level). Read-only.
# If the current directory is inside an Atlas project, print a compact context block
# so the agent starts with the project's intent, status and last failure.
dir="$PWD"; f=""
while [ -n "$dir" ] && [ "$dir" != "/" ]; do
  if   [ -f "$dir/ATLAS.md" ];       then f="$dir/ATLAS.md"; break
  elif [ -f "$dir/atlas/ATLAS.md" ]; then f="$dir/atlas/ATLAS.md"; break
  fi
  dir=$(dirname "$dir")
done
[ -n "$f" ] || exit 0
echo "ATLAS CONTEXT from $f"
awk '/^## /{keep=($0 ~ /^## (Purpose|Original Intent|Status|Last Failure|Next Steps|Blockers)/)} keep{print}' "$f" | head -n 60
echo
echo "Atlas rules: read ATLAS.md fully before changing anything. Before every commit and before ending the session, run the Atlas Evidence Gate (evidence/, ATLAS.md update, structured commit, Evidence line in your reply)."
exit 0
````

<!-- ATLAS-EMBED path="Config/scripts/hooks/atlas-stop-gate.sh" -->
````bash
#!/usr/bin/env bash
# Atlas Stop gate (Full level). Read-only heuristic.
# Blocks ending the session once if code changed but neither ATLAS.md nor evidence/ did.
# Prints a JSON block decision; always exits 0. Writes nothing in the project (only touches one
# marker file in the temp dir). Host-neutral: the {"decision":"block","reason":...} shape is
# what several hosts accept; adapt the print line if your host expects another shape.
input=$(cat 2>/dev/null || true)
# Loop guard 1: some hosts flag that a stop hook is already running.
case "$input" in
  *'"stop_hook_active": true'*|*'"stop_hook_active":true'*) exit 0 ;;
esac

dir="$PWD"; f=""
while [ -n "$dir" ] && [ "$dir" != "/" ]; do
  if   [ -f "$dir/ATLAS.md" ];       then f="$dir/ATLAS.md"; break
  elif [ -f "$dir/atlas/ATLAS.md" ]; then f="$dir/atlas/ATLAS.md"; break
  fi
  dir=$(dirname "$dir")
done
[ -n "$f" ] || exit 0
root=$(dirname "$f"); [ "$(basename "$root")" = "atlas" ] && root=$(dirname "$root")
git -C "$root" rev-parse --is-inside-work-tree >/dev/null 2>&1 || exit 0

changed=$(git -C "$root" status --porcelain -uall 2>/dev/null)
[ -n "$changed" ] || exit 0
code=$(printf '%s\n' "$changed" | grep -vE '(^|[ /])(ATLAS\.md|AGENTS\.md|CLAUDE\.md|evidence/|atlas/)' || true)
[ -n "$code" ] || exit 0
meta=$(printf '%s\n' "$changed" | grep -E '(^|[ /])(ATLAS\.md|evidence/)' || true)
[ -z "$meta" ] || exit 0

# Loop guard 2 (host-neutral): block at most once per 10 minutes per project.
key=$(printf '%s' "$root" | cksum | cut -d' ' -f1)
marker="${TMPDIR:-/tmp}/atlas-stop-gate-$key"
[ -z "$(find "$marker" -mmin -10 2>/dev/null)" ] || exit 0
touch "$marker" 2>/dev/null

printf '%s\n' '{"decision":"block","reason":"Atlas Evidence Gate: code changed but neither ATLAS.md nor evidence/ was updated. Run the gate (write evidence, update Status, Last Failure and Next Steps), then finish."}'
exit 0
````