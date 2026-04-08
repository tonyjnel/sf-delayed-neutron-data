# Delayed Neutron Data Library for Spontaneous Fission

A comprehensive delayed neutron (DN) data library for spontaneous fission (SF) of 211 actinide nuclides spanning Z = 89 (Ac) through Z = 106 (Sg). The library provides total DN source strength and continuous energy spectra computed using the GEF Monte Carlo fission code.

This library addresses a longstanding gap in nuclear data: prior to this work, only three nuclides had published delayed neutron data for spontaneous fission (Cf-252, Cm-242, and Cm-244), and no tabulated SF DN energy spectra existed for any nuclide.

## Contents

```
.
├── dn_sf_nubar.csv          # Summary of nu_d for all 211 nuclides
├── endf/                    # ENDF-6 fission product yield files
│   └── GEFY_{Z}_{A}_s.dat  # 211 files, one per nuclide
├── spectra/                 # Delayed neutron energy spectra (YAML)
│   └── dn_spectrum_{Z}_{A}_sf.yaml
├── gef-output/              # Raw GEF output files
│   └── GEF_{Z}_{A}_sf.dat  # Full GEF results per nuclide
└── gef-input/
    └── DN_all_SF.in         # GEF input file for all 211 nuclides
```

## Data Formats

### CSV - `dn_sf_nubar.csv`

Summary table with one row per nuclide:

| Column | Description |
|--------|-------------|
| `Z` | Atomic number |
| `A` | Mass number |
| `Nuclide` | Symbolic name (e.g., Cf-252) |
| `nu_delayed` | Delayed neutron yield per fission |
| `nu_delayed_unc` | Uncertainty on nu_delayed |

### YAML Spectra - `spectra/`

Each file contains:
- Nuclide identification (Z, A, symbol)
- Total delayed neutron yield and uncertainty
- Mean delayed neutron energy (MeV)
- Energy grid: 200 bins from 0 to 10 MeV (50 keV bin width), reported at bin centers
- Probability density spectrum (1/MeV)
- Top 20 DN precursors with Q-values, nuclear temperatures, and fractional contributions

### ENDF - `endf/`

ENDF-6 formatted spontaneous fission product yield files (GEFY-10.1), produced directly by GEF. These contain independent and cumulative fission product yields suitable for summation calculations.

### GEF Output - `gef-output/`

Complete GEF output for each nuclide in XML format, including fission fragment yields, prompt neutron/gamma data, fission channel contributions, and delayed neutron precursor information.

## Method

### GEF Fission Model

Data were generated using GEF version 2025/V1.2 (Schmidt and Jurado, 2016), a Monte Carlo fission event generator that simulates the complete fission process from saddle to scission. For each event, GEF determines pre-neutron fission fragment yields using a macroscopic-microscopic model with five fission channels. Fragment excitation energy and angular momentum are computed via an energy-sorting mechanism, followed by prompt neutron and gamma emission via statistical evaporation.

Post-neutron fragments undergo beta decay using JEFF-3.3 decay data, including beta-n and beta-2n branching ratios, to compute DN precursor yields and total DN multiplicity for each fissioning system.

All 211 nuclides were calculated assuming ground-state SF (E* = 0) with an enhancement factor of 100 (10^7 fission events per nuclide).

### Energy Spectrum Construction

The composite DN spectrum for each nuclide is constructed by summing evaporation-model spectra over all identified DN precursors:

- Individual precursor spectrum: chi(E) = C * E * exp(-E/T), where T = sqrt(Q_bn / (3a)) and a = A_residual / 8
- Nuclear masses from the JEFF-3.3 mass table
- For beta-2n emitters, an effective Q-value of Q_bn / 2 is used
- Composite spectrum is the probability-weighted sum over all precursors
- Spectra are evaluated on 200 bins from 0 to 10 MeV (50 keV width)

## Validation

The library was validated against the only three SF nuclides with published delayed neutron yields. All three agree within 1-sigma uncertainties:

| Nuclide | GEF | Published | Ratio | Reference |
|---------|-----|-----------|-------|-----------|
| Cf-252 | 0.00893 +/- 0.00072 | 0.0086 +/- 0.0010 | 1.04 | Cox et al. 1958 |
| Cm-244 | 0.00358 +/- 0.00028 | 0.0033 +/- 0.0010 | 1.09 | Mills 1995 |
| Cm-242 | 0.00119 +/- 0.00010 | 0.0013 +/- 0.0003 | 0.92 | Mills 1995 |

Spectral shapes peak near 0.3 MeV and compare favorably with ENDF/B-VIII.0 evaluated DN spectra for thermal neutron-induced fission.

## Nuclide Coverage

211 nuclides across 18 elements:

| Z | Element | Isotopes |
|---|---------|----------|
| 89 | Ac | 224--231 |
| 90 | Th | 225--235 |
| 91 | Pa | 226--238 |
| 92 | U  | 230--240 |
| 93 | Np | 231--242 |
| 94 | Pu | 235--244 |
| 95 | Am | 237--245 |
| 96 | Cm | 242--252 |
| 97 | Bk | 242--252 |
| 98 | Cf | 244--256 |
| 99 | Es | 246--258 |
| 100 | Fm | 248--260 |
| 101 | Md | 250--262 |
| 102 | No | 251--264 |
| 103 | Lr | 251--264 |
| 104 | Rf | 253--265 |
| 105 | Db | 255--268 |
| 106 | Sg | 258--266 |

## Limitations

- Energy spectra use an evaporation model rather than measured spectral data for individual precursors.
- Traditional 6-group and 8-group DN parameters (group abundances and decay constants) are not included.
- Uncertainties reflect Monte Carlo statistical variation and do not include systematic model uncertainties from GEF or the underlying nuclear data.

## References

- K.-H. Schmidt and B. Jurado, *Nucl. Data Sheets* **131**, 107 (2016).
- S. A. Cox et al., *Phys. Rev.* **112**, 960 (1958).
- R. J. Tuttle, INDC(NDS)-107, 29-67, IAEA (1979).
- R. W. Mills, PhD thesis, Univ. Birmingham (1995).
- M. C. Brady and T. R. England, *Nucl. Sci. Eng.* **103**, 129 (1989).

## Authors

Anthony J. Nelson (nelson254@llnl.gov) and William Zywiec (zywiec1@llnl.gov)

## Acknowledgements

This material is based upon work supported in part by the Department of Energy National Nuclear Security Administration through the Nuclear Science and Security Consortium under Award Number DE-NA0003996. This work was also performed under the auspices of the U.S. Department of Energy by Lawrence Livermore National Laboratory under Contract DE-AC52-07NA27344.
