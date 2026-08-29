# Efficiency Testing and Ion Trapping with a PCB RFQ Cooler-Buncher

**Thomas Griffiths** — MSc Particle and Nuclear Physics, University of Edinburgh (2026)
Supervised by Dr. Moritz Pascal Reiter.

This repository holds the data-analysis notebooks behind an MSc dissertation
characterizing a printed-circuit-board (PCB) radio-frequency quadrupole
(RFQ) cooler-buncher: a device that transports, cools, and bunches ion beams
using a buffer gas and RF/DC electric fields, built from etched copper
electrodes on a stacked PCB rather than conventional machined rod
electrodes. The project has two experimental workstreams — **(1)** the
device's ion transmission efficiency, measured against a source+detector
baseline, and **(2)** its ion trapping and storage-time behavior, including
an unexpected chemical contamination effect discovered during longer-storage
tests.

## The physics

A quadrupole RFQ confines ions transversely with an oscillating RF field.
The strength of this confinement is set by the dimensionless Mathieu
stability parameter,

$$q = \frac{4eV_{\mathrm{RF}}}{m\,\Omega^2 r_0^2},$$

and the resulting time-averaged pseudopotential well depth,

$$D = \frac{q V_{\mathrm{RF}}}{4},$$

where $V_{pp}$ is the RF drive amplitude, $\Omega$ the drive frequency, $m$
the ion mass, and $r_0$ the field radius. Because the PCB electrodes in this
device are not perfectly parallel or symmetric (a consequence of board
assembly tolerances), $r_0$ is corrected to an effective value $r_{0,\text{eff}}$
derived from the measured electrode separations, used throughout for all
$q$-value and pseudopotential calculations.

Buffer-gas pressure inside the RFQ cannot be measured directly, so it is
inferred from an externally indicated gauge reading via a vacuum
conductance model (Dickel, PhD thesis, Univ. Giessen 2010):

$$C = 3.63\,A\sqrt{\frac{T}{M}}\left[1+\frac{3}{16}\frac{L C}{A}\right]^{-1}\ \ [\text{L/s}],$$

valid for both tubing segments and thin apertures, combined in series
(pumping path) or parallel (multiple apertures) as appropriate.

Where a trapped ion population is found to slowly convert into a second,
heavier species (see Headline Results below), that conversion is modelled
as a simple two-state population exchange,

$$N_1(t) = N_0e^{-kt}, \qquad N_2(t) = N_0\left(1-e^{-kt}\right),$$

fit by weighted least squares with Poisson uncertainties on each peak's
integrated area, giving a characteristic conversion time constant $\tau=1/k$.

## The notebooks

Each notebook corresponds to one or more figures/tables in the dissertation
and is documented internally with markdown cells tying its content back to
the relevant section. Grouped roughly by what they characterize:

**Detector and source characterization**
1. **`detector_bias_and_pulse_height.ipynb`** — MagneTOF detector bias
   voltage scan and average pulse-amplitude-vs-bias cross-check (Figures
   3.4, A.2). Used to select an operating bias voltage.
2. **`source_heating_power_survey.ipynb`** — broad initial survey of the ion
   source's usable heating range (Figure A.1).

**Transmission efficiency (Chapters 3–4)**
3. **`transmission_efficiency.ipynb`** — baseline detector-response
   characterization (Figures 3.5, 3.6) and the core baseline-vs-PCB-RFQ
   comparison giving the headline transmission efficiency result (Figure
   4.1, Table 4.1).

**RFQ field characterization (Chapter 3)**
4. **`gradient_scan.ipynb`** — axial DC gradient / injection-energy scan,
   confirming axial confinement (Figure 3.7).
5. **`rf_amplitude_scan.ipynb`** — RF amplitude scan confirming radial
   confinement, in both $V_{RF}$ and Mathieu $q$-value terms (Figures 3.8,
   3.9).
6. **`internal_pressure_scan.ipynb`** — buffer-gas pressure vs. ion
   transmission, using the vacuum conductance model above to convert gauge
   readings to internal RFQ pressure (Figure 3.10).

**Ion trapping and contamination (Chapters 3–4)**
7. **`ion_trapping_and_contamination.ipynb`** — the trapping/extraction
   cycle and bunch-quality classification methodology (Figures 3.3, 3.11),
   then the storage-time results: bunch shape evolution, identification of
   a growing secondary (contaminant) species, and its pressure- and
   tubing-material-dependence (Figures 4.2, 4.3, 4.4).

