# QPA as a Stepping Stone: Channel Count, What It Buys, and Candidate Follow-On Capstones

**Date:** 2026-09-03
**Origin:** NAF, working through the pattern limitations of a two-element crossed pair and asking where the project leads.
**Status:** Recorded for the student team and for advisor planning. A condensed version was adopted as section 9 of `docs/project_description.md` ("Where This Leads") in the same session.

## What was said

> "In a perfect world, I would like to steer the antenna in any arbitrary look direction. But,
> if I understand the math and physics correctly, if I am using two orthogonal HF receive loops,
> we are always going to have two fairly broad lobes 180 deg apart with two deep nulls on the
> sides. I can never look in just a single direction. Do you agree?" — NAF, 2026-09-03

> "I can see this particular senior design project to be a stepping stone to more sophisticated
> schemes that could resolve some of these issues. Do you agree?" — NAF, 2026-09-03

> "I would like to add a 'Where this leads' section. That should include the possibility of
> future senior capstone project years that build on this. You can also create a new note that
> discusses the specifics of how this project could be a stepping stone. Also note that a TAPR
> member is design a 4 channel coherent receiver with each channel having 60 MHz BW. Imagine
> what we can do with that!" — NAF, 2026-09-03

NAF's reading of the physics is correct, and the constraint is exact. The
derivation is recorded below because it is the constraint that sets the whole ladder.

## 1. Why the 180° symmetry is unavoidable with two co-located elements

Two co-located orthogonal elements (crossed loops, crossed dipoles, the geometry is the same)
have azimuth voltage patterns cos φ and sin φ. Any complex-weighted sum is

```
F(φ) = a·cos φ + b·sin φ ,   a, b ∈ ℂ
```

Separating real and imaginary parts, the power pattern is a real quadratic form on the unit
circle:

```
|F(φ)|² = uᵀ M u ,   u = (cos φ, sin φ) ,   M real, symmetric, positive semidefinite, 2×2
```

Diagonalizing M gives `|F|² = (λ₁+λ₂)/2 + ((λ₁−λ₂)/2)·cos(2(φ−ψ))`, a constant plus one
cos(2φ) term. Three consequences follow with no remaining freedom:

- **`|F(φ+180°)| = |F(φ)|` for every choice of a and b.** No weight vector breaks it.
- **Maximum and minimum are exactly 90° apart**, so the nulls sit midway between the lobes.
  The lobes are the broad cos-shaped ones, roughly 90° between the −3 dB points.
- **A perfect null requires M to be rank 1**, which holds only when b/a is purely real, meaning
  the two channels are combined co-phased. Any relative phase error fills the null in.

So the commandable relative phase controls null depth. It trades that depth
against an omnidirectional response: equal amplitudes in quadrature give M = I and no pattern
at all. Worth stating precisely for the team, because it is easy to misread: a maximum **can**
be pointed at any bearing. What cannot be done is suppressing the reciprocal bearing at the
same time.

## 2. Null depth is a channel-match specification

The rank-1 condition converts the physics directly into a hardware specification. A perfect
null requires the two branches combined co-phased, so whatever calibration residual remains
sets the achievable depth. For an amplitude error ε_a (as a voltage ratio) and a phase error δ
(radians), the first-order cancellation depth is

```
depth ≈ 20·log₁₀( sqrt(ε_a² + δ²) )
```

R5's numbers run through that relation, and the resulting margin against R7, are already worked
out as Q3 of [2026-09-01_design_review_questions.md](2026-09-01_design_review_questions.md),
which also carries the mutual-coupling floor this arithmetic ignores. That note holds the PDR
question and the margin; it is not restated here.

What is worth recording is the link back to section 1. R7 is written as a null-depth requirement
because a null is the only sharp feature two co-located elements produce, and the depth it can
reach is set by calibration stability rather than by the antennas. Every rung of the ladder in
section 4 inherits that dependence.

## 3. What generalizes, and what does not

**Does not generalize: the frequency-translation trick.** Occupying 30–60 MHz exploits a
specific vacancy in the RX-888 MKII's passband at full sample rate. There is no third slot.
If the students internalize the translator as the scalable core of the architecture, the
follow-on project opens by discovering the ceiling. Additional channels come from additional
digitizers sharing a reference, or from a natively multi-channel receiver (section 5 below).

**Does generalize, and is the actual stepping stone:**

1. **Inter-channel calibration.** Measuring and holding a complex gain difference across
   0.1–30 MHz, over temperature, using a switched common-injection path. Hardest deliverable
   in the project, independent of channel count, and a prerequisite for every rung above.
2. **The weight-policy architecture** already framed in
   [2026-09-01_maximal_ratio_combining.md](2026-09-01_maximal_ratio_combining.md). Manual
   steering, MRC, and max-SINR null placement are three policies over one code path,
   `y = Σ wₖ xₖ`. Written over an N-channel data model this survives; written for exactly two
   channels it is discarded.
3. **The channel-estimate observable h₂/h₁.** The MRC note already identified that one
   estimator serves MRC, direction finding, and the calibration check. That estimator, and not
   the antennas, is the durable scientific instrument.

**One more that costs a schema decision:** bearing products logged with their 180° ambiguity
represented explicitly, so that a later cross-fix between stations can consume this year's
data directly.

## 4. The escalation ladder by channel count

Each rung fixes something the rung below cannot, which is what makes this a ladder.

