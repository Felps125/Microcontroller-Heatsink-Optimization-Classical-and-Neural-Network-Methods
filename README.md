# Microcontroller-Heatsink-Optimization-Classical-and-Neural-Network-Methods

<p align="center">
  <img src="https://img.shields.io/badge/Languages-Python%20%7C%20C%2B%2B%20%7C%20PowerShell-blue.svg" alt="Languages Badge">
  <img src="https://img.shields.io/badge/Status-Finalizing-yellow.svg" alt="Status Badge">
  <img src="https://img.shields.io/badge/Numerical--Methods-FDM%20%7C%20Conjugate%20Gradient%20%7C%20PINNs-orange.svg" alt="Numerical Methods Badge">
</p>

## 📝 Short Resume

> This repository documents all codes developed for this project. Part 1 determines the temperature field of the studied object, proving the need for a heatsink. Part 2 optimizes different parameters to find the best heatsink option while considering the project's inherent constraints and both those codes were developed based on Numerical Methods

---

## 📂 Project Structure

*   **`Part 1/`** — Temperature field determination & thermal analysis.
*   **`Part 2/`** — Parameter optimization & heatsink selection models.

### 🌡️ Part 1: Temperature Field Determination

The main code developed in this part is `Laplace_Transient`, which is based on the Finite Difference Method (FDM) implemented in C++. Thus, the first step was defining the essential libraries for this development, which are presented below:

<img width="1125" height="192" alt="image" src="https://github.com/user-attachments/assets/0d63eb1f-8183-4b6a-9c1d-c2683d56a29a" />

In a general way, some libraries were necessary to realize certain calculus (`<cmath>`), besides inputs and manipulating vectors (`<vector>`, `<algorithm>`), handling files, paths, strings (`<string>`, `<sstream>`), etc. Besides that, some dumps were used to optimize the readability such as `fs` in `namespace fs = std::filesystem;`.

## ⚙️ Main Functions

The first function, called `is_within_chamfer()`, had the goal of tracking the chamfer area of the microcontroller, dividing it into the inactive/active area. This is based on the Line equation in which each position in bidimensional space, according to the microcontroller dimension and an id that was used to evaluate 4 cases by using the switch function.

<img width="1009" height="444" alt="image" src="https://github.com/user-attachments/assets/bd7415c5-8228-403f-af0f-11bc7dd15495" />

First, the line equation was rewritten in terms of the root at x (`Cx`) and y axle (`Cy`), in this way, it is reasonable to conclude that the active area will be when the left part is less than one, taking into account the retangule limit (`xsup`/`ysup`).

$$\frac{x}{C_x} + \frac{y}{C_y} = 1$$

To identify if a grid node lies within any of the four corner chamfers, the algorithm applies a coordinate mapping strategy based on boundary limits. First, a fast bounding box check filters out points far from the corners to save computational time. For nodes located near a corner, the coordinates are adjusted using the chip's total dimensions—subtracting from the total length or width—to effectively map all regions to a local origin. This allows the algorithm to evaluate all four corners using a single, generalized segmentary line equation that respects the physical limits of each specific corner.

$$
\begin{aligned}
\text{1. Upper-Left Corner:} \quad & x < C_x \quad \text{and} \quad y > y_{sup} - C_y \quad \implies \quad \frac{x}{C_x} + \frac{y_{sup} - y}{C_y} < 1 \\
\text{2. Upper-Right Corner:} \quad & x > x_{sup} - C_x \quad \text{and} \quad y > y_{sup} - C_y \quad \implies \quad \frac{x_{sup} - x}{C_x} + \frac{y_{sup} - y}{C_y} < 1 \\
\text{3. Lower-Left Corner:} \quad & x < C_x \quad \text{and} \quad y < C_y \quad \implies \quad \frac{x}{C_x} + \frac{y}{C_y} < 1 \\
\text{4. Lower-Right Corner:} \quad & x > x_{sup} - C_x \quad \text{and} \quad y < C_y \quad \implies \quad \frac{x_{sup} - x}{C_x} + \frac{y}{C_y} < 1
\end{aligned}
$$

<img width="1024" height="660" alt="image" src="https://github.com/user-attachments/assets/675c9c56-f0a2-4ce1-9e66-86d9d5eb03cb" />

The second function was defined with the goal of returning the specific ID related to the respective analyzed positions, otherwise, it will return 0 for conditions purposes further.

