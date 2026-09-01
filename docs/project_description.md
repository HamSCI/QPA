# A Steerable Two-Element Receive Antenna Array for the HamSCI Personal Space Weather Station HF Receiver

**EE/CE Senior Design Capstone Project (two semesters)**

| | |
|---|---|
| **Sponsor / Mentor** | Dr. Nathaniel A. Frissell, W2NAF, Department of Physics and Engineering, The University of Scranton (nathaniel.frissell@scranton.edu) |
| **Additional mentors** | HamSCI volunteer engineering community; The University of Scranton Amateur Radio Club (W3USR) |
| **Team size** | 2–3 students (EE and/or CE) |
| **Duration** | Two semesters (Fall 2026 – Spring 2027) |
| **Disciplines** | RF/analog circuit design, antennas, digital signal processing, embedded Linux software |

---

## 1. Background

The Ham Radio Science Citizen Investigation (HamSCI, [hamsci.org](https://hamsci.org)) Personal Space Weather Station (PSWS) is a network of inexpensive, ground-based scientific instruments operated by amateur radio volunteers and universities around the world. The network studies radio propagation, the ionosphere, and the coupled geospace system. More than 30 stations are already deployed, with another 25 planned under a US National Science Foundation (NSF) Distributed Array of Small Instruments (DASI) project; installations reach from Ellesmere Island in the Canadian Arctic to the Neumayer III Station in Antarctica.

The flagship instrument of this network is the **PSWS HF Receiver**: an RX-888 MKII 16-bit direct-sampling software-defined radio (SDR) that digitizes the entire high-frequency spectrum (0.1–60 MHz usable) in a single channel and streams it over USB 3 to a low-cost Linux mini-computer. A GPS-disciplined oscillator (GPSDO) gives the system part-per-trillion frequency stability, and a Turn Island Systems TS1 TimeSync injector provides timing accuracy better than ±1 µs. The **ka9q-radio** software package by Phil Karn, KA9Q, uses a fast-convolution architecture to carve this wideband stream into hundreds of simultaneous, arbitrary-bandwidth "slice" receivers on a conventional CPU. HamSCI's **SigMonD** (Signal Monitor Daemon, [github.com/HamSCI/sigmond](https://github.com/HamSCI/sigmond)) coordinates the family of client applications that consume these slices: WSPR and FT8 spot decoding, WWV/WWVH/CHU Doppler and time-of-flight measurement, web SDR service, and more.

The system is described in the August 2026 issue of *QST* ("The HamSCI Personal Space Weather Station HF Receiver," pp. 30–33), available from the project sponsor.

## 2. The Gap This Project Fills

Today, every PSWS HF Receiver listens through **one antenna**, typically an omnidirectional active vertical. An omnidirectional station measures *what* arrives and *when*, but it is blind to *where a signal comes from*. Direction sensitivity would open a new dimension for the network:

- **Direction finding** of scientific beacons (WWV, WWVH, CHU) to detect off-great-circle propagation caused by ionospheric disturbances;
- **Interference rejection**: steering a pattern null onto a local noise source or an interfering station, improving every downstream measurement;
- **Diversity reception** and pattern selection for weak-signal work in contesting, DXing, and the WSPR/FT8 monitoring that feeds HamSCI science.

The classic solution, dating to the Watson-Watt direction finders of the 1920s, is a pair of orthogonal antenna elements whose outputs are combined with controlled amplitude and phase. What is new here is the platform: the PSWS HF Receiver already digitizes 60 MHz of spectrum coherently on a single ADC, and its primary science band occupies only the bottom half. **The top 30 MHz of the digitizer's passband is available real estate.** If a second antenna's 0.1–30 MHz output is translated up into 30–60 MHz and combined onto the receiver's single coaxial input, the existing hardware becomes a two-channel coherent receiver at almost no additional cost, and pattern steering becomes a pure software problem.

## 3. Project Objective

Design, build, and demonstrate a **two-element orthogonal active receive antenna array with a coherent dual-channel front end** for the PSWS HF Receiver, together with the **ka9q-radio/SigMonD software** needed to combine the two channels with arbitrary complex weights and steer the array's receive pattern in software.

At the end of the project, a visitor to the demonstration should be able to watch the team rotate a synthesized antenna pattern from a keyboard, place a null on a transmitter of known bearing, and see the received signal drop on a live display.

## 4. System Concept

```
 Element 1 (e.g., N–S)          Element 2 (e.g., E–W, orthogonal)
      │                               │
 ┌────▼─────┐                    ┌────▼─────┐
 │  Active   │                   │  Active   │
 │ antenna + │                   │ antenna + │
 │ amplifier │                   │ amplifier │
 └────┬─────┘                    └────┬─────┘
      │ 0.1–30 MHz                    │ 0.1–30 MHz
 ┌────▼─────┐                    ┌────▼──────────┐
 │ Low-pass  │                   │  Frequency    │   LO locked to
 │  filter   │                   │  translator   │◄── station GPSDO
 │ (≤30 MHz) │                   │ (→ 30–60 MHz) │   reference
 └────┬─────┘                    └────┬──────────┘
      │                               │ 30–60 MHz (band-pass filtered)
      └────────────┬──────────────────┘
              ┌────▼─────┐
              │ Combiner/ │  single coax, DC power via bias tee
              │ diplexer  │
              └────┬─────┘
              ┌────▼──────────────┐
              │ PSWS HF Receiver  │  RX-888 MKII: one ADC digitizes
              │ (existing, as-is) │  both channels coherently
              └────┬──────────────┘
              ┌────▼──────────────┐
              │ ka9q-radio        │  paired slices at f and f + f_LO
              ├───────────────────┤
              │ SigMonD steering  │  calibration + complex weights:
              │ client (new)      │  y = w₁·x₁ + w₂·x₂
              └───────────────────┘
```

**Hardware.** Two orthogonally oriented active receive elements (element type is a semester 1 trade study: active dipoles, loops, or verticals) each cover 0.1–30 MHz. Channel A passes directly through a low-pass filter. Channel B is mixed against a local oscillator that is phase-locked to the station's existing GPSDO reference, translating its 0.1–30 MHz spectrum into the 30–60 MHz half of the digitizer's passband; band-pass filtering suppresses the image, LO leakage, and out-of-band ingress (e.g., FM broadcast). A combiner/diplexer sums the two channels onto the receiver's single feedline. The station hardware itself is untouched.

**Coherence.** Because one ADC samples both channels, they are inherently sample-synchronous. Because the translator LO is disciplined by the same GPSDO that disciplines the ADC clock, the inter-channel phase relationship is stable and can be measured once and corrected in software. A built-in calibration path (for example, a switched common test signal injected into both channels) lets the team measure the complex gain difference between channels as a function of frequency.

**Software.** ka9q-radio provides matching slice receivers at *f* (channel A) and *f* + *f*<sub>LO</sub> (channel B). A new SigMonD client application applies the calibration table and a user-commanded complex weight to each channel, then sums them. For a crossed orthogonal pair, the weighted sum synthesizes a dipole-like figure-8 pattern rotated to any commanded azimuth, which places a steerable null in any direction and supports bearing-line estimation on arriving signals (with the 180° ambiguity inherent to a two-element array). The client should follow SigMonD's existing architecture: subscribe to multicast RTP slice streams via ka9q-python, run as a managed systemd service, and log products to the shared database.

## 5. Draft Technical Requirements

These are the sponsor's initial targets. Refining them into a complete, testable requirements specification, with each value justified by analysis, is the team's first deliverable.

| # | Requirement (initial target) |
|---|---|
| R1 | Two orthogonally oriented active receive elements covering 0.1–30 MHz (threshold: 1.8–30 MHz amateur/science bands) |
| R2 | Channel B frequency translator moves 0.1–30 MHz into the 30–60 MHz Nyquist region; LO phase-locked to the station GPSDO reference |
| R3 | Image, LO leakage, and alias products suppressed sufficiently that they do not degrade either channel's science use (team to derive dB requirements from the RX-888's dynamic range) |
| R4 | Single coaxial feed into the unmodified PSWS HF Receiver RF input; front-end electronics powered over the feedline via bias tee |
| R5 | Inter-channel amplitude and phase calibrated across the band; post-calibration stability sufficient for pattern steering (initial target: ~1 dB / a few degrees over hours and normal outdoor temperature swings) |
| R6 | SigMonD client software performs calibrated complex-weight combining of paired ka9q-radio slices with operator-commanded steering |
| R7 | Demonstrated null depth of at least 15 dB on a signal of known bearing (initial target) |
| R8 | Outdoor-rated enclosures and antennas; continuous unattended operation for at least 72 hours |
| R9 | Full documentation (schematics, board files, bill of materials, calibration procedure, software) released open source so HamSCI volunteers can replicate the design |

## 6. Two-Semester Plan

### Semester 1: Requirements, Design, and Prototyping
- Requirements review with the sponsor and HamSCI mentors; finalize the specification.
- Trade studies: antenna element type, LO frequency plan (high-side vs. low-side injection, guard bands, spectral inversion handling), mixer and filter topology, combiner design, calibration method.
- Analysis and simulation: antenna modeling, full RF cascade budget (gain, noise figure, intercept points, dynamic range against the 16-bit ADC).
- Breadboard prototypes: active element front end and frequency translator; bench demonstration of two-channel coherent capture through a single RX-888.
- Software architecture prototype: paired-slice acquisition and complex-weight combining on bench signals.
- **Milestones:** Preliminary Design Review (mid-semester), Critical Design Review (end of semester).

### Semester 2: Build, Integrate, Demonstrate
- Fabricate final hardware: PCBs, enclosures, weatherproofing, bias-tee power.
- Install the array at the field test site and integrate with a production PSWS HF Receiver under SigMonD.
- Calibration campaign; verify inter-channel stability against R5.
- Steering demonstrations: pattern rotation, null placement, and bearing estimation against transmitters of known location (WWV at 5/10/15 MHz, CHU, local AM broadcast stations), compared with great-circle bearings.
- 72-hour soak test under SigMonD service management.
- **Milestones:** integration readiness review, final demonstration, final report and poster, open-source release to the HamSCI GitHub organization.

## 7. Deliverables

1. Requirements specification and trade-study reports (semester 1).
2. Working hardware: two active elements, translator/combiner unit, cabling, and calibration fixture.
3. Complete design package: schematics, PCB files, bill of materials, mechanical drawings.
4. SigMonD steering client software with calibration tooling, contributed upstream.
5. Test and calibration report with measured patterns, null depths, and bearing accuracy.
6. Final capstone report, poster, and public demonstration.
7. Replication documentation for the HamSCI community.

## 8. What You Will Learn

This project spans the full arc of a real instrument development effort inside an active, NSF-funded research collaboration:

- **RF/analog:** antenna theory, low-noise active antennas, mixers and phase-locked oscillators, filter design, frequency planning, RF cascade analysis, PCB layout;
- **Digital/software:** software-defined radio, digital beamforming and array calibration, Linux services, multicast networking, Python/C development in the ka9q-radio/SigMonD ecosystems;
- **Systems engineering:** requirements definition, trade studies, design reviews, integration, verification against a specification;
- **Professional practice:** open-source collaboration with an international volunteer engineering community, and the chance to see your hardware design replicated at stations around the world. Strong results are candidates for presentation at the HamSCI Workshop and publication in venues such as *QST*.

An amateur radio license is helpful and the club (W3USR) will happily get you licensed, but the project is receive-only, so a license is optional.

## 9. Team Roles (2–3 students)

- **RF/analog hardware lead (EE):** antennas, amplifiers, translator, filters, combiner, calibration hardware.
- **Software/DSP lead (CE or EE):** ka9q-radio/SigMonD integration, combining and steering algorithms, calibration software, demonstration interface.
- **Systems and test lead (EE or CE):** requirements, RF budget, test planning, calibration campaign, field measurements, documentation. In a two-person team, these duties are shared across the other two roles.

## 10. Resources Provided

- A complete PSWS HF Receiver station (RX-888 MKII SDR, GPSDO, TS1 TimeSync, Linux mini-PC) and a field test site.
- University RF laboratory test equipment (VNA, spectrum analyzer, signal generators).
- Component and fabrication budget through the sponsor. [Amount to be confirmed.]
- Mentorship from the sponsor and from HamSCI/TAPR volunteer engineers who designed the current PSWS hardware and software.
- The open-source ka9q-radio and SigMonD codebases and their developer communities.

## 11. References

1. Frissell, N. A. (2026), "The HamSCI Personal Space Weather Station HF Receiver," *QST*, August 2026, pp. 30–33. (Copy available from the sponsor.)
2. HamSCI Personal Space Weather Station: https://hamsci.org/psws and https://www.psws.hamsci.org
3. SigMonD, the HamSCI signal monitor daemon: https://github.com/HamSCI/sigmond
4. ka9q-radio, by Phil Karn, KA9Q: https://github.com/ka9q/ka9q-radio
