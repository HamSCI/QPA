# Maximal-Ratio Combining as an Operating Mode for the QPA Steering Client

**Date:** 2026-09-01
**Origin:** Suggestion from Phil Karn, KA9Q, relayed by NAF.
**Status:** Recorded for the student team. The main project description is unchanged (decision below).

## What was said

> "KA9Q also told me to look at 'Maximal ratio combining'... are you familiar with that term?
> Apparently it is common in the cellular industry." — NAF, 2026-09-01

> "Can you write that up as a note? I don't think it should be part of the main project
> description." — NAF, 2026-09-01

## Decision

This material lives here as a note and the project description stays as written. Rationale:
the description is a solicitation, and its requirements state what the team must achieve.
The choice of weight policies (manual steering, MRC, null placement) belongs to the team's
semester 1 trade study, and prescribing MRC in the description would pre-empt that work. This
note is handed to the team when they start. If the description ever needs it, one sentence in
the software paragraph is the most it should get, decided after the team forms.

## What maximal-ratio combining is

MRC is the classic diversity-combining result \[Brennan, 1959]. Given N receive branches
carrying the same signal, weight each branch by the complex conjugate of its channel gain
divided by its branch noise power, then sum:

```
w_k = h_k* / σ_k²        y = Σ w_k x_k        output SNR = Σ (branch SNRs)
```

The conjugate co-phases the branches so they add coherently, and the magnitude weighting
favors the branches hearing the signal best. Among all linear combiners it maximizes output
SNR against uncorrelated noise, hence the name. It is ubiquitous in the cellular industry:
CDMA rake receivers apply it across multipath fingers, base stations apply it across
diversity antennas, and it is the baseline receive strategy in MIMO systems. Brennan's paper
also defines the two simpler alternatives, selection combining (take the best branch) and
equal-gain combining (co-phase only).

## Why it fits QPA

MRC is exactly the client's combining operation, `y = w₁·x₁ + w₂·x₂`, with the weights chosen
automatically instead of manually. That makes it a weight *policy*, sharing one code path with
everything else the client does:

| Policy | Weights come from | Use |
|---|---|---|
| Manual steering | Geometry: commanded azimuth | Pattern demo, null on a known bearing |
| MRC | Measured channel estimates h₁, h₂ for a desired signal | Automatic SNR maximization, diversity |
| Max-SINR / null-on-interferer | Desired signal plus interference statistics | Interference rejection |

For MRC the channel estimates can come from a beacon (WWV, WWVH, CHU) or from decoded
WSPR/FT8 symbols in the slice. Two bonuses follow:

1. **The array points itself.** Applying conjugate weights steers the synthesized figure-8 at
   the signal with no bearing input.
2. **The estimates are the DF observable.** The ratio h₂/h₁ carries the bearing/polarization
   information, so the same estimator feeds MRC, direction finding, and calibration checks.

## What it buys at HF

Ionospheric propagation splits into O and X magnetoionic modes whose combined polarization
varies with time, so a single fixed element suffers polarization fading. An orthogonal pair
under MRC rides through it. This yields a clean quantitative metric for the project:
**WSPR/FT8 decode counts, single element versus MRC of both, over the same interval.**

## What it does not do

MRC maximizes SNR assuming branch noise is uncorrelated between branches. It does not reject
a correlated interferer; that calls for max-SINR (MVDR-type) weights, of which the manual
two-element null placement in the description is the special case. MRC and null steering are
the two ends of the same weight-selection problem.

## Options recorded for later (NAF's call, not yet decided)

- Extend requirement R6 so the client supports named weight policies (manual, MRC), rather
  than manual weights only.
- Add a demonstration criterion: decode-count comparison, one element vs. MRC, alongside the
  existing null-depth demo.
- Credit the suggestion to Phil Karn, KA9Q, wherever it lands.

## Reference

Brennan, D. G. (1959), "Linear Diversity Combining Techniques," *Proceedings of the IRE*,
June 1959, pp. 1075–1102. Citation details verified 2026-09-01 against the published reprint
introduction (Beaulieu, *Proc. IEEE*); the paper analyzes selection, maximal-ratio, and
equal-gain combining.