| Channels | Configuration | Resolves |
|---|---|---|
| 2 | Crossed loops (QPA) | Azimuth null placement. Ambiguous bearing, uncorrected polarization. |
| 3 | Crossed loops plus an omnidirectional sense element | The 180° ambiguity. Loop plus sense gives a cardioid: one lobe, one null. The classical Bellini-Tosi/Watson-Watt fix, and frequency-independent. |
| 4 | Three orthogonal loops plus an electric whip | The full magnetic field vector plus one electric component. Elevation angle becomes observable, and polarization becomes a measured quantity. |
| 6 | Three loops plus three short dipoles (full vector sensor) | Unambiguous 3-D arrival direction and complete polarization from a single point, since the Poynting vector needs all three E and all three H components. |
| >4 | Any set, processed jointly | Superresolution direction finding (MUSIC, ESPRIT) separates up to N−1 simultaneous arrivals, enough at N = 4 to split O from X or one-hop from two-hop. |

Note the accuracy caveat on the 4-channel row: three loops alone determine only the plane
perpendicular to the measured H vector, so adding one electric component constrains the
direction without fully resolving it for arbitrary polarization. Four channels is a strong
intermediate rung, and the full six-component sensor is the endpoint.

**Practical route to rung 3 without new receiver hardware:** a second RX-888 disciplined from
the same GPSDO. The two digitizers would be frequency-locked with a fixed unknown sample offset
between them, and QPA's common-injection calibration path is exactly the mechanism that
resolves such an offset. Whether the RX-888 MKII's sample clock can be externally disciplined
needs confirming before this is treated as available.

## 5. The TAPR four-channel receiver

Per NAF, 2026-09-03: **a TAPR member is designing a coherent receiver with four channels, each
carrying 60 MHz of bandwidth.** No further specifics were given in this session, and the
designer's name, the project name, and the specifications are not recorded here because they
have not been verified. **Confirm with NAF before citing this anywhere outside the project.**

If it materializes as described, it removes both compromises QPA is built around:

- **No frequency translation, and no lost spectrum.** Every channel covers 0.1–60 MHz natively.
  The deliberate sacrifice of 30–60 MHz argued in section 2 of the project description goes
  away, so sporadic E, meteor scatter, and 6 m science come back to the station.
- **Four coherent channels at the antenna**, reaching rung 4 of the table above in one step.

What that unlocks scientifically, in rough order of value to HamSCI:

1. **Elevation angle, therefore reflection height.** A beacon at a known great-circle distance,
   observed with a measured arrival elevation, gives an ionospheric reflection height under a
   simple hop model. This is passive oblique-incidence sounding at a small fraction of an
   ionosonde's cost, from a network already numbering dozens of stations.
2. **Polarization as a measurement.** The night-effect error becomes an observable, and the
   ordinary and extraordinary magnetoionic modes can be separated by their differing
   polarization and arrival angle. The splitting itself carries electron-density and magnetic
   field information.
3. **Mode separation.** Distinguishing one-hop from two-hop arrivals removes an ambiguity that
   currently limits Doppler and time-of-flight products.
4. **Unambiguous single-station bearings**, which makes off-great-circle propagation detection
   work from a single site.

## 6. Candidate follow-on capstone projects

None of these is committed. Each depends on funding, student interest, and the outcome of the
2026–27 project. The NSF Antarctic award (OPP-2332427) runs September 2024 through August 2029,
so a multi-year sequence fits inside it, with each team inheriting a calibrated and documented
system.

| Project | Focus | Capability added | Depends on |
|---|---|---|---|
| QPA, 2026–27 | Two elements, one RX-888, frequency translation | Calibration, steering client, 15 dB null on a known bearing | Nothing |
| Follow-on A | Third coherent channel plus a sense element | Unambiguous bearings; first bearing cross-fix between two PSWS stations | QPA's calibration procedure; an external clock path or a second station |
| Follow-on B | Vector sensor on the four-channel receiver | Elevation angle, polarization, mode separation, superresolution DOA | The TAPR receiver existing; A's estimator |
| Follow-on C | Network-scale direction finding | Distributed bearing products in the shared database; Antarctic operations | The bearing-product schema decided during QPA |

Follow-on C is worth flagging as available early and cheaply. Two stations each reporting an
ambiguous bearing line produce an unambiguous fix by intersection, so the network resolves at
the network level what a single two-element station cannot resolve locally. It requires no
antenna development, only that bearing products be logged in a form a cross-fix can consume,
which is a decision this year's team makes whether or not they think about it.

## 7. Open items (NAF's calls, not yet decided)

- Confirm the TAPR four-channel receiver's designer, status, and specifications, and decide
  whether the project description should name them once known. Section 9 currently carries a
  bracketed placeholder.
- Whether to instruct this year's team to build the client over an N-channel data model, or to
  leave it as advice in this note. Related to the deferred R6 extension in the MRC note.
- Whether the bearing-product database schema is in scope for QPA or deferred to Follow-on A.
- Whether R7 should be re-derived from R5 in the team's requirements deliverable (section 2
  above recommends yes).

## References

- `docs/project_description.md` section 9, "Where This Leads," adopted 2026-09-03.
- [2026-09-01_maximal_ratio_combining.md](2026-09-01_maximal_ratio_combining.md), which
  established the weight-policy framing this note builds on.
- Brennan, D. G. (1959), "Linear Diversity Combining Techniques," *Proceedings of the IRE*,
  June 1959, pp. 1075–1102.
