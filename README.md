# Normal-Oblique-Fresnel-Lab
# Fresnel EM Laboratory

### Interactive Electromagnetic Reflection & Transmission Analyzer for a Two-Medium Interface

**Fresnel EM Laboratory** is an interactive browser-based scientific simulation for studying the reflection, transmission, refraction, phase, power flow, and attenuation of electromagnetic waves at a planar interface between two media.

The project combines **Maxwell's electromagnetic boundary conditions**, **Snell's law**, **complex Fresnel coefficients**, **TE/TM polarization**, **complex material properties**, **energy-conservation verification**, and real-time field visualization into a single computational laboratory.

The central scientific question investigated by the project is:

> **How do material properties, electromagnetic impedance/admittance, polarization, incidence angle, and absorption determine the reflection and transmission of electromagnetic energy at an interface?**

The repository contains a complete standalone implementation in:

```text
Fresnel_EM_Laboratory.html
```

The simulation runs locally in a modern web browser and uses an offline deterministic physics engine.

---

# 1. Scientific Motivation

When an electromagnetic wave reaches the boundary between two different materials, the wave generally divides into two components:

- a **reflected wave**, which propagates back into the first medium;
- a **transmitted/refracted wave**, which enters the second medium.

The amount of reflected and transmitted energy depends on:

- incidence angle,
- TE/TM polarization,
- relative permittivity,
- relative permeability,
- electrical conductivity,
- frequency,
- electromagnetic loss,
- and the impedance/admittance mismatch between the two media.

The classical **Fresnel equations** describe this behavior.

However, simple Fresnel calculators often report only reflection and transmission coefficients without showing:

- their complex phase,
- the distinction between field and power coefficients,
- electromagnetic energy conservation,
- lossy-medium behavior,
- evanescent fields,
- Brewster-angle behavior,
- total internal reflection,
- or the connection with Maxwell boundary conditions.

This project brings these effects together into one interactive laboratory.

---

# 2. Major Features

The simulator provides:

- Real-time incident, reflected, and transmitted electromagnetic-field visualization
- TE / s-polarization analysis
- TM / p-polarization analysis
- Mixed-polarization visualization
- Complex-valued Fresnel coefficients
- Power reflectance and transmittance
- Energy-conservation verification
- Complex permittivity support
- Electrical conductivity support
- Lossy dielectric modeling
- Evanescent-field detection
- Total internal reflection analysis
- Brewster-angle calculation
- Critical-angle calculation
- Wavelength calculation in both media
- Attenuation-length estimation
- Angular Fresnel sweeps
- Reflection-phase visualization
- TE/TM comparison
- Built-in numerical validation
- Natural-language experiment parser
- Beginner/intermediate/advanced explanations
- Experiment JSON export
- Printable experiment reports
- Offline deterministic physics calculations

---

# 3. Physical Model

The simulator considers two homogeneous and isotropic media separated by a planar boundary at

```text
z = 0
```

A monochromatic electromagnetic plane wave is incident from **Medium 1** onto **Medium 2**.

The simulator uses the phasor convention

```text
exp[i(k·r - ωt)]
```

where

- `k` is the wave vector,
- `r` is position,
- `ω` is angular frequency,
- `t` is time.

The angular frequency is

```text
ω = 2πf
```

where `f` is the electromagnetic-wave frequency.

---

# 4. Complex Material Model

The simulator supports both lossless and lossy media.

For the phasor convention used by the implementation, the effective complex permittivity is represented as

```text
εeff = ε0(εr' + iεr'') + iσ/ω
```

where

- `ε0` = vacuum permittivity,
- `εr'` = real relative permittivity,
- `εr''` = dielectric-loss contribution in the program's convention,
- `σ` = electrical conductivity,
- `ω` = angular frequency.

The permeability is

```text
μ = μ0 μr
```

where

- `μ0` = vacuum permeability,
- `μr` = relative permeability.

The complex propagation constant is then determined from

```text
k = ω√(μεeff)
```

