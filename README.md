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

<img width="1038" height="391" alt="image" src="https://github.com/user-attachments/assets/6da6de23-ea6f-41d2-903b-3df9258bed07" />

First, the line equation was rewritten in terms of the root at x (`Cx`) and y axle (`Cy`), in this way, it is reasonable to conclude that the active area will be when the left part is less than one, taking into account the retangule limit (`xsup`/`ysup`).

$$\frac{x}{C_x} + \frac{y}{C_y} = 1$$

To identify if a grid node lies within any of the four corner chamfers, the algorithm applies a coordinate mapping strategy based on boundary limits. First, a fast bounding box check filters out points far from the corners to save computational time. For nodes located near a corner, the coordinates are adjusted using the chip's total dimensions—subtracting from the total length or width—to effectively map all regions to a local origin. This allows the algorithm to evaluate all four corners using a single, generalized segmentary line equation that respects the physical limits of each specific corner.

$$
\begin{aligned}
\text{1. Upper-Right Corner:} \quad & x > x_{sup} - C_x \quad \text{and} \quad y > y_{sup} - C_y \quad \implies \quad \frac{x_{sup} - x}{C_x} + \frac{y_{sup} - y}{C_y} < 1 \\
\text{2. Upper-Left Corner:} \quad & x < C_x \quad \text{and} \quad y > y_{sup} - C_y \quad \implies \quad \frac{x}{C_x} + \frac{y_{sup} - y}{C_y} < 1 \\
\text{3. Lower-Left Corner:} \quad & x < C_x \quad \text{and} \quad y < C_y \quad \implies \quad \frac{x}{C_x} + \frac{y}{C_y} < 1 \\
\text{4. Lower-Right Corner:} \quad & x > x_{sup} - C_x \quad \text{and} \quad y < C_y \quad \implies \quad \frac{x_{sup} - x}{C_x} + \frac{y}{C_y} < 1
\end{aligned}
$$

<img width="1024" height="660" alt="image" src="https://github.com/user-attachments/assets/7630f2c4-a7a0-4e0b-be19-e0f3ead7f4d9" />

The second function was defined with the goal of returning the specific ID related to the respective analyzed positions, otherwise, it will return 0 for conditions purposes further.

<img width="955" height="195" alt="image" src="https://github.com/user-attachments/assets/781d2b7d-e177-4f15-94cc-345ff9572921" />

The functions `frac_to_chamfer_x` and `frac_to_chamfer_y` calculates how much of a mesh cell remains free before hitting the chamfer wall. First, it isolates the variable in the line equation to find the exact intersection point ($x_{int}$ or $y_{int}$) where the cell's current track meets the diagonal boundary. Next, using the direction flag (dir), it subtracts the coordinates ($\text{Intersection} - \text{Node}$ or vice-versa) to get a positive physical distance (dist) representing the remaining space inside the cell. Finally, if this distance is smaller than the cell's total size (dx or dy), the code divides the distance by the cell size (dist / dx or dist / dy). This converts the physical measurement into a normalized fraction between 0.0 and 1.0, telling the simulation the exact percentage of the cell volume that is outside the blocked void area.

<img width="1027" height="395" alt="image" src="https://github.com/user-attachments/assets/95bfbe2d-7665-4df8-b026-674b9a917282" />
<img width="952" height="143" alt="image" src="https://github.com/user-attachments/assets/6c2b280d-7893-4300-b0e1-f9f26a125270" />

The `Q_function` uses a Bounded Exponential Growth as a base of calculus, which means there is time to Q target the standard value analyzed . In this code, `Q_function` has a significant role because it computes the heat generation from the chips inside the microcontroller.

<img width="553" height="52" alt="image" src="https://github.com/user-attachments/assets/54f6196b-a8ed-4479-856a-8b460cf7a350" />

### Main Execution
In the first rows of `int main` is responsible for some configurations such as Portuguese language setup and accent permission use, besides defining a dynamic path for opening and reading files. In this case, the file is in txt format, where there are binary numbers distributed according to the geometry of the original file that create the txt. The leading function of this input file was to track how two types of materials (Epoxy Molding Compound and Copper) are allocated in the microcontroller geometry.
In addition, for the purposes of compatibility, this code takes the number of rows and columns from the txt file by using the binary within the file as a base for filling the matrix from the Classical Linear Equation System that emerged from FDM.