<img width="955" height="195" alt="image" src="https://github.com/user-attachments/assets/781d2b7d-e177-4f15-94cc-345ff9572921" />

The functions `frac_to_chamfer_x` and `frac_to_chamfer_y` calculates how much of a mesh cell remains free before hitting the chamfer wall. First, it isolates the variable in the line equation to find the exact intersection point ($x_{int}$ or $y_{int}$) where the cell's current track meets the diagonal boundary. Next, using the direction flag (dir), it subtracts the coordinates ($\text{Intersection} - \text{Node}$ or vice-versa) to get a positive physical distance (dist) representing the remaining space inside the cell. Finally, if this distance is smaller than the cell's total size (dx or dy), the code divides the distance by the cell size (dist / dx or dist / dy). This converts the physical measurement into a normalized fraction between 0.0 and 1.0, telling the simulation the exact percentage of the cell volume that is outside the blocked void area.

<img width="1035" height="24" alt="image" src="https://github.com/user-attachments/assets/1c8f0d81-f72a-4c1b-890f-2c5ba17aa917" />

The `Q_function` uses a Bounded Exponential Growth as a base of calculus, which means there is time to Q target the standard value analyzed . In this code, `Q_function` has a significant role because it computes the heat generation from the chips inside the microcontroller.

<img width="553" height="52" alt="image" src="https://github.com/user-attachments/assets/54f6196b-a8ed-4479-856a-8b460cf7a350" />

### Main Execution
In the first rows of `int main` is responsible for some configurations such as Portuguese language setup and accent permission use, besides defining a dynamic path for opening and reading files. In this case, the file is in txt format, where there are binary numbers distributed according to the geometry of the original file that create the txt. The leading function of this input file was to track how two types of materials (Epoxy Molding Compound and Copper) are allocated in the microcontroller geometry.
In addition, for the purposes of compatibility, this code takes the number of rows and columns from the txt file by using the binary within the file as a base for filling the matrix from the Classical Linear Equation System that emerged from FDM.

<img width="554" height="274" alt="image" src="https://github.com/user-attachments/assets/e5c32e2e-255e-4c5f-bfd8-6d7b47e04e72" />

However, it was defined those variables presented below, which are essential to compute each thermal and geometrical parameter: 
### Variables Glossary

*   **xsup, ysup:** Geometric boundaries/limits of the board (NodeMCU ESP8266).
*   **dx, dy:** Grid spacing along the x and y axes.
*   **Cx, Cy:** Width and length of the chamfered corner ("triangle").
*   **alpha_cobre:** Thermal diffusivity of copper.
*   **alpha_fr4:** Thermal diffusivity of the FR-4 resin material.
*   **alpha_esp:** Thermal diffusivity of the ESP8266 chip region.
*   **deltat:** Time step size for the transient regime.
*   **heat_source_val:** Internal heat generation rate ($Q$) for the ESP8266 chip.
*   **fonte_w, fonte_h:** Width and height of the ESP8266 heat source.
*   **fonte_x_centro, fonte_y_centro:** Center coordinates of the ESP8266 heat source.
*   **heat_source_val_ch340:** Internal heat generation rate ($Q$) for the CH340G chip.
*   **fonte_w_ch340, fonte_h_ch340:** Width and height of the CH340G heat source.
*   **fonte_x_centro_ch340, fonte_y_centro_ch340:** Center coordinates of the CH340G heat source.
*   **heat_source_val_ams117:** Internal heat generation rate ($Q$) for the AMS1117 regulator.
*   **fonte_w_ams117, fonte_h_ams117:** Width and height of the AMS1117 heat source.
*   **fonte_x_centro_ams117, fonte_y_centro_ams117:** Center coordinates of the AMS1117 heat source.
*   **T:** Vector storing the current Temperature Field for each grid node.
*   **T_old:** Vector storing the Temperature Field from the previous time step.
*   **active:** Mapping vector for active and inactive zones used to adjust the board's chamfered geometry.
*   **a1, a2, b1, b2:** Adjustment parameters for the respective chamfered corners used in the Laplace equation discretization.
*   **Q:** Vector storing the heat generation distribution across the grid.
*   **raio_furo:** Radius of the physical mounting holes.
*   **offset_furo:** Edge offset distance for the mounting holes.
*   **limite_inferior_y:** Lower boundary threshold used for positioning the mounting holes.