Because `εeff` and therefore `k` can be complex, the same formulation can describe both propagating and attenuating electromagnetic waves.

---

# 5. Snell's Law from Wave-Vector Conservation

At a planar interface, the tangential component of the wave vector must remain continuous.

Therefore,

```text
kx = k1 sin(θi)
```

where `θi` is the incidence angle.

For each medium,

```text
kz,j = √(kj² - kx²)
```

where `kz,j` is the component of the propagation vector normal to the interface.

This formulation automatically generalizes Snell's law to complex materials.

For ordinary lossless media, it reduces to the familiar equation

```text
n1 sin(θ1) = n2 sin(θ2)
```

---

# 6. Passive Wave-Vector Selection

When complex square roots are involved, more than one mathematical branch may exist.

The simulator selects the physically appropriate **passive branch** of `kz`.

This ensures that the transmitted field either

```text
propagates away from the interface
```

or

```text
decays into the second medium
```

rather than growing exponentially in an unphysical manner.

This is particularly important for:

- absorbing materials,
- conductive materials,
- evanescent fields,
- total internal reflection.

---

# 7. Maxwell Boundary Conditions

The Fresnel equations originate from Maxwell's electromagnetic boundary conditions.

For an interface without an imposed surface current, the tangential electric and magnetic fields must satisfy continuity conditions.

Conceptually,

```text
E1,t = E2,t

H1,t = H2,t
```

Applying these conditions to incident, reflected, and transmitted plane waves produces the Fresnel reflection and transmission coefficients.

The simulator implements this through **wave admittance**.

---

# 8. TE and TM Polarization

Electromagnetic waves at an interface are naturally separated into two independent polarizations.

## TE / s Polarization

For **Transverse Electric (TE)** polarization, the electric field is perpendicular to the plane of incidence.

The TE wave admittance used by the simulator is

```text
YTE = kz / (ωμ)
```

---

## TM / p Polarization

For **Transverse Magnetic (TM)** polarization, the magnetic field is perpendicular to the plane of incidence while the electric field lies in the plane of incidence.

The TM wave admittance is

```text
YTM = ωεeff / kz
```

Because TE and TM waves have different wave admittances, they generally experience different reflection and transmission at oblique incidence.

---

# 9. Fresnel Reflection Coefficient

Using the wave admittances on the two sides of the interface, the simulator calculates

```text
r = (Y1 - Y2) / (Y1 + Y2)
```

The coefficient `r` is generally complex:

```text
r = |r| exp(iφr)
```

Its magnitude determines the reflected field amplitude while its argument represents the phase shift introduced by reflection.

---

# 10. Fresnel Transmission Coefficient

The tangential electric-field transmission coefficient is

```text
t = 2Y1 / (Y1 + Y2)
```

Like `r`, the coefficient `t` may also be complex.

The simulator therefore retains both its amplitude and phase.

---

# 11. Important Coefficient Convention

The implementation reports **tangential electric-field coefficients**.

This is particularly important for TM polarization.

Some optics textbooks define TM coefficients using the complete electric-field vector rather than its tangential boundary component. Such definitions can introduce different signs or geometric projection factors.

The coefficients used by this simulator are internally consistent with its electromagnetic boundary-admittance formulation.

---

# 12. Field Coefficients Are Not Power Coefficients

One of the most important scientific distinctions in the project is between electromagnetic **field amplitudes** and electromagnetic **power**.

The reflection coefficient `r` is a field-amplitude coefficient.

Power reflectance is

```text
R = |r|²
```

Therefore `R` represents the fraction of incident electromagnetic power reflected by the interface.

---

# 13. Power Transmittance

The transmitted power cannot generally be calculated simply as

```text
|t|²
```

because the two media can have different electromagnetic admittances.

The simulator instead evaluates

```text
T = |t|² Re(Y2) / Re(Y1)
```

where `T` is the normalized power crossing the interface into Medium 2.

Therefore,