<img width="554" height="274" alt="image" src="https://github.com/user-attachments/assets/e5c32e2e-255e-4c5f-bfd8-6d7b47e04e72" />

However, it was defined those variables presented below, which are essential to compute each thermal and geometrical parameter: 
### Variables Glossary

*   **xsup, ysup:** Geometric boundaries/limits of the board (NodeMCU ESP8266).
*   **Nx, Ny:** Number of grid nodes along the horizontal $x$ and $y$-axis..
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

This code segment loops through the entire grid mesh to map the board's geometry. For each node, it calculates the physical coordinates (`x`, `y`) and its flattened vector index (`idx`). It then calls the `corner_id_for_outside` function to check if the current node falls inside any of the four chamfered corners; if it does, the node is flagged as inactive (`active[idx] = 0`) to correctly define the real shape of the NodeMCU board.

<img width="902" height="231" alt="image" src="https://github.com/user-attachments/assets/9a13c278-3cc9-4cec-9b3d-5461271b0585" />

To model the geometry, the Euclidean distance from each grid node to the upper-left and upper-right circular holes is evaluated based on a defined offset from the board edges (`offset_furo`). Nodes falling within the specified hole radius are deactivated in the mesh array, and their binary grid state is set to `1`. Vertical symmetry is exploited to simultaneously map the corresponding lower holes by reflecting the vertical grid index, ensuring all four corner perforations are generated without redundant calculations. Additionally, a filtering condition clears residual copper in the top margin by overriding any trace regions to substrate status (`1`). This removes noise artifacts and unwanted copper remnants from the layout binarization process, preventing artificial thermal conduction paths and preserving the true physical behavior of the board edges.

<img width="1003" height="308" alt="image" src="https://github.com/user-attachments/assets/0e7d3bfb-ea08-40a8-82ad-88d9b8c8f1ac" />

<img width="1024" height="380" alt="image" src="https://github.com/user-attachments/assets/f6467712-d2b2-4ffd-ab4d-36aceedbbf58" />


The spatial distribution of internal heat generation is implemented by mapping three distinct rectangular domains corresponding to the primary, secondary (CH340), and tertiary (AMS117) heat sources. For each node $(x, y)$ in the computational grid, a conditional check evaluates whether its coordinates lie within the physical boundaries of each component, calculated from their center coordinates and dimensions $(W \times H)$. If a node falls within a source's domain, the corresponding heat generation value (heat_source_val) is assigned to the source array $Q[\text{idx}]$.

<img width="1021" height="319" alt="image" src="https://github.com/user-attachments/assets/b0fbaad0-6693-40aa-ad69-ab86435e9000" />

In a standard uniform Cartesian grid, the spatial steps between adjacent nodes along the $x$ and $y$ axes are constant, denoted by $\Delta x$ and $\Delta y$, respectively. However, when modeling complex geometries—such as printed circuit boards featuring chamfered corners or curved cutouts—nodes near the boundary may have geometric neighbors that fall outside the active physical domain (`active == 1`). In these cases, the physical boundary intersects the grid segment at a fraction of the standard step size.
To account for this boundary asymmetry without unstructuring the entire mesh, non-uniform grid scaling factors ($a_1, a_2, b_1, b_2 \le 1.0$) are introduced. For any active node $(i, j)$ at position $(x, y)$, these factors measure the fractional distances to the boundary line along each direction: $a_1 \Delta x$ to the left, $a_2 \Delta x$ to the right, $b_1 \Delta y$ downward, and $b_2 \Delta y$ upward. For interior nodes surrounded entirely by active neighbors, these factors default to $1.0$.
Applying a Taylor series expansion for asymmetric spatial steps ($h_1 = a_1 \Delta x$ and $h_2 = a_2 \Delta x$), the second-order partial derivative with respect to $x$ is discretized as follows:

