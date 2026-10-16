# Atlas Context Tracker Project

> **NOTE ON INCEPTION**: This project tracks the usage and development of the Atlas skill/system itself. While this creates an inherent "Atlas tracking Atlas" situation that may seem confusing at first, this self-referential approach is intentional - it allows the Atlas system to dogfood its own capabilities, validate its effectiveness through self-observation, and improve through fact-based self-analysis.

---

## Purpose
Local-first project context tracking system that enables agents and humans to:
- Track live project context and ruleset evolution
- Maintain human-readable status reporting and to-do lists
- Establish foundation for multi-project tracking and kanban views
- Apply fact-checking and mathematical standards to self-improvement processes

---

## Original Intent

Local-first project context tracking system that enables agents and humans to:
- Track live project context and ruleset evolution
- Maintain human-readable status reporting and to-do lists
- Establish foundation for multi-project tracking and kanban views
- Apply fact-checking and mathematical standards to self-improvement processes

---

## Status
| Field | Value |
|-------|-------|
| Status | `In Progress` |
| Last worked on | 2026-10-16 |
| Branch | `atlas/atlas-upgrade-v2` |
| needs_review | true |
| Where I left off | Upgraded to the v2 ATLAS.md sections (Original Intent, Last Failure, Rule Ledger, Agent Instructions), fixed Status.Branch, then restored the root layout: `SKILL.md` moved out of `Atlas-source/` back to the repo root and the stale root `ATLAS.md` deletion staged, so only Atlas-owned files live in `atlas/`. Repo base is `master` (no `dev`/`main`); work is on `atlas/atlas-upgrade-v2`. |

---

## Last Failure

None. No error stopped the last session; the project was left mid-initialization with a clean record.

---

## Next Steps
1. **Begin Atlas skill development** - Start making improvements to the SKILL.md file based on identified needs and feedback
2. **Implement status reporting mechanism** - Ensure human-readable updates to Status section
3. **Establish fact-check protocol** - Create process for validating claims with evidence and mathematical reasoning
4. **Design extension points** - Prepare structure for later kanban/multi-project tracking features
5. **Document development process** - Record how we develop and improve the skill through this tracking system

---

## Inputs & Locations
Everything load-bearing lives inside the project directory. The single source of truth for project context is the ATLAS.md file itself, which evolves as the living ruleset.

Paths below are relative to this `atlas/` directory; framework-native files (the skill source, test dir) sit at the project root.

| Input | Location | Committed |
|-------|----------|-----------|
| Project rulebook and context tracker | `ATLAS.md` | yes |
| Skill source code (when developing Atlas) | `../SKILL.md` | yes |
| Toolbox scripts and templates | `files/` | yes |
| Design specs and documentation | `docs/` | yes |
| Verifiable evidence (logs, test results, etc.) | `evidence/` | yes |
| Test files | `../tests/` | yes |
| Git repository | `../.git/` | yes |

1. **Read ATLAS.md** - Always start by reading this file to understand current context and ruleset
2. **Update ATLAS.md** - As work progresses, update the Status, Where I left off, and Next Steps sections
3. **Gather evidence** - Place verifiable evidence in the evidence/ directory (build logs, test results, measurements, etc.)
4. **Apply fact checks** - For any claim, provide supporting evidence and mathematical reasoning where applicable
5. **Update ruleset** - Evolve the ATLAS.md file as understanding improves and processes are refined

---

## Steps
The loop for tracking this Atlas context tracker project:

1. **Read ATLAS.md** - Confirm understanding of current context and ruleset
2. **Work session** - Perform tracking, update ruleset, gather evidence
3. **Update ATLAS.md** - Modify Status, Where I left off, and Next Steps based on session outcomes
4. **Add evidence** - Place verifiable results in evidence/ directory with clear labeling
5. **Apply fact check protocol** - Validate claims with evidence and mathematical reasoning where applicable
6. **Plan next session** - Update Next Steps section with prioritized actions

---

## Definition of Done
Every session update must satisfy the following checks:

| Check | Evidence | Result |
|-------|----------|--------|
| ATLAS.md updated with current Status, Last worked on (ISO 8601), and Where I left off | `evidence/atlas-layout-restructure.txt`; ATLAS.md diff | **Pass** |
| Next Steps are clear, actionable, and prioritized | Manual review of Next Steps section | **Unverified** — manual review, no evidence file |
| Any claims made are supported by evidence | Corresponding files in evidence/ directory | **Unverified** — most rows still cite no real file. See D25. |
| Mathematical statements are correct and verifiable | Evidence files with calculations or references | **Unverified** — see D25 |
| No unsupported opinions presented as facts | Manual review distinguishing facts from opinions | **Unverified** — see D25 |
| Context tracking fields are consistently maintained | Evidence of regular updates to tracking sections | **Unverified** — see D25 |

---
## Atlas Doctor

Health of the Atlas integration itself. Checked 2026-10-16.