```text
T ≠ |t|²
```

in general.

This distinction is essential for physically correct Fresnel calculations.

---

# 14. Energy Conservation

For two lossless media supporting propagating waves, conservation of electromagnetic energy requires

```text
R + T = 1
```

within numerical precision.

The simulator continuously calculates the residual

```text
R + T - 1
```

and displays it to the user.

This provides a direct numerical verification that the calculated field coefficients are consistent with electromagnetic power conservation.

---

# 15. Lossy Media

For absorbing media, electromagnetic energy entering Medium 2 may subsequently be converted into heat or other forms of material excitation.

The simulator distinguishes:

```text
interface transmission
```

from

```text
subsequent absorption inside Medium 2.
```

The displayed `T` therefore represents normalized power entering the second medium at the interface.

Attenuation then occurs as the field propagates through the lossy material.

---

# 16. Electromagnetic Attenuation

Suppose the transmitted normal wave vector is

```text
kz = β + iα
```

Using

```text
exp(i kz z)
```

gives

```text
exp(iβz) exp(-αz)
```

Therefore

```text
α = Im(kz)
```

determines spatial field attenuation.

The simulator estimates the amplitude attenuation length using

```text
L = 1/α
```

when attenuation is present.

---

# 17. Brewster Angle

For suitable lossless materials, TM reflection can become zero at a special incidence angle known as the **Brewster angle**.

For common nonmagnetic materials,

```text
θB = arctan(n2/n1)
```

For air to glass,

```text
n1 ≈ 1
n2 ≈ 1.5
```

giving

```text
θB ≈ 56.31°
```

Near this angle,

```text
RTM → 0
```

for the ideal lossless interface.

The project includes an

```text
Air → glass · Brewster study
```

preset specifically for investigating this phenomenon.

---

# 18. Critical Angle

When an electromagnetic wave travels from a medium with higher refractive index into one with a lower refractive index,

```text
n1 > n2
```

a critical angle can exist:

```text
θc = asin(n2/n1)
```

Above this angle, the transmitted wave no longer propagates normally into Medium 2.

---

# 19. Total Internal Reflection

For

```text
θi > θc
```

the system enters **Total Internal Reflection (TIR)**.

In the ideal lossless case,

```text
R = 1
```

and the normal transmitted power becomes

```text
T = 0.
```

However, the electromagnetic field in Medium 2 is not identically zero.

Instead, an **evanescent field** penetrates into the second medium and decays exponentially.

The simulator explicitly detects and visualizes this situation.

---

# 20. Evanescent Fields

During total internal reflection, the normal wave-vector component becomes predominantly imaginary.

The transmitted field behaves approximately as

```text
E(z) ∝ exp(-αz)
```

rather than propagating indefinitely.

This is an important distinction:

```text
nonzero transmitted field
```

does not necessarily imply

```text
nonzero transmitted normal power.
```

This phenomenon is relevant to:

- frustrated total internal reflection,
- optical waveguides,
- prism coupling,
- near-field optics,
- photonics,
- evanescent sensors.

---

# 21. Live Electromagnetic Field Reconstruction

The central simulation canvas reconstructs representative incident, reflected, and transmitted sinusoidal fields.

The animation uses the same complex Fresnel solver responsible for the numerical results.

The reflected field incorporates

```text
|r|
```

and

```text
arg(r)
```

while the transmitted field incorporates

```text
|t|
```

and

```text
arg(t).
```

When `kz` contains an imaginary component, exponential spatial attenuation is also included.

The animation is intended as an educational reconstruction of the calculated phasor solution rather than a full FDTD or finite-element Maxwell simulation.

---

# 22. Angular Response Laboratory

The simulator automatically performs an angular sweep from approximately

```text
0° → 89.9°
```

using the same complex Fresnel solver as the live calculation.

The angular laboratory displays:

- TE reflectance
- TM reflectance
- TE transmittance
- TM transmittance
- TM reflection phase
- Energy balance `R + T`
- Brewster-angle marker
- Critical-angle marker