$$\frac{T_{i,j}^{p+1} - T_{i,j}^p}{\Delta t} = \alpha \left[ \frac{2}{\Delta x^2} \left( \frac{T_{i-1,j}^{p+1}}{a_1(a_1+a_2)} - \frac{T_{i,j}^{p+1}}{a_1 a_2} + \frac{T_{i+1,j}^{p+1}}{a_2(a_1+a_2)} \right) + \frac{2}{\Delta y^2} \left( \frac{T_{i,j-1}^{p+1}}{b_1(b_1+b_2)} - \frac{T_{i,j}^{p+1}}{b_1 b_2} + \frac{T_{i,j+1}^{p+1}}{b_2(b_1+b_2)} \right) \right] + \frac{Q_{i,j}(t)}{\rho c_p}$$

Because the domain geometry remains static over time, these geometric factors depend strictly on spatial coordinates and are precomputed once prior to the transient time-stepping loop.
The grid mapping uses column-major indexing (`idx = i * Ny + j`), where adjacent nodes along the $y$-axis are stored continuously in memory, while nodes along the $x$-axis are separated by a stride of length `Ny`. As a result, moving to the left neighbor ($i-1$) corresponds to shifting back by one full column (`idx - Ny`), while moving to the right neighbor ($i+1$) shifts forward by one column (`idx + Ny`). Conversely, moving to the bottom neighbor ($j-1$) and top neighbor ($j+1$) corresponds to step shifts of `-1` and `+1`, respectively. When any of these adjacent neighbor checks evaluate to inactive (`active == 0`), it signals that the physical boundary cuts through that grid segment, triggering the calculation of the corresponding fractional step factor ($a_1, a_2, b_1,$ or $b_2$).

<img width="1051" height="333" alt="image" src="https://github.com/user-attachments/assets/eaed8d5e-fd24-4ae0-a0c0-a400bd6ea215" />

Left neighbor ($i - 1$):

$$
\text{idx}_{\text{left}} = (i - 1) \cdot Ny + j = i \cdot Ny - Ny + j = (i \cdot Ny + j) - Ny = \mathbf{\text{idx} - Ny}
$$

Right neighbor ($i + 1$):

$$
\text{idx}_{\text{right}} = (i + 1) \cdot Ny + j = i \cdot Ny + Ny + j = (i \cdot Ny + j) + Ny = \mathbf{\text{idx} + Ny}
$$

Downward neighbor ($j - 1$):

$$
\text{idx}_{\text{down}} = i \cdot Ny + (j - 1) = (i \cdot Ny + j) - 1 = \mathbf{\text{idx} - 1}
$$

Upward neighbor ($j + 1$):

$$
\text{idx}_{\text{up}} = i \cdot Ny + (j + 1) = (i \cdot Ny + j) + 1 = \mathbf{\text{idx} + 1}
$$

The numerical formulation defines key parameters such as the convergence tolerance (`tol = 1e-5`), the successive over-relaxation factor (`omega = 1.1`), the total number of time steps (`N_passos = 150`), and the iteration limit (`iter_max = 50`). 
Within the main time-marching loop, the current temperature matrix `T` is saved to `T_old` at each step, while the error tracker and iteration counters are reset. The iterative solver then runs via a `while` loop, sweeping through the internal nodes of the mesh (excluding outer boundaries) using nested loops. For each grid point, the physical coordinates `x` and `y` are computed using the spatial increments (`dx` and `dy`), and a linear index `idx` is mapped to efficiently access and update the vector data across the simulation domain.

<img width="946" height="367" alt="image" src="https://github.com/user-attachments/assets/52e22921-432f-43f9-8b87-ff9f09577b34" />

For each active node (`active[idx] == 1`), the algorithm dynamically assigns the thermal diffusivity (`alpha`) based on its spatial location. It first checks if the coordinates fall within the primary ESP8266 heat source region to assign `alpha_esp`, otherwise defaulting to copper (`alpha_cobre`) or FR-4 substrate (`alpha_fr4`) depending on the binary matrix map. Next, the precomputed irregular mesh spacing correction coefficients (`A1`, `A2`, `B1`, `B2`) are retrieved for that node. Finally, conditional boundaries verify if the node lies within the secondary CH340 or AMS117 component regions, overriding the diffusivity value with a specific constant (`3e-7`).

<img width="1019" height="336" alt="image" src="https://github.com/user-attachments/assets/724f46fe-4bcb-442f-b27b-5a8ee58686a3" />

