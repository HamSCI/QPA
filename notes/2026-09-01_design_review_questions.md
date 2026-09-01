# Design-Review Questions Held Back From the QPA Project Description

**Date:** 2026-09-01
**Origin:** Technical review of `docs/project_description.md` (Claude, under NAF's direction).
**Status:** Advisor-side material. The project description is unchanged for these four items.

## What was said

> "Some of those findings are probably things the design team should figure out for
> themselves, right?" — NAF, 2026-09-01

## Decision

Four findings from that review are the team's design work, not gaps in the solicitation, so
they stay out of `docs/project_description.md` and live here as questions to ask at the
Preliminary and Critical Design Reviews. Same reasoning as
[the maximal-ratio combining note](2026-09-01_maximal_ratio_combining.md): the description
states what only the advisor knows and what the team is accountable for, and putting a derived
answer in it pre-empts the trade study that was supposed to produce it.

A finding that would appear in the students' own PDR belongs in the advisor's question list.
If the team arrives at a review without having reached one of these, ask the question. If they
arrive having already solved it, that is the review passing.

Six other findings from the same review did go into the description: the "available real
estate" claim was calibrated against what 30–60 MHz costs the station, the block diagram
stopped pre-answering the LO frequency plan, R2's "Nyquist region" wording was corrected,
array geometry was added to the semester 1 trade studies, the HamSCI Workshop was named as the
real schedule driver, and the diagram boxes were aligned. Two remain open pending NAF: the
assumed RX-888 sample rate (section 2) and a safety and regulatory requirement row (section 5).

---

## Q1. What is each channel's share of the ADC's full scale?

**Assigned by:** R3, and "full RF cascade budget (gain, noise figure, intercept points,
dynamic range against the 16-bit ADC)" in the semester 1 plan.

Summing two antennas onto one input divides the RX-888's full scale between them, so each
channel gives up headroom relative to today's single-antenna station. A 16-bit ADC fed by a
wideband HF antenna is already dynamic-range-limited by the AM broadcast band, which both
channels carry.

**Ask at PDR:** what is the per-channel backoff, where did the number come from, and what
does it do to the noise floor at the quiet end of the band? A team that has budgeted this has
done the cascade analysis; a team that has not will discover it at integration.

## Q2. What keeps real 30–60 MHz signals out of channel B's output band?

**Assigned by:** "mixer and filter topology" in the semester 1 trade studies.

The description names the post-mixer band-pass filter. The subtler hazard is upstream: a real
signal already at 30–60 MHz (VHF low-band land mobile, 6 m, TV channel 2) that reaches the
mixer can leak through to the output band, where it is indistinguishable from translated HF
and will corrupt any bearing computed from that slice.

**Ask at PDR:** show the filtering ahead of the mixer, and the leakage budget through it.

## Q3. Do R5 and R7 agree with each other?

**Assigned by:** "Refining them into a complete, testable requirements specification, with
each value justified by analysis, is the team's first deliverable."

R5 and R7 are not independent. For two equal-amplitude contributions, residual amplitude
error ε and phase error δ leave a null residual of roughly √(ε² + δ²). R5's 1 dB and 3° give
ε = 0.12 and δ = 0.052, a residual of 0.133, so a null near 17.5 dB; at 5° it falls to about
16.5 dB. R7 asks for 15 dB, so the two initial targets are consistent with a few dB of margin
and no more, before mutual coupling and site asymmetry take their share. Practical coupling
between nominally orthogonal elements at −20 to −30 dB sets its own floor.

The targets are self-consistent, so this is a good first-week exercise.

**Ask at PDR:** which of R5 and R7 did you derive from the other, and what is the margin?

## Q4. What does the injected calibration signal actually calibrate?

**Assigned by:** "calibration method" in the semester 1 trade studies.

A switched common test signal injected at the amplifier inputs measures the complex gain
difference of the two receive chains, which is what R5 is written against. It cannot capture
element pattern differences, mutual coupling between the crossed elements, or ground
asymmetry, and those are where real null depth is lost. The full array manifold needs on-air
calibration against a transmitter of known bearing.

This is the finding most likely to be missed entirely, and the one whose consequence is a
demonstration that will not reach R7 for reasons the bench never showed. The semester 2 plan
already compares bearings against great-circle values, which is the right measurement; the
question is whether the team treats it as calibration, feeding the measured corrections back
into the weights.

**Ask at PDR:** what does the injection path cover, and what covers the rest?