This makes it possible to investigate the complete angular response rather than evaluating only one incidence angle.

---

# 23. Numerical Validation Laboratory

The project includes built-in analytical validation cases.

Examples include:

| Test | Expected result |
|---|---:|
| Air → glass, normal-incidence reflectance | `R ≈ 0.04` |
| Air → glass, normal-incidence transmittance | `T ≈ 0.96` |
| Air → glass Brewster angle | `≈ 56.31°` |
| Glass → air at 60° | `R ≈ 1` |

For example, at normal incidence for air and glass,

```text
n1 = 1
n2 = 1.5
```

and

```text
R = |(n1 - n2)/(n1 + n2)|²
```

therefore

```text
R = |(1 - 1.5)/(1 + 1.5)|²

R = 0.04.
```

For the lossless interface,

```text
T = 1 - R = 0.96.
```

These values provide direct reference cases for checking the numerical engine.

---

# 24. Offline Experiment Parser

The project includes a rule-based experiment parser.

For example:

```text
632.8 nm TM wave, 5 V/m, air into glass at 56 degrees
```

can automatically configure several simulator parameters.

The parser can recognize selected:

- wavelength units,
- frequency units,
- electric-field amplitudes,
- incidence angles,
- TE/TM polarization,
- air-to-glass experiments.

Importantly, the parser does **not** replace the physics solver.

It only translates human-readable experiment descriptions into numerical inputs.

The deterministic Fresnel engine remains authoritative.

---

# 25. Built-In Material Presets

The current simulator provides presets for:

```text
Air
Vacuum
Glass
Water
Acrylic
Silicon
Lossy dielectric
Conductor
Custom material
```

Users can also directly modify:

```text
εr'
εr''
μr
σ
```

for both media.

---

# 26. Experiment Presets

Several useful physical experiments are included.

### Air → Glass: Brewster Study

Demonstrates suppression of TM reflection near the Brewster angle.

### Normal Incidence

Provides the simplest analytical Fresnel reference case.

### Glass → Air: Total Internal Reflection

Demonstrates the critical-angle transition and evanescent field.

### Air → Lossy Dielectric

Shows attenuation and complex-valued Fresnel coefficients.

### Identical Media

Demonstrates electromagnetic matching.

For identical materials,

```text
Y1 = Y2
```

and therefore

```text
r = 0.
```

The ideal interface becomes electromagnetically invisible.

---

# 27. User-Controlled Inputs

The laboratory allows control over:

```text
Incident electric-field amplitude E0
Frequency f
Incidence angle θi
Polarization
Initial phase
Animation speed
Relative permittivity εr'
Dielectric-loss term εr''
Relative permeability μr
Conductivity σ
Medium 1
Medium 2
```

---

# 28. Calculated Outputs

The physics engine calculates and displays:

```text
Complex TE reflection coefficient
Complex TM reflection coefficient

TE reflectance
TM reflectance

TE transmittance
TM transmittance

Wavelength in Medium 1
Wavelength in Medium 2

Brewster angle
Critical angle

Attenuation length

R + T residual

Reflection phase

Evanescent/TIR condition
```

---

# 29. Research-Level Interpretation Modes

The application provides three explanation levels.

### Beginner

Explains the physical meaning of reflection, transmission, wavelength change, and energy partition.

### Intermediate

Introduces electromagnetic admittance, Fresnel equations, and TE/TM behavior.

### Advanced

Discusses:

- phasor conventions,
- complex permittivity,
- passive wave-vector branches,
- complex propagation,
- boundary-consistent coefficient conventions.

This makes the simulator useful for both introductory learning and more advanced electromagnetic analysis.

---

# 30. Export and Reproducibility

Experiments can be exported as JSON.

The exported data includes the experiment inputs and calculated numerical results.

This helps preserve computational experiments for:

- later analysis,
- comparison,
- research documentation,
- classroom assignments,
- reproducibility studies.

