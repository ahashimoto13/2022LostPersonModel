# Guide to Running the 2022 Lost Person Model

This repository contains the implementation of the agent-based model described in:

_Hashimoto, A., Heintzman, L., Koester, R. et al. An agent-based model reveals lost person behavior based on data from wilderness search and rescue. Sci Rep 12, 5873 (2022)._  https://doi.org/10.1038/s41598-022-09502-4

---

## Requirements

- MATLAB R2021b (or compatible)
- MATLAB Mapping Toolbox
- Python (for map generation)

---

## Map Files

The full set of preprocessed map files and GIS layer data is not stored directly in this repository due to size constraints.

These files are available via GitHub Releases.

After downloading, extract the archives and place the map files in the expected directory before running the MATLAB simulations.

Map generation procedures and GIS layer details are described in the manuscript and supplemental materials.

---

## Generating Maps for Initial Conditions (Python)

> **Note:** Updated map-generation scripts are available through [AGSTools](https://git.caslab.ece.vt.edu/hlarkin3/ags_grabber)

1. Open Python and navigate to the `importmap` project folder.
2. Open `feature_set.py` and enter the desired initial conditions (ICs).
3. Set the map `extent`.
4. Run `feature_set.py`.

The output will be map files of the form:
`BW_LFandInac_Zelev_[IC lat, IC lon].mat`

---

## Running the Simulations (MATLAB)

### Run all behavior probability distributions for 65 ICs

1. Run `main_hiker_t100.m`

This script uses:

- `parameters.m`
- `randsmpl.m`
- `run_replicate.m`
- `beh_dist_6.mat`
- `mapdim20_updatethresh.mat`

2. In `parameters.m`:
   - Adjust the number of behavior probability sets (`probs`)
   - Adjust the number of replicates (`replicates`)
   - Adjust the simulation time variable (`simT`) if necessary

3. Simulation outputs are saved to:
`\sims500t`

---

# Analysis

## Part 1: Energy Distance Analysis

### Goal

Calculate the energy statistic for the distance between the closest simulation point and the ISRID find point across all behavior distributions and replicates.

### Steps

1. Navigate to:
`\analysis energy dist`

2. Run `bestbeh_edist_clpts.m`

This script uses:

- `beh_dist_6.mat`
- `mapdim20_updatethresh.mat`

The script:

- Calculates the best-fitting simulations based on energy distance
- Finds the best behavior profile for all ICs using weighted closest-point distances
- Plots trajectory points for the best-fit behaviors
- Plots final trajectory points for the best-fit behaviors
- Plots weighted trajectory points
- Saves best-fit data to:
`\analysis energy dist\best fit data`

Outputs:

- `allbesthiker_edist500t.mat`
- `allclosestpoints500t.mat`
- `allweightedbesthiker_edist500t.mat`

3. Run `plotbehaviors.m`

This script:

- Plots bar charts of behavior probability distributions with weights, energy distances, and distances from the IPP to the find point
- Plots the sensitivity analysis for `L`
- Sorts ICs by weights and behaviors
- Plots weights versus distances
- Generates several figures used in the manuscript

---

## Part 2: Leave-One-Out Cross Validation (LOOCV)

### Goal

Use leave-one-out cross validation to determine best-fitting behavior profiles based on energy distance.

For each IC:
- One IC is removed
- The average weighted behavior profile is calculated from the remaining ICs
- The resulting behavior profile is then used to rerun simulations for the omitted IC

### Steps

1. Navigate to:
`\analysis LOOCV`

2. Run `bestbeh_LOO1.m`

This script uses:

- `beh_dist_6.mat`
- `mapdim20_updatethresh.mat`

The script:

- Runs the LOOCV analysis
- Finds and saves best-fit behaviors for each IC
- Saves outputs to:
`\analysis LOOCV\fits500\bestbeh_icf#`

3. Run `main_LOO2.m`

This script uses:

- `parameters.m`
- `randsmpl.m`
- `run_replicate.m`
- `beh_dist_6.mat`
- `mapdim20_updatethresh.mat`

The script:

- Runs simulations using the LOOCV-derived behavior profiles
- Allows adjustment of:
  - Simulation duration (`100` hours)
  - Number of replicates (`500`)
- Saves outputs to:
`\analysis LOOCV\simsfinal`

4. Run `leave1outfinal3.m`

This script:

- Calculates best-fit behaviors by energy distance for each IC
- Compares left-out energy distances to original energy distances
- Computes percentile rankings
- Generates:
  - Box plots
  - Bar charts
  - Histograms