| # | Issue | Severity | Status | Suggested Fix | Last Checked |
|---|---|---|---|---|---|
| D16 | `~/.config/opencode/commands/atlas.md` hardcodes `/Users/emir/.config/opencode/skills/atlas/SKILL.md`, which the Atlas spec forbids in a wrapper. | info | ignored | **Accepted by the user, deliberately.** The hardcoded path is how OpenCode resolves this skill without matching another one. `<workspace>/SKILL.md` remains the pristine source of truth for cloning and for any other host; the OpenCode copy is a host-local install. Re-check only if `/atlas` stops resolving the skill. (Path corrected: the user moved the wrapper from `command/` to `commands/`.) | 2026-10-16 |
| D20 | Skill copy drift: `~/.config/opencode/skills/atlas/SKILL.md` differed from `<workspace>/SKILL.md`. | warn | fixed | Re-installed during the v2 workspace upgrade: the two files are now byte-identical to SKILL 2.1.0 (sha256 `0fae35d6…`, verified 2026-10-16). | 2026-10-16 |
| D21 | Status.Branch said `main`, but the repository uses `master` (there is no `main` or `dev` branch). | warn | fixed | Status.Branch now tracks the real branch. Repo base is `master`; the project predates the `main`/`dev`/`atlas/<feature>` strategy and adopting it is a separate decision. Evidence: `evidence/atlas-upgrade.txt`. | 2026-10-16 |
| D22 | ATLAS.md lacked the v2 sections **Original Intent**, **Last Failure** and **Rule Ledger**. | info | fixed | Added all three. Original Intent frozen from the user's own Purpose wording after confirmation (2026-10-16). | 2026-10-16 |
| D23 | The Inputs & Locations table was malformed: an orphan duplicate row and a stray column separator ended the table. | warn | fixed | Removed the orphan row and the stray separator. | 2026-10-16 |
| D24 | Uncommitted working-tree changes: `D ATLAS.md` (root file moved into `atlas/`, deletion never committed) and `M Atlas-source/SKILL.md`. | warn | fixed | Both resolved by the layout restore: the root `ATLAS.md` deletion is staged and `Atlas-source/SKILL.md` is moved to the repo root as `SKILL.md`. Evidence: `evidence/atlas-layout-restructure.txt`. | 2026-10-16 |
| D25 | `atlas/evidence/` is empty while the Definition of Done cites evidence files. | warn | partially addressed | `evidence/atlas-layout-restructure.txt` now backs the layout rows, and every DoD row with no real file is marked **Unverified** instead of Pass. The remaining rows need real evidence or removal. | 2026-10-16 |
| D26 | Legacy `atlas/` layout (ATLAS.md, evidence/, docs/, files/ live under `atlas/`). | info | fixed | Resolved by SKILL 2.1.0: the `atlas/` folder is now the canonical v2 layout, not legacy. This project already matches it. | 2026-10-16 |
| D27 | The project copy of the skill is a version behind: `SKILL.md` (repo root) is 2.0.0 (sha256 `822765b2…`), while `<workspace>/SKILL.md` and the host copy are 2.1.0 (`0fae35d6…`). | warn | open | Sync `SKILL.md` from `<workspace>/SKILL.md`, or restore it deliberately if the lag is intentional. Left unchanged here to preserve the user's uncommitted content. | 2026-10-16 |

---

## Decisions
- **If updating context, then always modify ATLAS.md** - Never rely on external tracking systems; the ATLAS.md file is the single source of truth
- **If making a claim, then provide evidence** - Unsupported statements must be clearly marked as hypotheses or opinions
- **If using mathematics, then show your work** - Include calculations, references, or clear reasoning paths
- **If evolving the ruleset, then document the change** - Explicitly note when and why the tracking approach is modified
- **If measuring performance, then use reproducible methods** - Ensure measurements can be validated and repeated

---

## Rule Ledger
| Rule | Born from (incident or evidence path) | Scope (project/global) | Status (unproven/proven) |
|---|---|---|---|
| Keep the host-installed skill copy byte-identical to `<workspace>/SKILL.md`; edit the original and re-install, never the host copy. | Doctor D20 | global | proven |
| A `/atlas` wrapper must not contain another machine's absolute path. | Doctor D16 | project | unproven |
| Always update the single-source `ATLAS.md` when context changes. | Decisions (stated at setup) | project | unproven |
| Mark unsupported statements as hypotheses, never as facts. | Decisions (stated at setup) | project | unproven |
| Keep framework-native files at the project root; only Atlas-owned files (`ATLAS.md`, `evidence/`, `docs/`, `files/`) live in `atlas/`. | Doctor D24/D26; SKILL 2.1.0 | global | proven |

---

## Planning Context
This project exists to develop and improve the Atlas skill/system itself. By developing Atlas through Atlas, we create a virtuous cycle where:
1. The skill improves through self-directed development
2. Effectiveness is validated through fact-based self-analysis and testing
3. Leadership is demonstrated through actual dogfooding of our own tools
4. Improvements are grounded in mathematical standards, evidence, and user feedback

The ultimate goal is to create a robust, continuously improving project tracking system that helps agents and humans manage complex workflows with clarity and accountability, while practicing what we preach.

---

## Agent Instructions

Read this file first. Update Status, Last Failure, Next Steps, Blockers and evidence before ending your session. Enforce the DoD and the branch strategy (`atlas/<feature>`, `dev`, `main`). Do not assume any context. Address open Atlas Doctor items or document why not. Use `Config/GLOBAL.md` unless overridden here.

- The repository currently uses `master` only (no `dev`/`main`). Migrating to the `main`/`dev`/`atlas/<feature>` strategy is an open decision, not an assumption.
- Keep `Config/profile.md` and `Config/registry.json` in the workspace in sync with reality; the registry is derived and regenerable.
- Never edit `<workspace>/SKILL.md` without the user's approval of the specific diff.
