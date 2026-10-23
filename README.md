<div align="center">

# Model Reduction for Sampled-Data Control

**Jupyter notebooks (2023) on reduced-order models of a flexible beam under sampled-data PD control: balanced truncation, error bounds, small-gain conditions and simulation comparisons.**

[![Python](https://img.shields.io/badge/Python-3.9-3776AB?style=flat-square&logo=python&logoColor=white)](https://www.python.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-notebooks-F37626?style=flat-square&logo=jupyter&logoColor=white)](https://jupyter.org/)
[![python-control](https://img.shields.io/badge/python--control-0.9.4-4B8BBE?style=flat-square)](https://python-control.readthedocs.io/)
[![Years](https://img.shields.io/badge/year-2023-555555?style=flat-square)](https://github.com/mavasbel-research/model_reduction/commits/main)
[![Status](https://img.shields.io/badge/status-research%20archive-lightgrey?style=flat-square)](#-known-issues)

<table>
<tr>
<td align="center" width="33%"><img src="ode_simulation_comparison.png" alt="Plant output of full and reduced models, continuous and sampled" width="100%"><br><sub>Full vs reduced model, continuous and sampled</sub></td>
<td align="center" width="33%"><img src="reduction_vs_sampling.png" alt="Small-gain condition over reduction order and sampling period" width="100%"><br><sub>Small-gain condition over order n̂ and period T</sub></td>
<td align="center" width="33%"><img src="performance_error_bound_3d.png" alt="Closed-loop error bound over Kp/Td and sampling period" width="100%"><br><sub>Error bound over K<sub>p</sub>/T<sub>d</sub> and T</sub></td>
</tr>
</table>

</div>

## 📑 Contents

- [What the notebooks do](#-what-the-notebooks-do)
- [Notebooks and run order](#-notebooks-and-run-order)
- [Requirements](#-requirements)
- [Getting started](#-getting-started)
- [Figures](#-figures)
- [Known issues](#-known-issues)

## 🔎 What the notebooks do

1. **Plant.** A fixed–fixed flexible beam of length 1.3 m discretised into 150 finite elements (ρ = 7746 kg/m³, E = 2.0·10¹¹ Pa, h = 3 mm, w = 0.1 m, 2 % modal damping; first modes 9.27, 25.56, 50.11 and 82.83 Hz), reduced with Craig–Bampton to 80 states. The plant is extended with the output derivative used by the controller.
2. **Reduced-order model.** Balanced truncation of the extended plant to order 5, with the Hankel-singular-value bound $\varepsilon = \sum_{i>\hat n}\sigma_i = 0.0652$ against an actual error $\lVert P_{ext}-\hat P_{ext}\rVert_\infty = 0.0298$.
3. **Controller.** A PD controller with low-pass filter, $C(s)=K_p\,\dfrac{(T_d/N+T_d)\,s+1}{(T_d/N)\,s+1}$ with $K_p = 80\cdot10^{-6}$, $T_d = 10$, $N = 10^3$, discretised with zero-order hold at $T = 0.75$ s.
4. **Closed-loop guarantees.** A small-gain test $(\lVert P_{cl}\rVert_\infty + \lVert E_{cl}\rVert_\infty)\,\kappa < 1$ and a closed-loop error bound $\varepsilon_{cl,u,y}$, swept over the reduction order and sampling period (2,160 cases) and over $K_p$ and $T$ (40,000 cases) with `joblib`.
5. **Simulation.** Continuous and sampled-data responses of the full and reduced closed loops to $u = \sin(15t)$, plus a two-mass example where a naively reduced model becomes unstable under sampling.

## 📒 Notebooks and run order

Notebooks hand results to each other through IPython `%store` (`ipython_config.json` enables auto-restore). Run them in this order:

| # | Notebook | Produces |
|:---:|:---|:---|
| 1 | `BaseBeamModel.ipynb` | Beam model $P$ and extended plant $P_{ext}$ (starts with `%store -z`, which clears stored values) |
| 2 | `SDAROMBeamModel.ipynb` | Balanced-truncation ROM $\hat P_{ext}$ and its bound |
| 3 | `BasePDModel.ipynb` | Continuous and discretised controller, closed loops |
| 4 | `SDAMORCompODESimulation.ipynb` | `ode_simulation_comparison.png` |
| 5 | `SDAMORMultiOrderPD.ipynb` | Sweep over order and period → `reduction_vs_sampling.png`, `performance_error_bound.png` |
| 6 | `SDAMROMultiParamsPD.ipynb` | Sweep over $K_p$ and period → `smallgain_cond_pd_kp_kd_ratio.png`, `performance_error_bound_3d.png` |
| — | `SDAMORExampleUnstaODESimulation.ipynb` | Two-mass example → `example_naive_mor_unstable.png` |

Shared helpers live in `custom_utils.py`: `ControlUtils.hinfnorm`, `ControlUtils.balaced_truncation` and `ODEUtils.solveSDLinearSystem` (sampled-data simulation: sample, hold, integrate the plant with RK45, update the controller).

## 🧰 Requirements

Pinned in `control.yml` (conda environment `control`):

| Package | Version |
|:---|:---|
| Python | 3.9.18 |
| control | 0.9.4 |
| slycot | 0.5.4 |
| numpy · scipy · sympy | 1.26.2 · 1.11.4 · 1.11.1 |
| matplotlib | 3.8.2 |
| joblib · tqdm · psutil · pillow | 1.3.2 · 4.66.1 · 5.9.0 · 10.1.0 |
| jupyter · ipykernel · ipywidgets | 1.0.0 · 6.26.0 · 8.1.1 |

Figures use `text.usetex: True`, so a LaTeX installation is needed.

## 🚀 Getting started

On Windows:

```bash
git clone https://github.com/mavasbel-research/model_reduction.git
cd model_reduction
conda env create -f control.yml
conda activate control
jupyter notebook
```

> [!NOTE]
> `control.yml` was exported on Windows and lists Windows-only packages (`pywin32`, `m2w64-*`, `vc14_runtime`). On Linux or macOS, create a Python 3.9 environment and install the pip versions from the table above instead.

## 🖼️ Figures

| | |
|:---:|:---:|
| <img src="example_naive_mor_unstable.png" alt="Naive model reduction becomes unstable under sampling" width="100%"><br><sub>Naively reduced model under sampling</sub> | <img src="smallgain_cond_pd_kp_kd_ratio.png" alt="Small-gain condition over controller gain ratio and period" width="100%"><br><sub>Small-gain condition over K<sub>p</sub>/T<sub>d</sub> and T</sub> |
| <img src="performance_error_bound.png" alt="Closed-loop performance error bound" width="100%"><br><sub>Closed-loop error bound</sub> | <img src="performance_error_bound_3d.png" alt="Closed-loop error bound surface" width="100%"><br><sub>Error-bound surface</sub> |

## ⚠️ Known issues

| Issue | Where |
|:---|:---|
| Sweep results are saved as `.pickle` files, which git ignores; run the job cell of each sweep notebook before the cells that load the pickle | notebooks 5 and 6 |
| The two-mass example prints the norm of the beam ROM (`hatMathcalPext`) instead of its own system | `SDAMORExampleUnstaODESimulation.ipynb`, cell 5 |
| The axis label says $K_p/K_d$ but the plotted ratio is $K_p/T_d$ | `SDAMROMultiParamsPD.ipynb`, cell 8 |
| Both sweep notebooks write `performance_error_bound.png`, so the last one run wins | notebooks 5 and 6 |

This repository does not include a license file.

---

<div align="center">
<sub>Part of <a href="https://github.com/mavasbel-research">mavasbel-research</a> · <a href="https://github.com/mavasbel">Marco Vásquez Beltrán</a></sub>
</div>
