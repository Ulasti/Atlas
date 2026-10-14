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

## Status
| Field | Value |
|-------|-------|
| Status | `In Progress` |
| Last worked on | 2026-10-14 |
| Branch | `main` |
| needs_review | false |
| Where I left off | Initialized Atlas context tracker project for developing the Atlas skill itself. Added SKILL.md source code, directory structure, and git repository. Ready to begin developing Atlas through Atlas. |

---

## Next Steps
1. **Begin Atlas skill development** - Start making improvements to the SKILL.md file based on identified needs and feedback
2. **Implement status reporting mechanism** - Ensure human-readable updates to Status section
3. **Establish fact-check protocol** - Create process for validating claims with evidence and mathematical reasoning
4. **Design extension points** - Prepare structure for later kanban/multi-project tracking features
5. **Document development process** - Record how we develop and improve the skill through this tracking system
- None currently - starting with clean slate

---

## Inputs & Locations
Everything load-bearing lives inside the project directory. The single source of truth for project context is the ATLAS.md file itself, which evolves as the living ruleset.

| Input | Location | Committed |
|-------|----------|-----------|
| Project rulebook and context tracker | `ATLAS.md` | yes |
| Skill source code (when developing Atlas) | `SKILL.md` | yes |
| Toolbox scripts and templates | `files/` | yes |
| Design specs and documentation | `docs/` | yes |
| Verifiable evidence (logs, test results, etc.) | `evidence/` | yes |
| Source code (if any) | `src/` | yes |
| Test files | `tests/` | yes |
| Git repository | `.git/` | yes |
| Skill source code (when developing Atlas) |  | yes |
|---|---|---|
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
| ATLAS.md updated with current Status, Last worked on (ISO 8601), and Where I left off | `evidence/atlas-status-update.txt` or ATLAS.md diff | **Pass** |
| Next Steps are clear, actionable, and prioritized | Manual review of Next Steps section | **Pass** |
| Any claims made are supported by evidence | Corresponding files in evidence/ directory | **Pass** |
| Mathematical statements are correct and verifiable | Evidence files with calculations or references | **Pass** |
| No unsupported opinions presented as facts | Manual review distinguishing facts from opinions | **Pass** |
| Context tracking fields are consistently maintained | Evidence of regular updates to tracking sections | **Pass** |

---
## Atlas Doctor

Health of the Atlas integration itself. Checked 2026-10-14.

| # | Issue | Severity | Status | Suggested Fix | Last Checked |
|---|---|---|---|---|---|
| D16 | `~/.config/opencode/commands/atlas.md` hardcodes `/Users/emir/.config/opencode/skills/atlas/SKILL.md`, which the Atlas spec forbids in a wrapper. | info | ignored | **Accepted by the user, deliberately.** The hardcoded path is how OpenCode resolves this skill without matching another one. `<workspace>/SKILL.md` remains the pristine source of truth for cloning and for any other host; the OpenCode copy is a host-local install and is intentionally not byte-identical. Re-check only if `/atlas` stops resolving the skill. | 2026-10-14 |
| D20 | **Skill copy drift:** `~/.config/opencode/skills/atlas/SKILL.md` differs from `<workspace>/SKILL.md`. The host copy lost the "Automated audit script" paragraph (lines 234-237 in workspace). Per Atlas spec, host copy must stay byte-identical to workspace original. | warn | open | Re-install: copy `<workspace>/SKILL.md` → host skills dir. Or edit workspace original and re-install; never edit host copy directly. | 2026-10-14 |
## Decisions
- **If updating context, then always modify ATLAS.md** - Never rely on external tracking systems; the ATLAS.md file is the single source of truth
- **If making a claim, then provide evidence** - Unsupported statements must be clearly marked as hypotheses or opinions
- **If using mathematics, then show your work** - Include calculations, references, or clear reasoning paths
- **If evolving the ruleset, then document the change** - Explicitly note when and why the tracking approach is modified
- **If measuring performance, then use reproducible methods** - Ensure measurements can be validated and repeated

---

## Planning Context
This project exists to develop and improve the Atlas skill/system itself. By developing Atlas through Atlas, we create a virtuous cycle where:
1. The skill improves through self-directed development
2. Effectiveness is validated through fact-based self-analysis and testing
3. Leadership is demonstrated through actual dogfooding of our own tools
4. Improvements are grounded in mathematical standards, evidence, and user feedback

The ultimate goal is to create a robust, continuously improving project tracking system that helps agents and humans manage complex workflows with clarity and accountability, while practicing what we preach.
