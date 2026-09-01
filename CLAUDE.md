# QPA (Quadrature Phased Array): Two-Element Orthogonal Active Receive Array for the HamSCI PSWS HF Receiver

## Project Overview
QPA (Quadrature Phased Array) is a two-semester EE/CE senior design capstone project at The University of Scranton. A team of two or three students will design, build, and demonstrate a two-element orthogonal active receive antenna array with a coherent dual-channel front end for the HamSCI Personal Space Weather Station (PSWS) HF Receiver. The existing HF Receiver digitizes a single 0.1–60 MHz channel; this project splits that bandwidth into two coherent 30 MHz channels (one direct, one moved into the upper half of the digitizer's passband by a frequency translator) so that a ka9q-radio/SigMonD software system can combine the two antenna elements with arbitrary complex weights and steer the receive pattern in software. The primary audiences are the student team, the course instructors, the PI, and the HamSCI volunteer engineering community.

**PI**: Dr. Nathaniel A. Frissell, W2NAF, Department of Physics and Engineering, The University of Scranton
**Collaborators**: HamSCI volunteer engineering community; The University of Scranton Amateur Radio Club (W3USR)
**Funder**: Supports the HamSCI PSWS / DASI effort, funded by NSF grants AGS-2045755, AGS-2432821, AGS-2432822, AGS-2432824, AGS-2432823, AGS-2431666, and OPP-2332427; NASA grants 80NSSC23K1322, 80NSSC25K7026, and 80NSSC26K0051; and Frankford Radio Club and ARDC grants (per the acknowledgment in Frissell, *QST*, August 2026, p. 33).
**Project period**: Two academic semesters (planned: Fall 2026 – Spring 2027)

**NAF's role on OPP-2332427**: Institutional PI for the University of Scranton subaward to the New Jersey Institute of Technology (NJIT holds the award; Hyomin Kim is PI, Andrew J. Gerrard co-PI). This matters for QPA because the antennas developed here are destined for the U.S. Antarctic stations under that award as well as for DASI2 amateur sites, and NAF is the students' route to the Antarctic deployment requirements (requirement R10 in `docs/project_description.md`).

**Licensing**: Hardware designs will be released under an appropriate open hardware license (such as the TAPR Open Hardware License); source code under the MIT license (this repository's `LICENSE`).
**Student AI use**: Students will be given Claude Code accounts for this project. Their use is governed by `.claude/rules/ai-governance.md`, University of Scranton academic integrity policy, and the course instructor's rules.

## Project Goal
Deliver a working, documented, and replicable dual-channel antenna array front end and beam-steering software demonstration for the PSWS HF Receiver: two orthogonal active elements, a GPSDO-disciplined frequency translator and combiner feeding the receiver's single RF input, and a ka9q-radio/SigMonD application that demonstrates measurable pattern steering (for example, a steerable null on a transmitter of known bearing).

## Repository Structure
This project starts from the `ai_project_template` scaffold.

```
QPA/
├── CLAUDE.md
├── README.md
├── LICENSE
├── .gitignore
├── .claude/
│   ├── settings.json
│   ├── commands/commit.md        ← /commit workflow
│   └── rules/
│       ├── ai-governance.md
│       ├── latex-writing.md      ← for design reports/posters
│       └── python-code.md        ← for SigMonD/ka9q-radio client code
├── ai/
│   └── ai_usage_log.md           ← mandatory AI session log
├── docs/
│   └── project_description.md    ← the capstone project description presented to students
└── reference/                    ← gitignored; local-only reference material (publisher PDFs)
```

The `reference/` directory holds publisher-provided reference material (currently the August 2026 QST HamSCI special section PDF). It is gitignored and **must never be committed or redistributed** (see `.claude/rules/ai-governance.md`).

## Key External Resources
- HamSCI PSWS: https://hamsci.org/psws and https://www.psws.hamsci.org
- SigMonD (signal monitor daemon): https://github.com/HamSCI/sigmond
- ka9q-radio (Phil Karn, KA9Q): https://github.com/ka9q/ka9q-radio

## Submodules (optional)
If this project later adds submodules (e.g., a hardware design repo or an Overleaf report):
1. Make changes and commit **inside** the submodule first
2. Then commit the updated submodule pointer in this repo
3. Always use `[AI-assisted]` prefix on commits made with AI assistance
4. Ask before pushing to any remote

The `/commit` workflow auto-detects submodules via `git submodule status`.

## AI Governance
All AI-assisted work must comply with the policies in `.claude/rules/ai-governance.md`.
Every substantive AI session must be logged in `ai/ai_usage_log.md` before committing.
Use the `/commit` command to handle logging and committing in the correct order.
