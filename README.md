# Football Kinematics: Pitch Control & Space Creation from Tracking Data

Measuring how football players control and create space, using player tracking data and a physics-based approach in Python.

> **Status:** work in progress. This is a personal project by a physics student, combining kinematics, data analysis and football analytics. See the [roadmap](#roadmap) for what is done and what is planned.

## Motivation

A heatmap shows where a player *was*. It does not show how much space that player actually *controlled*, or how much space their movement opened up for teammates. Answering that needs two things event data cannot give: the position of all 22 players at every instant, and their velocities.

This project builds that analysis step by step:

1. **Presence**: where does a player spend time? (heatmaps)
2. **Static control**: which areas is each player closest to? (Voronoi diagrams)
3. **Dynamic control**: which areas can each player *reach first*, given their velocity? (kinematic pitch control)
4. **Value**: how much is that space worth, and who creates it with off-the-ball movement? (Generated Space Value)

## Questions this project tries to answer

- How different is a static Voronoi picture from a kinematic pitch control model, and in which game situations does the difference matter most?
- How sensitive are velocity and acceleration estimates to the smoothing method applied to noisy tracking positions?
- Can off-the-ball space creation be measured per player in a way that is stable from match to match?

## Data

| Dataset | Type | Used for |
|---|---|---|
| [Metrica Sports sample data](https://github.com/metrica-sports/sample-data) | Tracking (25 Hz) + synchronised events, 3 anonymised matches | Main dataset: kinematics, Voronoi, pitch control |
| [IDSSE / DFL Bundesliga dataset](https://doi.org/10.1038/s41597-025-04505-y) | Tracking + events, 7 matches (CC-BY 4.0) | Checking that results hold on a second dataset |
| [SkillCorner open data](https://github.com/SkillCorner/opendata) | Broadcast tracking, 10 matches | Optional extension |
| [StatsBomb open data](https://github.com/statsbomb/open-data) | Event data, many competitions | Training the Expected Threat (xT) grid |

Data is not stored in this repository. `src/data.py` downloads it through [kloppy](https://github.com/PySport/kloppy), which reads all of the formats above into a common structure.

## Methods

**Kinematics.** Velocities are obtained by numerical differentiation of the position time series. Raw tracking positions are noisy, so differentiation amplifies the noise; positions are smoothed first (moving average vs. Savitzky-Golay filter are compared) and physically impossible values (speed above ~12 m/s) are discarded. The effect of each choice on the speed and acceleration distributions is reported.

**Presence.** 2D kernel density estimation of each player's positions, split by ball possession.

**Static control.** Voronoi tessellation of the pitch from player positions at a given frame. Each player's region is the set of points they are closest to. Simple and intuitive, but it assumes all players are stationary and equally fast.

**Dynamic control.** Pitch control model following Spearman (2018): for each point on the pitch, compute every player's time to intercept from their current position and velocity, with a reaction time and a maximum speed, and convert arrival times into a probability that each team controls the ball there.

**Value of space.** An Expected Threat (xT) grid, computed as a Markov chain over pitch zones from event data, gives the probability that possession in each zone ends in a goal. Weighting pitch control by xT turns "area controlled" into "valuable area controlled".

**Generated Space Value (GSV).** Proposed metric: for an off-the-ball run, the change in xT-weighted pitch control of the runner's *teammates* between the start and the end of the run. It aims to credit players who open space for others rather than for themselves. Its definition will be refined in Phase 6 and tested in Phase 7.

## Roadmap

### Phase 0: Project setup
- [ ] Remove `venv/` from the repository and add it to `.gitignore`
- [ ] Add `requirements.txt`
- [ ] Create the folder structure below

### Phase 1: Data loading and exploration
- [ ] Load Metrica tracking and event data with kloppy
- [ ] Convert coordinates to metres and normalise attacking direction across halves
- [ ] Plot single frames and short sequences on a pitch
- [ ] Data quality report: missing frames, substitutions, ball out of play

### Phase 2: Kinematics
- [ ] Velocity and acceleration from positions by finite differences
- [ ] Compare smoothing methods and window lengths; remove unphysical values
- [ ] Speed and acceleration distributions per player
- [ ] Per-player physical profile: top speed, distance covered by speed band

### Phase 3: Presence
- [ ] KDE heatmaps per player
- [ ] Heatmaps in possession vs. out of possession

### Phase 4: Static control
- [ ] Voronoi diagram for a single frame, clipped to the pitch boundaries
- [ ] Area controlled per player and per team over time
- [ ] Animation of a full attacking sequence

### Phase 5: Dynamic control
- [ ] Time-to-intercept model with reaction time and maximum speed
- [ ] Pitch control surface for a frame (Spearman model)
- [ ] Voronoi vs. pitch control: quantify where and when they disagree
- [ ] Sensitivity of the surface to the model parameters

### Phase 6: Value of space
- [ ] xT grid from StatsBomb event data
- [ ] xT-weighted pitch control per team over time
- [ ] Detect off-the-ball runs from the tracking data
- [ ] Compute GSV per run and aggregate per player

### Phase 7: Validation and write-up
- [ ] Stability: is a player's GSV consistent across halves and matches?
- [ ] Uncertainty: bootstrap confidence intervals on per-player values
- [ ] Sanity check: does xT-weighted control before a pass relate to pass success?
- [ ] Repeat the key results on the Bundesliga dataset
- [ ] Final report with figures, limitations and conclusions

## Repository structure

```
Football-Kinematics/
├── notebooks/          # one notebook per phase, numbered
├── src/
│   ├── data.py         # loading and coordinate handling
│   ├── kinematics.py   # smoothing, velocities, accelerations
│   ├── voronoi.py      # static control
│   ├── pitch_control.py
│   ├── xt.py           # Expected Threat grid
│   └── viz.py          # pitch plots and animations
├── figures/            # exported figures used in this README
├── requirements.txt
└── README.md
```

## Tech stack

- Python 3
- pandas, NumPy
- SciPy (`spatial` for Voronoi, `signal` for filtering, `stats` for KDE)
- kloppy (data loading)
- matplotlib, mplsoccer (visualisation)

## Getting started

```bash
git clone https://github.com/phinojos/Football-Kinematics.git
cd Football-Kinematics
python -m venv venv
source venv/bin/activate      # Windows: venv\Scripts\activate
pip install -r requirements.txt
```

## Results

Figures will be added here as each phase is completed.

## Limitations

- The open tracking datasets are small (a handful of matches), so per-player conclusions are illustrative, not definitive.
- Metrica's sample matches are anonymised: players and teams cannot be identified.
- Pitch control parameters (reaction time, maximum speed) are taken as equal for all players in the first version.

## References

- Spearman, W. (2018). *Beyond Expected Goals*. MIT Sloan Sports Analytics Conference.
- Fernández, J. & Bornn, L. (2018). *Wide Open Spaces: A statistical technique for measuring space creation in professional soccer*. MIT Sloan Sports Analytics Conference.
- Singh, K. (2019). *Introducing Expected Threat (xT)*.
- Shaw, L. [LaurieOnTracking](https://github.com/Friends-of-Tracking-Data-FoTD/LaurieOnTracking): tutorials on tracking data and pitch control with the Metrica dataset.
- Bassek, M., Rein, R., Weber, H. & Memmert, D. (2025). An integrated dataset of spatiotemporal and event data in elite soccer. *Scientific Data*, 12, 195.

## Acknowledgements

Thanks to Metrica Sports, the DFL, SkillCorner and StatsBomb for making their data openly available.

