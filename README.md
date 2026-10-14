# Atlas Skill Development Tracker

A self-hosted project for developing and improving the Atlas skill using Atlas itself.

## What This Is

This project tracks the development of the Atlas skill/system through an intentional self-referential setup:
- **Skill source code**: The `SKILL.md` file contains the Atlas skill being developed
- **Tracking system**: This project uses Atlas to track its own development progress
- **Dogfooding**: We improve Atlas by using Atlas to manage Atlas improvements

## Quick Start

1. **Read the current state**: `cat ATLAS.md`
2. **Make improvements**: Edit `SKILL.md` to enhance the Atlas skill
3. **Update tracking**: Modify `ATLAS.md` to reflect your work:
   - Update "Where I left off" with what you accomplished
   - Update "Next Steps" with your planned actions
   - Add evidence to `evidence/` directory (test results, measurements, etc.)
4. **Validate claims**: Support all statements with evidence and reasoning
5. **Repeat**: Continue the cycle

## Core Workflow

### Tracking Your Work
After each work session, update ATLAS.md:
- **Status**: Current state (In Progress, Blocked, Done, etc.)
- **Where I left off**: Brief summary of what you accomplished
- **Next Steps**: Prioritized list of what to do next

### Evidence-Based Development
Place verification in `evidence/`:
- Test results: `evidence/tests.log`
- Build logs: `evidence/build.log`
- Measurements: `evidence/measurements.txt`
- Screenshots: `evidence/screenshot.png`
- References: `evidence/reference.pdf`

### Skill Development Process
1. Identify improvement needed in `SKILL.md`
2. Make changes to `SKILL.md`
3. Test your changes (using `/atlas` command)
4. Document results in `evidence/`
5. Update `ATLAS.md` tracking sections
6. Commit changes: `git add SKILL.md ATLAS.md evidence/ && git commit -m "feat: [description]"`

## Project Structure

```
Atlas/
├── ATLAS.md          # Living ruleset and context tracker (UPDATE THIS)
├── SKILL.md          # Atlas skill source code being developed (EDIT THIS)
├── README.md         # This file
├── files/            # Reusable tools/templates
├── docs/             # Design specifications
├── evidence/         # Verifiable proof (logs, tests, measurements)
├── src/              # Source code (if applicable)
├── tests/            # Test files
└── .git/             # Version control
```

## Key Principles

- **Evidence first**: Never state a claim without supporting evidence
- **Local-first**: All tracking data lives in this project directory
- **Living document**: ATLAS.md evolves as your understanding improves
- **Atomic commits**: Each commit should represent one logical change
- **Continuous validation**: Regularly test that your changes work

## Getting Help

- Read `ATLAS.md` for current context and next steps
- Check `evidence/` for verification of past work
- Use `/atlas status Atlas` to see this project's status from any Atlas project
- The skill is functional: try `/atlas help` to see available commands

## Remember

This project gets more valuable the more you use it. Each update to ATLAS.md and SKILL.md makes the system better at tracking itself. The goal isn't perfection—it's continuous improvement grounded in evidence and real usage.