The temporary updated temperature (`T_GS`) for each node is then evaluated using the following discrete governing equation:

$$
T_{\text{GS}} = \frac{\left(\frac{T_{\text{old}}[\text{idx}]}{\Delta t} + 2\alpha \left( \frac{\text{CT}_2}{(\Delta x)^2} + \frac{\text{CT}_3}{(\Delta y)^2} \right) + \frac{Q(\text{idx}, \Delta t \cdot \text{passo})}{1.63 \times 10^6}\right)}{\text{CT}_4}
$$

In this formulation, the first term inside the numerator accounts for the transient storage effect from the previous time step, the second term incorporates spatial heat conduction weighted by neighboring nodes and thermal diffusivity ($\alpha$), and the third term introduces the scaled internal heat generation. Finally, successive over-relaxation (using factor $\omega$) is applied to update the node temperature `T[idx]`, and the maximum absolute change is tracked in `erro` to check for convergence.

At every 10th time step, a monitoring checkpoint triggers to report the simulation progress directly to the console (`cout`). Simultaneously, it handles the export routine by dynamically generating a CSV data file named sequentially (e.g., `laplace_transiente_passo_[passo].csv`). The code checks if the file stream opened successfully before writing a standardized header (`X,Y,T,Ativo,Material,Fonte`). It then nests loops across the entire grid (`Nx` by `Ny`) to compute physical coordinates, retrieving and printing the node temperature, activity status, material matrix type, and evaluated heat source value for every grid point, safely closing the file handle afterward.
The scaling factor $1.63 \times 10^6 \text{ J/(m³}\cdot\text{K)}$ represents the volumetric heat capacity ($\rho C_p$) of the epoxy molding compound used to encapsulate the semiconductor chip, combining a typical density of roughly $1,900 \text{ kg/m³}$ and a specific heat capacity of about $850 \text{ J/(kg}\cdot\text{K)}$.

<img width="1034" height="380" alt="image" src="https://github.com/user-attachments/assets/b1601aa8-45a5-45dc-a559-3baae02b567b" />

### 🌡️ Part 2: Parameter optimization & heatsink selection models
This second part aimed to find the best adjusted parameters to find an ideal heatsink in terms of geometrical parameters according to the respective constraints. In this form, the objective was to compare two numerical methods. This case was between the nonlinear conjugate gradient and PINNs (Physics-Informed Neural Networks).

### ⚙️ PINNs (Phisics Informed Neural Networks)

First, the base neural network model used for this case was a **Multi-Layer Perceptron (MLP)**.

A **Multi-Layer Perceptron (MLP)** is a foundational class of feedforward artificial neural networks (ANN) and forms the backbone of modern deep learning architectures. Unlike single-layer Perceptrons or linear classifiers, MLPs consist of an input layer, one or more intermediate **hidden layers**, and an output layer. By interleaving linear matrix operations with non-linear activation functions, an MLP acts as a **Universal Function Approximator** (Cybenko, 1989; Hornik, 1991), capable of modeling arbitrarily complex non-linear decision boundaries and high-dimensional feature spaces.

---

### 1. Architecture & Layer Mechanics

An MLP is typically structured as a **Fully Connected (Dense)** network where every node in layer $l$ connects to every node in layer $l+1$ via parameterized weights.

<img width="670" height="458" alt="image" src="https://github.com/user-attachments/assets/8569a8de-353b-4ea4-a30f-d92daf58cade" />


### 1.1 Layer Functional Breakdown

In a Multi-Layer Perceptron, processing flows sequentially across three functional layers, with each stage altering the dimensional representation and geometric abstraction of the data. The Input Layer ($l=0$) acts as a passive entry point for the raw feature vector $\mathbf{X} \in \mathbb{R}^{d_{in}}$, applying no mathematical transformations and adding zero trainable parameters.
The Hidden Layers ($l=1 \dots L-1$) perform the core feature learning. By combining parameterized weights $\mathbf{W}^{(l)}$, biases $\mathbf{b}^{(l)}$, and non-linear activations, they map input features into higher-dimensional latent spaces to resolve complex, non-linear patterns that are non-separable in the original domain. This layer group contributes $\sum_{l=1}^{L-1} (n_{l-1} \cdot n_l + n_l)$ parameters. The Output Layer ($l=L$) projects the resulting latent representations onto task-specific decision boundaries, outputting continuous scalars for regression, binary logits, or multi-class probabilities via activations like Softmax, adding a final $n_{L-1} \cdot n_L + n_L$ parameters.

