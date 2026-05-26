# Idealized 19.75% HALEU UCO Pebble-Bed (OpenMC)

OpenMC neutronics and depletion of an idealized, single 19.75% HALEU UCO TRISO pebble in HTR-PM-style geometry. Contains reflective boundaries to approximate an infinite pebble lattic. Cross sections are ENDF/B-VIII.0.

The pebble is a 3 cm radius sphere with a 0.5 cm graphite shell. The fuel zone is packed at 9% pf with 13 thousand TRISO particles. Two notebooks: one for fresh-fuel k∞, spectrum, and U-235 reaction-rate ratios, the other for a 1080 EFPD (around 3 year) burnup sweep.

## Results

| Quantity | Value |
|---|---|
| Fresh-fuel k∞ | 1.625 |
| Power per pebble | 595 W (250 MWth / 420k pebbles) |
| Burn duration | 1080 EFPD (36 x 30 day steps) |
| U-235 at EOC | 48% of initial |
| Pu-239 at EOC | 6% of initial U-235 |
| Pu-239 share of fissions, EOC | 15% |
| Discharge burnup | 340 MWd/kgU (infinite lattice) |
| Doppler defect (300→1200 K) | –4.2 pcm |

In addition, a fuel-temperature sweep from 293K to 1200K was also performed to determine the Doppler reactivity feedback. This is the primary passive-safety mechanism in HTGRs. Results are detailed in the Temperature Reactivity section below.

Determined k∞ of 1.625 at fresh is consistent with the published range for 19.75% HALUE UCO TRISO under cold infinite-lattice assumptions, and runs about 0.07 above reported X-energy Xe-100 fresh k∞ with 15.5% HALUE. This lines up with the physics behind higher enrichment concentration. A finite-core k-effective would be roughly 20% lower once leakage and reactivity holds are folded in.

## Figures

**Key Isotopes vs Burnup** <br>
![](figures/isotope_inventory_depletion.png)

Figure 1: Key isotope tracking during a 1080 EFPD burn at 595 W per pebble. U-235 depletes to roughly 48% of initial, while Pu-239 increases from U-238 (µ,𝛾) capture and saturates at ~6% relative to initial U-235. Xe-135 reaches equilibrium within the first time step. By end of 1080 EFPD, ~15% of fission occur on bred Pu-239 rather than U-235, demonstrating partial-breeder behavior of thermal HALEU reactors



**Pebble k∞ Depletion** <br>
![](figures/pebble_kinf_depletion.png)

Figure 2: Pebble k∞ vs burnup for a 19.75% HALEU UCO TRISO pebble (HTR-PM geometry). Steep initial drop is from Xe-135 equilibrium. Linear decline is U-235 depletion. Projected discharge burnup ~ 340 MWd/kgU (infinite-lattice approx., real-core value of ~160 MWd/kgU after accounting for leakage and burnable poisions).

**Pebble Core Map** <br>
![](figures/pebble_core_map.png)

Figure 3: Geom cross-section of HALEU UCO Pebble bed with 9% fill. Reflective BC on the cube around the pebble, forming an infinite pebble lattice. The colors are trivial, but dots represent the TRISO particles.

**Spectrum and U-235 reaction-rate ratios** <br>
![](figures/AlphaP_fissionEta.png)

Figure 4: Thermal-spectrum flux in the fuel and the standard U-235 ratios: α (capture/fission), P_f, and η = ν · P_f.

## Temperature Reactivity

To address the idealized temperature assumption in the baseline model, a fuel-temperature
sweep was run from 293 K to 1200 K in 150 K increments, holding all other conditions
(geometry, enrichment, BCs) fixed. This isolates the Doppler broadening effect on U-238
resonance absorption.

**k∞ vs Fuel Temperature**
![k-inf vs temperature](figures/kinf_vs_temperature.png)

Figure 5: k∞ decreases monotonically with fuel temperature, driven by Doppler broadening
of the U-238 capture resonances. The drop from cold (~293 K) to operating temperature
(~1200 K) is approximately -4.2 pcm/K which is consistent with the strongly negative Doppler
feedback expected for HALEU TRISO fuels.

**Doppler Coefficient**
![Doppler coefficient](figures/doppler_coefficient.png)