**Supporting / reference**
8. **`nuclear_chart.ipynb`** — chart of nuclides from the NUBASE2020
   evaluation, used as an introductory figure (Figure 1.1). Requires a
   separately-downloaded external dataset (see the notebook's own header).
9. **`threshold_scan_exploratory.ipynb`** — detector count rate vs. MPANT
   discriminator threshold. Exploratory only; not cited anywhere in the
   final dissertation.

## Headline results

- **Transmission efficiency:** 51 ± 7 (stat.) $^{+22}_{-0}$ (syst.) % of the
  baseline ion flux survives transport through the PCB RFQ.
- **Ion trapping:** stable trapping demonstrated at storage times up to a
  second — an order of magnitude beyond what had previously been tested on
  this device — with a bunch FWHM of 4.11 µs and a 13.0 µs transit time from
  the exit aperture to the detector. At longer storage times, a slow
  chemical conversion of the trapped population into a heavier hydrated
  cluster (tentatively Rb$^+$(H$_2$O)$_{19}$) was observed. The conversion
  rate scales with buffer gas pressure, pointing to the helium supply
  rather than the PCB itself as the water source, and was substantially
  slowed (τ increasing from 226 ± 14 ms to 531 ± 42 ms) by replacing a long
  PTFE gas inlet with a shorter copper one.

## Repository layout

```
detector_bias_and_pulse_height.ipynb
source_heating_power_survey.ipynb
transmission_efficiency.ipynb
gradient_scan.ipynb
rf_amplitude_scan.ipynb
internal_pressure_scan.ipynb
ion_trapping_and_contamination.ipynb
nuclear_chart.ipynb
threshold_scan_exploratory.ipynb

HVControl/            # raw .mpa files, detector bias scan       [gitignored]
CurrentControl/        # raw .mpa files, source heating survey    [gitignored]
EfficiencyControl/      # raw .mpa files, transmission efficiency  [gitignored]
Gradient/               # raw .mpa files, DC gradient scan         [gitignored]
RFPCB/                  # raw .mpa files, RF amplitude scan        [gitignored]
Pressure/               # raw .mpa files, pressure scan            [gitignored]
IonTrap/                # raw .mpa files, ion trapping             [gitignored]
ThresholdControl/       # raw .mpa files, threshold scan           [gitignored]
nubase2020.txt          # external NUBASE2020 dataset              [gitignored, download separately]

thesis/
    *.csv               # one per figure/table, with a commented header
                         # describing its purpose and dissertation reference
    raw/                # archived copies of the underlying .mpa files,
                         # organized per figure, for full provenance
```

The raw `.mpa` data folders and the NUBASE dataset are excluded from this
repository via `.gitignore` (see the notebooks' own data-loading cells for
exact expected folder names and file-naming conventions). Each notebook's
final cell(s) export a documented CSV of the plotted data and archive the
specific raw files that produced it into `thesis/raw/`, so figures remain
independently reproducible/traceable without needing the full raw dataset
committed to git.


## Running

Each notebook is self-contained and can be run independently — none share
state with each other. Each expects its own raw data folder (see table
above) to exist alongside it; if that folder is absent (e.g. after a fresh
clone, since raw data isn't committed), the notebook's file-discovery cells
will simply find no files rather than failing outright.
`nuclear_chart.ipynb` additionally requires `nubase2020.txt`, downloadable
from the [IAEA Atomic Mass Data Center](https://www-nds.iaea.org/amdc/) —
see that notebook's header for details.

## Known limitations / future work

- No detector bias plateau was observed across the tested range (Section
  3.4.2); the operating bias was chosen pragmatically, so cross-session
  detector gain drift can't be distinguished from a genuine change in
  transmission in the baseline-vs-PCB-RFQ comparison.
- Imprecise board assembly left the PCB RFQ's electrode separation
  asymmetric front-to-rear rather than uniformly oversized; this is
  simplified to a single $r_{0,\text{eff}}$ throughout, which captures the
  overall confinement strength but not the resulting field-multipole
  content itself.
- The secondary trapped species is tentatively identified as
  $\text{Rb}^+(\text{H}_2\text{O})_{19}$ from its arrival-time mass ratio; a more precise
  time-to-mass calibration was beyond the scope of this work and would help
  distinguish it from neighboring candidate cluster sizes.
- An RFQ "Switchyard" concept (a downstream beam-routing extension) was
  explored early in the project but ultimately dropped from the final
  scope; it appears only as future-work motivation in the dissertation
  text, not as analysis in this repository.

## Acknowledgements

Supervised by Dr. Moritz Pascal Reiter. The PCB RFQ's design and much of
the baseline test methodology draw on Tayemar Fowler-Davis's PhD thesis,
*"Pushing the Limits"* (University of Edinburgh, 2025). Lab colleagues
Jamie, Gemma, Toby, Gabi, and Peter provided lab support throughout.