The project can also generate a printable experiment report directly in the browser.

---

# 31. Technology Stack

The complete scientific simulator uses:

```text
HTML5
CSS
Vanilla JavaScript
HTML Canvas API
Custom complex-number arithmetic
Native browser APIs
```

No external numerical physics library is required by the standalone simulator.

The repository also contains a Next.js development scaffold using technologies including:

```text
Next.js 16
React 19
TypeScript
Tailwind CSS
pnpm
```

The complete current Fresnel laboratory itself is implemented in:

```text
Fresnel_EM_Laboratory.html
```

while the current Next.js page remains a scaffold.

---

# 32. Project Structure

```text
fresnel-reflection-simulation/
│
├── Fresnel_EM_Laboratory.html
│   └── Complete standalone Fresnel simulation
│
├── app/
│   ├── globals.css
│   ├── layout.tsx
│   └── page.tsx
│
├── components/
│   └── ui/
│
├── lib/
│   └── utils.ts
│
├── public/
│
├── package.json
├── pnpm-lock.yaml
├── pnpm-workspace.yaml
├── next.config.mjs
├── postcss.config.mjs
├── tsconfig.json
└── .gitignore
```

---

# 33. Running the Simulation

The easiest method requires no installation.

Clone the repository:

```bash
git clone <your-repository-url>
```

Enter the directory:

```bash
cd fresnel-reflection-simulation
```

Then open:

```text
Fresnel_EM_Laboratory.html
```

in a modern browser.

---

# 34. Running with a Local Web Server

You can optionally use Python:

```bash
python -m http.server 8000
```

Then navigate to the local server and open:

```text
Fresnel_EM_Laboratory.html
```

---

# 35. Running the Next.js Scaffold

For development of the Next.js portion, install Node.js and `pnpm`.

Then:

```bash
pnpm install
```

Start the development server:

```bash
pnpm dev
```

The current Next.js interface is a scaffold. The complete physics laboratory remains in the standalone HTML implementation unless it is later migrated into React.

---

# 36. Recommended Scientific Experiments

## Experiment 1 — Normal Incidence

Use:

```text
Medium 1: Air
Medium 2: Glass
Angle: 0°
```

Verify approximately:

```text
R = 0.04
T = 0.96
R + T = 1
```

---

## Experiment 2 — Brewster Angle

Use:

```text
Air → Glass
Polarization: TM
Angle ≈ 56.31°
```

Observe:

```text
RTM → 0
```

for the ideal lossless model.

Compare the result with TE polarization.

---

## Experiment 3 — TE vs TM

Sweep incidence angle from

```text
0° → 89.9°
```

and compare the TE and TM reflection curves.

At normal incidence they agree for ordinary isotropic media, while their behavior separates at oblique incidence.

---

## Experiment 4 — Total Internal Reflection

Use:

```text
Glass → Air
Angle: 60°
```

Observe:

```text
R ≈ 1
```

together with an evanescent field in Medium 2.

---

## Experiment 5 — Lossy Dielectric

Use the lossy dielectric preset.

Investigate:

- complex reflection coefficients,
- phase changes,
- attenuation,
- interface power transmission,
- absorption with depth.

---

## Experiment 6 — Electromagnetic Matching

Set both media to identical parameters.

Expected result:

```text
r ≈ 0
R ≈ 0
T ≈ 1
```

This demonstrates that reflection is caused by electromagnetic mismatch across the boundary.

---

# 37. Scientific Assumptions

The current model assumes:

- linear media,
- homogeneous media,
- isotropic media,
- plane electromagnetic waves,
- a planar interface,
- monochromatic excitation,
- no explicitly imposed surface currents,
- no explicitly imposed surface-charge sheets.

The simulator focuses on a **single interface**.

---

# 38. Current Limitations

The current version does not attempt to model:

- multilayer thin-film interference,
- anisotropic crystals,
- nonlinear optics,
- rough interfaces,
- finite Gaussian beams,
- diffraction,
- spatially varying materials,
- full 3D Maxwell geometries,
- FDTD propagation,
- FEM field solutions,
- automatically fitted experimental dispersion models.

