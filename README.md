# Microcontroller-Heatsink-Optimization-Classical-and-Neural-Network-Methods

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.x-blue.svg" alt="Python Badge">
  <img src="https://img.shields.io/badge/Status-Complete-green.svg" alt="Status Badge">
  <img src="https://img.shields.io/badge/Numerical--Methods-Applied-orange.svg" alt="Numerical Methods Badge">
</p>

## 📝 Short Resume

> This repository documents all codes developed for this project. Part 1 determines the temperature field of the studied object, proving the need for a heatsink. Part 2 optimizes different parameters to find the best heatsink option while considering the project's inherent constraints and both those codes were developed based on Numerical Methods

---

## 📂 Project Structure

*   **`Part 1/`** — Temperature field determination & thermal analysis.
*   **`Part 2/`** — Parameter optimization & heatsink selection models.

### 🌡️ Part 1: Temperature Field Determination

The main code developed in this part is "Laplace_Transient", which is based on the Finite Difference Method (FDM) implemented in C++. Thus, the first step was defining the essential libraries for this development, which are presented below:

<img width="1125" height="192" alt="image" src="https://github.com/user-attachments/assets/0d63eb1f-8183-4b6a-9c1d-c2683d56a29a" />

In a general way, some libraries were necessary to realize certain calculus (`<cmath>`), besides inputs and manipulating vectors (`<vector>`, `<algorithm>`), handling files, paths, strings (`<string>`, `<sstream>`) and etc.
