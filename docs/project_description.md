# A Steerable Two-Element Receive Antenna Array for the HamSCI Personal Space Weather Station HF Receiver

**EE/CE Senior Design Capstone Project (two semesters)**

| | |
|---|---|
| **Project advisor** | Dr. Nathaniel A. Frissell, W2NAF, Department of Physics and Engineering, The University of Scranton (nathaniel.frissell@scranton.edu) |
| **Additional mentors** | HamSCI volunteer engineering community; The University of Scranton Amateur Radio Club (W3USR) |
| **Team size** | 2–3 students (EE and/or CE) |
| **Duration** | Two semesters (Fall 2026 – Spring 2027) |
| **Disciplines** | RF/analog circuit design, antennas, digital signal processing, embedded Linux software |

---

## 1. Background

The Ham Radio Science Citizen Investigation (HamSCI, [hamsci.org](https://hamsci.org)) Personal Space Weather Station (PSWS) is a network of inexpensive, ground-based scientific instruments operated by amateur radio volunteers and universities around the world. The network studies radio propagation, the ionosphere, and the coupled geospace system. More than 30 stations are already deployed, with another 25 planned under a US National Science Foundation (NSF) Distributed Array of Small Instruments (DASI) project; installations reach from Ellesmere Island in the Canadian Arctic to the Neumayer III Station in Antarctica.

The flagship instrument of this network is the **PSWS HF Receiver**: an RX-888 MKII 16-bit direct-sampling software-defined radio (SDR) that digitizes the entire high-frequency spectrum (0.1–60 MHz usable at its full sample rate) in a single channel and streams it over USB 3 to a low-cost Linux mini-computer. A GPS-disciplined oscillator (GPSDO) gives the system part-per-trillion frequency stability, and a Turn Island Systems TS1 TimeSync injector provides timing accuracy better than ±1 µs. The **ka9q-radio** software package by Phil Karn, KA9Q, uses a fast-convolution architecture to carve this wideband stream into hundreds of simultaneous, arbitrary-bandwidth "slice" receivers on a conventional CPU. HamSCI's **SigMonD** (Signal Monitor Daemon, [github.com/HamSCI/sigmond](https://github.com/HamSCI/sigmond)) coordinates the family of client applications that consume these slices: WSPR and FT8 spot decoding, WWV/WWVH/CHU Doppler and time-of-flight measurement, web SDR service, and more.

The system is described in the August 2026 issue of *QST* ("The HamSCI Personal Space Weather Station HF Receiver," pp. 30–33), available from the project advisor.

## 2. The Gap This Project Fills

Today, every PSWS HF Receiver listens through **one antenna**, typically an omnidirectional active vertical. An omnidirectional station measures *what* arrives and *when*, but it is blind to *where a signal comes from*. Direction sensitivity would open a new dimension for the network:

- **Direction finding** of scientific beacons (WWV, WWVH, CHU) to detect off-great-circle propagation caused by ionospheric disturbances;
- **Interference rejection**: steering a pattern null onto a local noise source or an interfering station, improving every downstream measurement;
- **Diversity reception** and pattern selection for weak-signal work in contesting, DXing, and the WSPR/FT8 monitoring that feeds HamSCI science.

The classic solution, dating to the Watson-Watt direction finders of the 1920s, is a pair of orthogonal antenna elements whose outputs are combined with controlled amplitude and phase. What is new here is the platform: the PSWS HF Receiver already digitizes 60 MHz of spectrum coherently on a single ADC, and its primary science band occupies only the bottom half. **The top 30 MHz of the digitizer's passband goes unused by the network's present HF science.** Occupying it costs the station its coverage from 30 to 60 MHz, which holds VHF low-band land mobile, the 6 m amateur band, and TV channel 2. That spectrum carries propagation science of its own, sporadic E and meteor scatter included, so this is a genuine trade. QPA accepts it deliberately, because this system's science target is the HF spectrum below 30 MHz. The trade also stays with each station, since fitting the array is a per-site choice. If a second antenna's 0.1–30 MHz output is translated up into 30–60 MHz and combined onto the receiver's single coaxial input, the existing hardware becomes a two-channel coherent receiver at almost no additional cost, and pattern steering becomes a pure software problem.

**This assumes the receiver runs at full sample rate.** ka9q-radio's default for the RX-888 MKII is 64.8 MSPS, which reaches only to about 30 MHz and leaves no upper half to translate into. QPA therefore requires the full 129.6 MSPS rate, within the LTC2208 converter's 130 MHz rating. Three conditions come with that choice. The host computer must sustain full rate, which ka9q-radio's documentation puts at a mid-range x86, with a Raspberry Pi 5 good for half rate only. The USB 3 link carries about 2.1 Gb/s continuously. And the receiver needs thermal headroom, because ka9q-radio runs at half rate by default precisely to avoid the heat problems users have reported at full speed, and the stock thermal pad may need improving; R8's 72-hour unattended soak will exercise this directly. One property of the choice helps the project: 129.6 MHz derives from the receiver's 27 MHz reference by small rational factors, which lowers Si5351 phase noise, and inter-channel phase stability is what R5 depends on.

## 3. Project Objective

Design, build, and demonstrate a **two-element orthogonal active receive antenna array with a coherent dual-channel front end** for the PSWS HF Receiver, together with the **ka9q-radio/SigMonD software** needed to combine the two channels with arbitrary complex weights and steer the array's receive pattern in software.

At the end of the project, a visitor to the demonstration should be able to watch the team rotate a synthesized antenna pattern from a keyboard, place a null on a transmitter of known bearing, and see the received signal drop on a live display.

**Where this hardware goes.** The array has two deployment targets, and the design must serve both.

1. **DASI2 amateur installations.** Volunteer-hosted PSWS sites, installed and maintained by the operator. This is the high-volume case, and it sets the cost, replicability, and ease-of-assembly targets.
2. **The U.S. Antarctic stations**: Amundsen-Scott South Pole (SPA), McMurdo (MCM), and Palmer (PLM), supporting NSF OPP-2332427, *Collaborative Research: The Next Generation of U.S. Geospace Research Facilities at South Pole, McMurdo, and Palmer Stations in Antarctica* (September 2024 to August 2029). This is the low-volume, high-consequence case, and it sets the environmental and serviceability targets. Dr. Frissell is the Institutional PI for the University of Scranton subaward to the New Jersey Institute of Technology on that award, so the project advisor is also the route to its requirements.

The second target is the harder constraint and is easy to design past accidentally. A unit that a ham can re-tension on a Saturday afternoon may be unserviceable at a station where the crew is small, the season is short, and a replacement part is a year away. Treat the Antarctic case as a design input from semester 1, carried through every trade study.

## 4. System Concept

```
 Element 1 (e.g., N–S)          Element 2 (e.g., E–W, orthogonal)
      │                               │
 ┌────▼──────┐                   ┌────▼──────┐
 │  Active   │                   │  Active   │
 │ antenna + │                   │ antenna + │
 │ amplifier │                   │ amplifier │
 └────┬──────┘                   └────┬──────┘
      │ 0.1–30 MHz                    │ 0.1–30 MHz
 ┌────▼──────┐                   ┌────▼──────────┐
 │ Low-pass  │                   │  Frequency    │   LO locked to
 │  filter   │                   │  translator   │◄── station GPSDO
 │ (≤30 MHz) │                   │ (→ 30–60 MHz) │   reference
 └────┬──────┘                   └────┬──────────┘
      │                               │ 30–60 MHz (band-pass filtered)
      └────────────┬──────────────────┘
              ┌────▼──────┐
              │ Combiner/ │  single coax, DC power via bias tee
              │ diplexer  │
              └────┬──────┘
              ┌────▼──────────────┐
              │ PSWS HF Receiver  │  RX-888 MKII: one ADC digitizes
              │ (existing, as-is) │  both channels coherently
              └────┬──────────────┘
              ┌────▼──────────────┐
              │ ka9q-radio        │  paired slices: f and its translated image
              ├───────────────────┤
              │ SigMonD steering  │  calibration + complex weights:
              │ client (new)      │  y = w₁·x₁ + w₂·x₂
              └───────────────────┘
```

**Hardware.** Two orthogonally oriented active receive elements (element type is a semester 1 trade study: active dipoles, loops, or verticals) each cover 0.1–30 MHz. Channel A passes directly through a low-pass filter. Channel B is mixed against a local oscillator that is phase-locked to the station's existing GPSDO reference, translating its 0.1–30 MHz spectrum into the 30–60 MHz half of the digitizer's passband; band-pass filtering suppresses the image, LO leakage, and out-of-band ingress (e.g., FM broadcast). A combiner/diplexer sums the two channels onto the receiver's single feedline. The station hardware itself is unmodified; its sample-rate configuration changes.

**Coherence.** Because one ADC samples both channels, they are inherently sample-synchronous. Because the translator LO is disciplined by the same GPSDO that disciplines the ADC clock, the inter-channel phase relationship is stable and can be measured once and corrected in software. A built-in calibration path (for example, a switched common test signal injected into both channels) lets the team measure the complex gain difference between channels as a function of frequency.

**Software.** ka9q-radio provides a slice receiver for channel A at *f*, and a matching slice for channel B at the frequency to which the translator maps *f*. A new SigMonD client application applies the calibration table and a user-commanded complex weight to each channel, then sums them. For a crossed orthogonal pair, the weighted sum synthesizes a dipole-like figure-8 pattern rotated to any commanded azimuth, which places a steerable null in any direction and supports bearing-line estimation on arriving signals (with the 180° ambiguity inherent to a two-element array). The client should follow SigMonD's existing architecture: subscribe to multicast RTP slice streams via ka9q-python, run as a managed systemd service, and log products to the shared database.

## 5. Draft Technical Requirements

These are the advisor's initial targets. Refining them into a complete, testable requirements specification, with each value justified by analysis, is the team's first deliverable.

| # | Requirement (initial target) |
|---|---|
| R1 | Two orthogonally oriented active receive elements covering 0.1–30 MHz (threshold: 1.8–30 MHz amateur/science bands) |
| R2 | Channel B frequency translator moves 0.1–30 MHz into the upper half of the digitizer's passband (30–60 MHz); LO phase-locked to the station GPSDO reference |
| R3 | Image, LO leakage, and alias products suppressed sufficiently that they do not degrade either channel's science use (team to derive dB requirements from the RX-888's dynamic range) |
| R4 | Single coaxial feed into the unmodified PSWS HF Receiver RF input; front-end electronics powered over the feedline via bias tee |
| R5 | Inter-channel amplitude and phase calibrated across the band; post-calibration stability sufficient for pattern steering (initial target: ~1 dB / a few degrees over hours and normal outdoor temperature swings) |
| R6 | SigMonD client software performs calibrated complex-weight combining of paired ka9q-radio slices with operator-commanded steering |
| R7 | Demonstrated null depth of at least 15 dB on a signal of known bearing (initial target) |
| R8 | Outdoor-rated enclosures and antennas; continuous unattended operation for at least 72 hours |
| R9 | Full documentation (schematics, board files, bill of materials, calibration procedure, software, and the host and sample-rate prerequisites of section 2) released open source so HamSCI volunteers can replicate the design: hardware designs under an appropriate open hardware license (such as the TAPR Open Hardware License), source code under the MIT license |
| R10 | Ruggedized sufficiently for installation at SPA, MCM, and PLM (section 3), extending R8. The team is to obtain the governing environmental and station-support requirements from Dr. Frissell and derive testable values for at least: operating and survival temperature; wind loading and rime or ice accretion on the elements; dry-snow ingress, which defeats seals that pass a rain test; UV and mechanical durability of plastics, cable jackets, and radomes at low temperature; and installation and service by station personnel, working from the team's documentation alone |
| R11 | Electrical safety and regulatory compliance. A DC-blocked static bleed path on each element and a bonded surge protector where the feedline enters the building, with antenna and mast grounding per NEC Article 810 and the host station's own grounding practice. Front-end LO and digital emissions designed to the FCC Part 15 Subpart B radiated limits for an unintentional radiator, with LO leakage from element 2 low enough that element 1 does not hear it. The team is to identify the governing standards and the applicable equipment-authorization path, and derive testable values |

**On R10.** Do not guess these numbers. Each station is a different environment, and hardware built to these numbers will actually be installed there. **Ask Dr. Frissell for the governing values**; as Institutional PI for the Scranton subaward on OPP-2332427 he is the route to the OPP project team and to station support documentation. Where a value is unavailable in semester 1, record the gap explicitly in the requirements specification and mark what depends on it.

**On R11.** The bias tee puts DC on an outdoor feedline and the translator puts an oscillator on an outdoor mast, so safety and emissions are design inputs from semester 1. LO leakage bites twice: it is an emissions question and a self-interference question at once, because anything element 2 radiates, element 1 can receive. The Antarctic stations carry their own electrical and grounding requirements; obtain them together with the R10 values.

### Success tiers

The requirements above define the full system. To keep the required scope honest, the advisor defines three success tiers. The two-semester plan targets the objective tier; the threshold tier alone is a complete, successful capstone.

- **Threshold (a successful capstone).** A bench-scale dual-channel system: prototype orthogonal elements, a working frequency translator, coherent two-channel capture through a single RX-888 with a stable measured inter-channel phase, a documented calibration procedure, and software combining with commanded weights demonstrated on bench signals (R1–R6 at prototype level).
- **Objective (the project goal).** The threshold system installed at the field site and demonstrated on the air: pattern rotation, a null of at least 15 dB on a transmitter of known bearing, bearing estimates compared with great-circle values, the 72-hour soak under SigMonD, and the open-source release (adds R7–R9, with R11 in force once the array goes outdoors).
- **Stretch (beyond expectations).** Hardware qualified against the Antarctic requirements of R10, with a verification matrix showing the evidence for each governing value, and a design package a HamSCI volunteer or station engineer can build from without contacting the team.

Everything above the threshold is upside. Because the project is grant funded, significant resources are available to help the team reach the upper tiers, beyond what is normally available to capstone projects (section 11).

## 6. Two-Semester Plan

### Semester 1: Requirements, Design, and Prototyping
- Requirements review with the advisor and HamSCI mentors; finalize the specification.
- Trade studies: antenna element type, array geometry and element spacing, LO frequency plan (high-side vs. low-side injection, guard bands, spectral inversion handling), mixer and filter topology, combiner design, calibration method.
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
- Present the project at the [2027 HamSCI Workshop](https://hamsci.org/hamsci2027), April 17–18, 2027, at the University of Scranton.
- **Milestones:** integration readiness review, final demonstration, HamSCI Workshop presentation (April 17–18), final report and poster, open-source release to the HamSCI GitHub organization.

**The workshop sets the schedule.** The HamSCI Workshop falls in mid-April, ahead of the end of the spring semester, so the team needs a working demonstration and presentable results about a month before the course's own final deadline. Plan semester 2 backward from April 17.

## 7. Deliverables

1. Requirements specification and trade-study reports (semester 1).
2. Working hardware: two active elements, translator/combiner unit, cabling, and calibration fixture.
3. Complete design package: schematics, PCB files, bill of materials, mechanical drawings.
4. SigMonD steering client software with calibration tooling, contributed upstream.
5. Test and calibration report with measured patterns, null depths, and bearing accuracy.
6. Final capstone report, poster, and public demonstration.
7. Replication documentation for the HamSCI community.
8. A presentation of the project at the [2027 HamSCI Workshop](https://hamsci.org/hamsci2027) (April 17–18, 2027, at the University of Scranton). This is an expectation of the project, and puts the team's work in front of the scientists and volunteer engineers who will deploy it.

## 8. What You Will Learn

This project spans the full arc of a real instrument development effort inside an active, NSF-funded research collaboration:

- **RF/analog:** antenna theory, low-noise active antennas, mixers and phase-locked oscillators, filter design, frequency planning, RF cascade analysis, PCB layout;
- **Digital/software:** software-defined radio, digital beamforming and array calibration, Linux services, multicast networking, Python/C development in the ka9q-radio/SigMonD ecosystems;
- **Systems engineering:** requirements definition, trade studies, design reviews, integration, verification against a specification;
- **Professional practice:** open-source collaboration with an international volunteer engineering community, and the chance to see your hardware design replicated at stations around the world. The team will present its work at the 2027 HamSCI Workshop, hosted at the University of Scranton (deliverable 8), and strong results are candidates for publication in venues such as *QST*.

This is also a deliberate career investment. The stack the project exercises (coherent receiver front ends, frequency planning, beamforming and array calibration, embedded Linux DSP) is the skill set that satellite communications, radar, cellular infrastructure, and software-defined radio employers interview for. A capstone that put working hardware in the field, with public design files, is a concrete answer to the interview question every new graduate faces: tell me about something you built.

An amateur radio license is helpful and the club (W3USR) will happily get you licensed, but the project is receive-only, so a license is optional.

## 9. Where This Leads

QPA is deliberately the first rung of a ladder, and the ladder is worth seeing from the start, because it changes a few design choices this year's team should make in semester 1.

**The limit of two elements.** Two co-located orthogonal elements produce a power pattern that is always symmetric under a 180° rotation, for every possible choice of complex weights. Any weighted sum of the two azimuth patterns cos φ and sin φ reduces to a constant plus a single cos(2φ) term, so the result is two broad lobes 180° apart with two nulls exactly midway between them. The relative phase between channels therefore acts as a null-depth control, trading depth against an omnidirectional response. Placing a null on any bearing works, which is what R7 asks for, and pointing a single unambiguous lobe takes a third channel. Crossed loops also suffer a polarization error on high-angle skywave, familiar as the "night effect" in classical direction finding, and correcting it requires a direct measurement of the incident polarization.

**What this project establishes is reusable.** The frequency translation into 30–60 MHz is a one-time exploit of a vacancy in this particular receiver's passband, and it does not extend to a third channel. Three other results carry forward to every later scheme, and they are the reason the project matters beyond its own demonstration:

1. **Inter-channel calibration** (R5): measuring a complex gain difference across the band and holding it stable over temperature. This is the hardest problem in any coherent array, and its difficulty is independent of channel count.
2. **The combining client's architecture.** Written over an N-channel data model, `y = Σ wₖ xₖ` supports everything below. Written for exactly two channels, it gets rewritten.
3. **The channel-estimate observable** h₂/h₁, which drives steering, direction finding, and the calibration check from one estimator.

The two-element demonstration is the proof that those three work. They are the durable deliverable.

**A four-channel receiver is coming.** A TAPR member is developing a coherent receiver with four channels, each covering 60 MHz. [Design in progress; specifications to be confirmed with the advisor.] That instrument removes both compromises QPA works around. Every channel would cover 0.1–60 MHz natively, so the sacrifice of 30–60 MHz described in section 2 disappears and sporadic E, meteor scatter, and 6 m science return. Four coherent channels at the antenna also change what is physically measurable:

| Configuration | What it adds |
|---|---|
| Two crossed loops plus an omnidirectional sense element | Resolves the 180° ambiguity. A cardioid has one lobe and one null. |
| Three orthogonal loops plus an electric whip | Measures the full magnetic field vector plus one electric component, so polarization becomes a measured quantity and elevation angle becomes observable. |
| Four elements with physical separation | A real aperture, with resolution improving toward the top of the band. |
| Any four-channel set, processed jointly | Superresolution direction finding (MUSIC, ESPRIT), which can separate up to three simultaneous arrivals. |

Elevation angle is the prize. A beacon at a known great-circle distance, observed with a measured arrival elevation, yields an ionospheric reflection height under a simple hop model. That is passive oblique sounding from a station that costs a small fraction of an ionosonde. Separating the ordinary and extraordinary magnetoionic modes, and separating one-hop from two-hop arrivals, would remove ambiguities that currently limit the network's Doppler and time-of-flight measurements.

**Candidate follow-on capstone projects.** Nothing below is committed. Each depends on funding, on student interest, and on what this year's team delivers.

| Project | Focus | Capability added |
|---|---|---|
| QPA (this project) | Two elements on one RX-888 | Calibration, steering client, a 15 dB null on a known bearing |
| Follow-on A | A third coherent channel and a sense element | Unambiguous bearings, and the first bearing cross-fix between two PSWS stations |
| Follow-on B | A vector sensor on the four-channel receiver | Elevation angle, polarization, mode separation, superresolution |
| Follow-on C | Network-scale direction finding | Distributed bearing products in the shared database, and Antarctic operations |

One rung needs no antenna development at all. Two stations, each reporting an ambiguous bearing line, produce an unambiguous fix by intersection. A distributed network resolves at the network level what a single two-element station cannot resolve locally, and QPA is its prerequisite.

The NSF award supporting the Antarctic deployment (OPP-2332427) runs through August 2029, so a multi-year sequence of capstone teams fits inside it, each inheriting a calibrated and documented system and adding one capability. Three decisions therefore cost almost nothing this year and repay the effort many times over: keep the client's data model general in the number of channels, write the calibration procedure for N channels and execute it for two, and log bearing products with their 180° ambiguity represented explicitly so a later cross-fix can consume them.

## 10. Team Roles (2–3 students)

- **RF/analog hardware lead (EE):** antennas, amplifiers, translator, filters, combiner, calibration hardware.
- **Software/DSP lead (CE or EE):** ka9q-radio/SigMonD integration, combining and steering algorithms, calibration software, demonstration interface.
- **Systems and test lead (EE or CE):** requirements, RF budget, test planning, calibration campaign, field measurements, documentation. In a two-person team, these duties are shared across the other two roles.

**Expanding the team.** Capstone students are expected to carry the majority of the project work and its management. Within that, the team is encouraged to expand as needed and appropriate: members of the TAPR/HamSCI community, who have very significant industry and scientific experience, and underclassmen.

## 11. Resources Provided

This project is grant funded. Significant resources are available to help students, beyond what is normally available to capstone projects:

- A complete PSWS HF Receiver station (RX-888 MKII SDR, GPSDO, TS1 TimeSync, Linux mini-PC) and a field test site, with a host able to sustain full-rate (129.6 MSPS) operation.
- University RF laboratory test equipment (VNA, spectrum analyzer, signal generators).
- Component and fabrication budget through the advisor's grant funding. [Amount to be confirmed.]
- Mentorship from the advisor and from HamSCI/TAPR volunteer engineers who designed the current PSWS hardware and software.
- The open-source ka9q-radio and SigMonD codebases and their developer communities.
- Claude Code accounts for each team member, for use in design analysis, software development, and documentation. AI use must follow University of Scranton academic integrity policy and the course instructor's rules, including disclosure of AI assistance in design reports.

## 12. References

1. Frissell, N. A. (2026), "The HamSCI Personal Space Weather Station HF Receiver," *QST*, August 2026, pp. 30–33. (Copy available from the project advisor.)
2. HamSCI Personal Space Weather Station: https://hamsci.org/psws and https://www.psws.hamsci.org
3. SigMonD, the HamSCI signal monitor daemon: https://github.com/HamSCI/sigmond
4. ka9q-radio, by Phil Karn, KA9Q: https://github.com/ka9q/ka9q-radio

---

*Project QPA (Quadrature Phased Array). Interested students should contact Dr. Frissell (nathaniel.frissell@scranton.edu).*

*This project supports the HamSCI Personal Space Weather Station effort, funded by NSF grants AGS-2045755, AGS-2432821, AGS-2432822, AGS-2432824, AGS-2432823, AGS-2431666, and OPP-2332427; NASA grants 80NSSC23K1322, 80NSSC25K7026, and 80NSSC26K0051; and Frankford Radio Club and ARDC grants.*