| Layer Type | Mathematical Role | Information State | Parameter Count |
| :--- | :--- | :--- | :--- |
| **Input Layer ($l=0$)** | Serves as an entry point for raw features $X \in \mathbb{R}^{d_{in}}$. Applies no trainable parameters or transformations. | Unprocessed / Normalized Features | $0$ |
| **Hidden Layers ($l=1 \dots L-1$)** | Maps input features into higher-dimensional latent spaces to resolve complex, non-linear patterns that are non-separable in the original domain. | Abstract Latent Representations | $\sum_{l=1}^{L-1} (n_{l-1} \cdot n_l + n_l)$ |
| **Output Layer ($l=L$)** | Maps latent features to task-specific decision boundaries (continuous scalars, binary logits, or multi-class probabilities). | Predicted Values ($\hat{Y}$) | $n_{L-1} \cdot n_L + n_L$ |

---

# 2. Rigorous Mathematical Formulation of the Artificial Neuron

Each artificial neuron inside a hidden or output layer performs two sequential operations: **Affine Transformation** followed by **Non-Linear Mapping**.

$$x_i \longrightarrow \Big[\text{Weighted Sum: } z = \mathbf{W}^T \mathbf{X} + b\Big] \longrightarrow \Big[\text{Activation: } a = f(z)\Big] \longrightarrow a$$

## Choice of Activation Function: Hyperbolic Tangent ($\tanh$)

In the code, the hidden layers use the `activation='tanh'`. This specific choice was made due to two fundamental factors of both the physical problem and the computational framework (PINN - *Physics-Informed Neural Network*):

1. **Alignment with the Analytical Solution for Rectangular Fins**

The differential equation governing the temperature distribution in one-dimensional rectangular fins has an analytical solution expressed in terms of hyperbolic functions:
   
   $$\theta(y) = \frac{\cosh[m(1 - y)] + \left(\frac{h}{m k}\right)\sinh[m(1 - y)]}{\cosh(mL) + \left(\frac{h}{m k}\right)\sinh(mL)}$$

Since the function $\tanh(x) = \frac{\sinh(x)}{\cosh(x)}$ shares the same exponential basis ($e^x$ and $e^{-x}$), the network learns to represent the thermal field much more smoothly and rapidly compared to using piecewise linear functions (such as ReLU).

3. **Smooth Differentiability and Second-Order Derivatives ($C^\infty$)**
To calculate the partial differential equation (PDE) loss, the code employs automatic differentiation (`tf.GradientTape`) to obtain the second derivative of temperature with respect to space: $$\frac{d^2T}{dy^2}$$

The $\tanh$ function is infinitely differentiable ($C^\infty$), and its derivative has a simple analytical form ($\frac{d}{dx}\tanh(x) = 1 - \tanh^2(x)$). Functions like ReLU have a second derivative that is zero almost everywhere in the domain, which would make it impossible to calculate the PDE residual.
The loss associated with the governing physics enforces energy conservation along the fin. It measures how far the neural network's predictions deviate from the 1D heat conduction-convection differential equation:

$$f_{res} = \frac{\frac{d^2T}{dy^2}}{\Theta_B} - m^2 \cdot \theta$$

* **$\frac{1}{\Theta_B} \frac{d^2T}{dy^2}$:** The spatial second derivative of temperature computed via automatic differentiation, representing net conductive heat transfer along the fin axis.
* **$m^2 \theta$:** The convective heat dissipation term, where $m^2 = \frac{h P}{k A_c}$ balances convective heat transfer at the surface against conduction within the cross-section.

The network minimizes PDE so predictions rigorously adhere to energy conservation without requiring labeled experimental data.
5. **Scale Invariance and Appropriate Amplitude**
   The output of $\tanh$ varies within the interval $[-1, 1]$. This assists in keeping the gradients well-conditioned across the hidden layers and stabilizes the training process when using double precision (`float64`).


