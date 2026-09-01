# AI Usage Log — QPA (Quadrature Phased Array)

This log records all substantive AI-assisted sessions for the project
"QPA: Two-Element Orthogonal Active Receive Array for the HamSCI PSWS HF Receiver".

Required per University of Scranton AI Policy, HamSCI Generative AI Use Agreement, NASA AI guidance, NSF AI guidance, and the project-specific expectations in `.claude/rules/ai-governance.md`.

---

<!-- Append new entries below this line, newest at the bottom. Use the format produced by the /commit command. -->

## [2026-09-01 01:04 UTC]
- **Tool**: Claude (Anthropic), claude-fable-5
- **Session Purpose**: Draft the senior design capstone project description (two-element orthogonal active receive array with dual-channel front end for the PSWS HF Receiver) and bring the repository up to the `ai_project_template` standards.
- **Sections/Files Affected**: `docs/project_description.md` (new), `CLAUDE.md` (new), `README.md` (new), `.claude/settings.json`, `.claude/commands/commit.md`, `.claude/rules/ai-governance.md`, `.claude/rules/latex-writing.md`, `.claude/rules/python-code.md` (new, from template), `ai/ai_usage_log.md` (new), `.gitignore` (appended OS/editor/LaTeX rules and `reference/` exclusion so the publisher-provided QST PDF is never committed).
- **Nature of Contribution**: Draft (project description synthesized from the August 2026 QST PSWS HF Receiver article, the HamSCI/sigmond repository, and NAF's stated architecture: 2 orthogonal active elements, 2 × 30 MHz coherent channels via GPSDO-locked frequency translation into the top half of the 0.1–60 MHz digitizer passband, ka9q-radio/SigMonD arbitrary-weight steering); repository scaffolding from `w2naf-academia/ai_project_template`.
- **Human Review Status**: Pending review
- **Git Hash**: f59fcd9

## [2026-09-01 01:16 UTC]
- **Tool**: Claude (Anthropic), claude-fable-5
- **Session Purpose**: Expand the QPA acronym (Quadrature Phased Array), per NAF, everywhere the project short name appears.
- **Sections/Files Affected**: `README.md`, `CLAUDE.md`, `docs/project_description.md` (added footer line), `ai/ai_usage_log.md`
- **Nature of Contribution**: Edit
- **Human Review Status**: Reviewed and verified (change dictated by NAF)
- **Git Hash**: 499377a

## [2026-09-01 01:19 UTC]
- **Tool**: Claude (Anthropic), claude-fable-5
- **Session Purpose**: Fill the funding placeholders with the grant numbers from the acknowledgment in the August 2026 QST PSWS HF Receiver article (verified verbatim against the PDF, p. 33).
- **Sections/Files Affected**: `CLAUDE.md` (Funder line), `.claude/rules/ai-governance.md` (project-specific expectations), `docs/project_description.md` (funding acknowledgment footer)
- **Nature of Contribution**: Edit
- **Human Review Status**: Reviewed and verified (source text extracted from the article PDF)
- **Git Hash**: 9d0fa93

## [2026-09-01 01:32 UTC]
- **Tool**: Claude (Anthropic), claude-opus-5[1m]
- **Session Purpose**: NAF dictated a requirement: *"Hardware should be sufficiently ruggedized for installation at SPA, MCM, and PLM."* He then confirmed those are the three U.S. Antarctic Program stations, supplied the NSF award record for OPP-2332427, and stated: *"The antennas developed by this project will be used both for DASI2 amateur installations and installations at MCM, PLM, and SPA to support the OPP project."* Capture this as a design requirement. (Dictated during a session in the MSTID-Climatology-Paper-2026-Project repo; the assistant located this project as the correct home and did the work here.)
- **Sections/Files Affected**: `docs/project_description.md` — new "Where this hardware goes" block at the end of section 3 naming the two deployment targets, and a new **R10** appended to the section 5 requirements table with a following note. `ai/ai_usage_log.md`.
- **Nature of Contribution**: Drafting a requirement from NAF's dictated statement and the award record he supplied.
- **Human Review Status**: Pending review. NAF dictated the requirement and supplied the source documents; he has not read the resulting text.
- **Verification**:
  - The award title, the three stations, the OPP directorate, and the September 2024 to August 2029 period are taken from the NSF award record for 2332427 that NAF supplied, and are quoted from it rather than recalled.
  - **Deliberately not asserted**: the award record names New Jersey Institute of Technology as recipient, with Hyomin Kim as PI and Andrew J. Gerrard as co-PI. NAF is not listed on that record, which is titled "Collaborative Research" and so presumably has sibling awards the record does not show. The text therefore says the array supports OPP-2332427 and makes no claim about who holds it or about Scranton's role. `CLAUDE.md` and `.claude/rules/ai-governance.md` already list OPP-2332427 among the grants supporting the PSWS/DASI effort, which is the acknowledgment framing and is unchanged.
  - **Also not asserted**: the award abstract's instrument list (fluxgate and search-coil magnetometers, ELF and VLF receivers, riometers, optical systems, GPS/GNSS receivers) does **not** include HF receivers. The new text makes no claim that a PSWS HF receiver is part of that award's funded instrument suite. It records NAF's statement that the antennas from this project will be installed at those stations in support of the OPP project.
  - **No environmental numbers were invented.** R10 names the dimensions that need testable values (operating and survival temperature, wind and rime or ice loading, dry-snow ingress, low-temperature UV and mechanical durability of plastics and jackets, and installation and service by station personnel) and directs the team to obtain the governing values from the OPP project team and station support documentation. The accompanying note tells them to record a gap explicitly where a value is unavailable. This matches section 5's existing statement that turning these targets into a testable specification is the team's first deliverable.
  - **R1 through R9 keep their numbers.** The requirement was appended as R10 rather than inserted, so no existing reference to an R-number changes meaning. R8 (outdoor-rated, 72 h unattended) is left intact as the baseline case, and R10 states that it extends R8.
- **Git Hash**: [to be added after commit]
