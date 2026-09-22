# ⚽ Pitch Control & Spatial Analysis in Football

> 🚧 **Work in Progress:** This project is currently under active development. Code, visualizations, and documentation are being updated regularly.

## 📌 About The Project

This repository focuses on evaluating football player effectiveness and spatial dominance through computational geometry and kinematic analysis. By moving beyond traditional event data (like passes and shots), this project aims to model the physical space controlled by teams and individual players during key phases of a match.

**Core objectives of this project:**
- Differentiate between a player's physical presence (heatmaps) and their actual pitch control.
- Apply computational geometry (Voronoi diagrams) to evaluate spatial dominance.
- (Planned) Introduce kinematic factors (velocity vectors and acceleration) to transition from static geometry to dynamic space evaluation.

## 🛠️ Built With

*   **Python 3**
*   **Data Manipulation:** `pandas`, `numpy`
*   **Mathematical/Spatial Algorithms:** `scipy.spatial` (Voronoi)
*   **Data Visualization:** `matplotlib`, `seaborn`, `mplsoccer`
*   **Data Source:** Open event data (e.g., StatsBomb)

## 📊 Roadmap & Features

- [ ] **Phase 1:** Data extraction, cleaning, and basic exploratory data analysis (EDA).
- [ ] **Phase 2:** 2D Kernel Density Estimation (KDE) for player heatmaps.
- [ ] **Phase 3:** Static Voronoi tessellations on specific match freeze-frames.
- [ ] **Phase 4:** Custom metric creation (e.g., *Generated Space Value*) to evaluate off-the-ball movement and scouting potential.

## 👁️ Visual Previews

*(Visualizations and GIFs demonstrating the pitch control models will be added here as the project evolves).*

---
*Created as a personal project combining data science, physics, and football analytics.*