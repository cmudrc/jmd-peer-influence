# Beyond Individual Trust: How Peer Influence Shapes the Adoption and Use of AI and Automation

Code for the paper of the same title, submitted to the *ASME Journal of Mechanical Design* (under review).

Aslan Noorghasemi, Sibibalan Jeevanandam, Neera Jain, Christopher McComb

The paper extends our IDETC 2026 conference paper (DETC2026-194708). It uses one trust-weighted peer-influence framework in two applications:

1. **AI adoption in engineering organizations**: an agent-based model of engineers who update their opinion of an AI tool from their own outcomes and from trusted colleagues.
2. **Automation use in vehicles**: a driver chooses between manual and automated driving with passengers present. Each occupant is a participant-specific model identified in a driving-simulator study.

In both applications, every simulation is paired with an individual-only baseline that removes peer influence, so its contribution can be measured directly.

## Getting the code

The two parts live in their own repositories and are linked here as Git submodules, pinned to the versions used in the paper. Clone everything with:

```sh
git clone --recursive https://github.com/cmudrc/jmd-peer-influence.git
```

If you already cloned without `--recursive`, run `git submodule update --init` inside the repository. GitHub's **Download ZIP** button does not include submodule contents; to download ZIP files, use each linked repository at tag `jmd-2026`.

## Repository layout

| Folder | Paper sections | Linked repository |
| --- | --- | --- |
| `organization/` | Organizational AI Adoption Model, Individual-Only Baseline, Results I, Appendices on CTMC estimation and surrogate validation | [`cmudrc/idetc-ai-adoption-paper`](https://github.com/cmudrc/idetc-ai-adoption-paper/tree/jmd-2026) at tag `jmd-2026` |
| `vehicle/` | Vehicle Automation Use Model, Results II, Additional Results appendix | [`cmudrc/av-social-trust`](https://github.com/cmudrc/av-social-trust/tree/jmd-2026) at tag `jmd-2026` |

## Paper figures and the scripts that make them

**Organization** (run from `organization/`):

| Figure | Script | Output |
| --- | --- | --- |
| Adoption with and without peer influence | `compare_social_vs_nosocial.py` | `figures/figure_social_effect_map.pdf`, `figures/figure_social_vs_nosocial_curves.pdf` |
| Minimum AI accuracy across organizational structures | `plot_min_accuracy.py` | `figures/figure_structure_min_accuracy.pdf` |
| Composition of adopters | `plot_loud_share_vs_accuracy.py` | `figures/figure_loud_share_grid.pdf` |
| ABM vs. reduced-model trajectories | `plot_trajectories.py` | `figures/abm_ode_two_examples.pdf` |
| ABM vs. fitted-ODE parity | `validate_fitted_ode.py` | `figures/validation_abm_to_fitted_ode/validation_abm_to_ode_parity_adoption.pdf` |
| Surrogate model selection | `select_surrogate_model.py` | `figures/surrogate_model_selection.pdf` |

**Vehicle** (all drawn by `vehicle/experiment/analysis/paper_figures/jmd/plot.py` from saved analysis results):

| Figure | Source analysis |
| --- | --- |
| Changed choices by track complexity; by passenger count | `track_social_effects` |
| Net change in automation use by the passengers' lean | `social_direction` |
| Social effects across the drive | `influence_timing` |
| Adaptive relationships (inbound trust, starting confidence) | `driver_confidence` |
| Illustrative drives | `example_cases` |
| Model schematic | `vehicle/experiment/assets/av-influence-comparison-lanes.pdf` (static) |

## Reproducing the results

Both parts require Python 3.11 or newer. Use a separate virtual environment for each part.

### Organization

```sh
cd organization
python -m pip install -r requirements.txt
```

Run these scripts in order (details and step numbers in the [organization README](https://github.com/cmudrc/idetc-ai-adoption-paper/blob/jmd-2026/README.md)):

1. `config.py`: builds the experiment grid in `settings.csv`
2. `run.py`: runs the ABM with peer influence (writes `models/`, about 28 GB)
3. `state.py`: SQLB state trajectories in `states/`
4. `fit_ode.py`, then `validate_fitted_ode.py`: CTMC generator fit and validation
5. `select_surrogate_model.py`, `create_surrogate.py`, `validate_surrogate_ode.py`: surrogate model
6. `plot_trajectories.py`, `plot_min_accuracy.py`, `plot_loud_share_vs_accuracy.py`: figures
7. `run_nosocial.py`: individual-only baseline with the same networks, opinions, trust and seeds (writes `states_nosocial/`)
8. `compare_social_vs_nosocial.py`: paired comparison tables (`results/nosocial/`) and figures

Sensitivity analyses: `sensitivity_1a.py`, `sensitivity_1b.py`.

### Vehicle

```sh
cd vehicle
python -m pip install -e '.[experiment]'
```

The participant-specific models (`experiment/data/combined_models.mat`) are **not included**; see Data availability. With that file in place:

1. `experiment/config.py`: settings
2. `experiment/tracks/generate_tracks.py`: shared driving tracks
3. `experiment/runner.py`: runs solo, decision-level and cognition-level simulations (9,272,016 models; resumable)
4. `experiment/analysis/main.py`: analyses
5. `experiment/analysis/paper_figures/jmd/plot.py`: paper figures

Without the private data, you can still run the unit tests (`python tests/tests.py`), the pipeline checks on fabricated trajectories (see the [experiment README](https://github.com/cmudrc/av-social-trust/blob/jmd-2026/experiment/README.md)), and the synthetic examples in the [vehicle README](https://github.com/cmudrc/av-social-trust/blob/jmd-2026/README.md).

## Data availability

The simulation code for both parts is public. Participant data from the driving-simulator study, including the fitted per-participant models (`vehicle/experiment/data/combined_models.mat`), are not included in this repository. The organization part needs no external data: all inputs are generated by `config.py`.

## Citation

```bibtex
@article{noorghasemi2026beyond,
  author  = {Noorghasemi, Aslan and Jeevanandam, Sibibalan and Jain, Neera and McComb, Christopher},
  title   = {Beyond Individual Trust: How Peer Influence Shapes the Adoption and Use of {AI} and Automation},
  journal = {Journal of Mechanical Design},
  note    = {Under review},
  year    = {2026}
}

@inproceedings{noorghasemi2026idetc,
  author    = {Noorghasemi, Aslan and McComb, Christopher},
  title     = {Designing for {AI} Adoption: A Multi-Scale Modeling Framework for Engineering Organizations},
  booktitle = {Proceedings of the ASME 2026 International Design Engineering Technical Conferences and Computers and Information in Engineering Conference},
  year      = {2026},
  venue     = {Houston, TX},
  number    = {DETC2026-194708}
}
```

## License

MIT. Each folder keeps the license of its source repository.

## Acknowledgment

Supported by The Boeing Company. The views expressed are those of the authors and do not necessarily reflect the views of The Boeing Company.