Figure 6: The temperature reactivity coefficient (dk/dT) is negative across the entire
range and becomes less negative at higher temperatures, consistent with the 1/√T
dependence of Doppler broadening. The magnitude of ~ –4.2 pcm/K confirms robust
passive-safety behavior under power-transient conditions.

**Doppler Reactivity Swing**
![Doppler reactivity](figures/doppler_reactivity.png)

Figure 7: Cumulative reactivity insertion across the temperature sweep. The total Doppler defect must beovercome by excess reactivity at startup and is a key input to control rod worth
and shutdown margin calculations in a real core design.


## Modeling

| Quality | Description |
|---|---|
| Fuel | 19.75% HALEU UCO (~80/20 UO₂/UC₂), 10.9 g/cm³ |
| TRISO kernel / buffer / IPyC / SiC / OPyC outer radii | 215.5 / 312.5 / 352.5 / 387.5 / 427.5 µm |
| Pebble outer radius | 3.0 cm |
| Fuel-zone radius | 2.5 cm (0.5 cm graphite shell) |
| Packing fraction | 9% |
| BCs | reflective cube around the pebble |
| Cross sections | ENDF/B-VIII.0 |
| S(α,β) | `c_Graphite` on buffer / PyC / matrix |
| Fresh-fuel run | 100 batches × 2,000 particles, 20 inactive |
| Depletion run | 50 batches × 2,000 particles, 10 inactive, predictor, 30-day steps |
| Depletion chain | `chain_endfb80_pwr.xml` |

## Limitations

This is a screening-level infinite-lattice calculation, not a real safety
analysis. The big simplifications:

Reflective BCs. Infinite lattice means no leakage. A finite core with
reflectors, control rods, and burnable absorbers ends up with noticeably
lower k_eff and a different flux shape.

Single pebble, single pass. Real HTR-PM cycles each pebble through the core
~10 times. This calculation runs one pebble straight through to EOC.

PWR depletion chain. `chain_endfb80_pwr.xml` is built for LWR spectra. An
HTGR-specific chain would do a better job on graphite-moderated transmutation
paths, especially for minor actinides.

Constant power. 595 W/pebble for the whole burn, no online refueling, no
power redistribution.

## Layout

```
.
├── README.md
├── LICENSE
├── requirements.txt
├── pebble_model.py            # geometry + materials, shared by both notebooks
├── ModelingPebbleBed.ipynb    # fresh-fuel k_inf, spectrum, rate ratios
├── PebbleBedDepletion.ipynb   # 1080 EFPD burnup
├── Temperature.ipynb   # Temperature Sweep
└── figures/
```

## Running it

Download OpenMC and Conda beforehand. OpenMC has a compiled core, so conda is easier than pip:

```bash
conda create -n openmc -c conda-forge openmc python=3.13
conda activate openmc
pip install -r requirements.txt
jupyter lab
```

Cross sections and depletion chain (the host the OpenMC team uses):

- ENDF/B-VIII.0 library (~10 GB): https://anl.box.com/shared/static/uhbxlrx7hvxqw27psymfbhi7bx7s6u6a.xz
- ENDF/B-VIII.0 PWR depletion chain (27.5 MB): https://anl.box.com/shared/static/nyezmyuofd4eqt6wzd626lqth7wvpprr.xml

Set the paths in the first cell of either notebook:

```python
openmc.config['cross_sections'] = '~/lib80x_hdf5/cross_sections.xml'
openmc.config['chain_file']     = '~/chain_endfb80_pwr.xml'
```

## References

[1] Z. Zhang et al., "Current status and technical description of Chinese
2 × 250 MWth HTR-PM demonstration plant," Nucl. Eng. Des. 239 (2009) 1212.

[2] E.J. Mulder, W.A. Boyes, "Neutronics characteristics of a 165 MWth Xe-100
reactor," Nucl. Eng. Des. 357 (2020) 110415.

[3] P.A. Demkowicz, B. Liu, J.D. Hunn, "Coated particle fuel: historical
perspectives and current progress," J. Nucl. Mater. 515 (2019) 434.

[4] P.K. Romano et al., "OpenMC: A state-of-the-art Monte Carlo code for
research and development," Ann. Nucl. Energy 82 (2015) 90.

[5] IAEA-TECDOC-1694, "Evaluation of High Temperature Gas Cooled Reactor
Performance" (2013).


