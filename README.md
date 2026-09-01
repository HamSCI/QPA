# QPA (Quadrature Phased Array): Two-Element Orthogonal Active Receive Array for the HamSCI PSWS HF Receiver

A two-semester EE/CE senior design capstone project at The University of Scranton, sponsored by Dr. Nathaniel A. Frissell, W2NAF, in support of the [HamSCI](https://hamsci.org) Personal Space Weather Station (PSWS) HF Receiver.

A team of two or three students will design, build, and demonstrate:

1. A **two-element orthogonal active receive antenna array** covering the HF spectrum;
2. A **coherent dual-channel front end** that feeds both elements into the PSWS HF Receiver's single 0.1–60 MHz digitizer as two 30 MHz channels, using a GPSDO-disciplined frequency translator to move the second channel into the upper half of the passband; and
3. A **ka9q-radio / [SigMonD](https://github.com/HamSCI/sigmond) software application** that combines the two channels with arbitrary complex weights, steering the receive pattern in software.

The full project description presented to students is in [docs/project_description.md](docs/project_description.md).

## Repository Contents

| Path | Purpose |
|---|---|
| `docs/project_description.md` | Capstone project description for students |
| `ai/ai_usage_log.md` | Log of all substantive AI-assisted work sessions |
| `.claude/` | AI governance rules and the `/commit` workflow |
| `reference/` | Local-only reference material (gitignored; never committed) |

## AI Governance

This repository follows the [`ai_project_template`](https://github.com/w2naf-academia/ai_project_template) standards: every substantive AI-assisted session is logged in `ai/ai_usage_log.md` before committing, AI-assisted commits carry the `[AI-assisted]` prefix, and all work complies with the policies in `.claude/rules/ai-governance.md`.

## License

MIT license; see [LICENSE](LICENSE).