Material parameters are user supplied rather than automatically retrieved from experimental optical databases.

For strongly dispersive materials, frequency-dependent experimental complex permittivity or refractive-index data should be used for quantitative research.

---

# 39. Why This Project Is Different

The objective is not merely to calculate a Fresnel coefficient.

The project connects the entire physical reasoning chain:

```text
Maxwell's equations
        ↓
Electromagnetic boundary conditions
        ↓
Wave-vector conservation / Snell's law
        ↓
TE/TM wave admittance
        ↓
Complex Fresnel coefficients
        ↓
Amplitude + phase
        ↓
Power reflectance/transmittance
        ↓
Energy conservation
        ↓
Brewster angle / TIR / attenuation
        ↓
Field visualization
        ↓
Physical interpretation
```

This makes the simulator useful as both a computational tool and an educational electromagnetic laboratory.

---

# 40. Educational Applications

The project can support courses and demonstrations in:

- Electromagnetic Theory
- Classical Electrodynamics
- Engineering Physics
- Optics
- Microwave Engineering
- Photonics
- Computational Physics
- Wave Propagation
- Electrical Engineering

It can help students connect mathematical Fresnel equations with their physical consequences.

---

# 41. Reproducibility

The live coefficients, angular sweeps, energy calculations, validation cases, and exported numerical results are generated from the same deterministic complex Fresnel solver.

This is important because it prevents the visual plots from being based on a different mathematical model than the displayed numerical coefficients.

Experiment configurations can additionally be exported to JSON for reproducible analysis.

---

# 42. Future Development

Possible extensions include:

- multilayer transfer-matrix method,
- thin-film interference,
- frequency-domain sweeps,
- wavelength-domain sweeps,
- material dispersion databases,
- complex refractive-index input,
- Jones-vector polarization analysis,
- Stokes parameters,
- CSV dataset export,
- automated numerical benchmarking,
- uncertainty propagation,
- experimental-data comparison,
- GPU-accelerated visualization,
- full React/Next.js migration,
- AI-assisted experiment configuration and interpretation.

---

# 43. References

The theoretical foundations of the project follow standard electromagnetic and optics literature:

1. D. J. Griffiths, *Introduction to Electrodynamics*, 4th Edition, Pearson, 2013.

2. J. D. Jackson, *Classical Electrodynamics*, 3rd Edition, Wiley, 1999.

3. M. Born and E. Wolf, *Principles of Optics*, 7th Edition, Cambridge University Press, 1999.

4. E. Hecht, *Optics*, 5th Edition, Pearson, 2017.

5. J. A. Stratton, *Electromagnetic Theory*, McGraw-Hill, 1941.

6. J. A. Kong, *Electromagnetic Wave Theory*, EMW Publishing, 1990.

7. S. Ramo, J. R. Whinnery, and T. Van Duzer, *Fields and Waves in Communication Electronics*, Wiley.

8. D. K. Cheng, *Field and Wave Electromagnetics*, 2nd Edition, Pearson.

---

# 44. Contributing

Contributions are welcome, particularly in:

- electromagnetic-model validation,
- numerical robustness,
- visualization,
- material databases,
- multilayer optics,
- documentation,
- accessibility,
- educational experiments.

For scientific changes, contributors should provide either an analytical derivation, trusted reference result, or numerical benchmark demonstrating the validity of the modification.

---

# 45. License

The uploaded project snapshot does not currently contain a license file.

Before describing the repository as open source, add an appropriate license such as:

```text
MIT
Apache-2.0
BSD-3-Clause
```

depending on how you want others to use and modify the project.

---

## Fresnel EM Laboratory

**From Maxwell boundary conditions to interactive electromagnetic-wave visualization.**

The project provides a computational bridge between:

**theory → equations → numerical calculation → validation → visualization → physical understanding.**
