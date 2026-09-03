# Awesome Terrain-Aware Navigation

<div align="center">
  <p>
    <strong>A curated list of papers, tools, and datasets for traversability estimation and navigation on uneven, rough terrain.</strong>
  </p>
</div>

Inspired by and building upon [Ikhyeon-Cho/awesome-traversability-analysis](https://github.com/Ikhyeon-Cho/awesome-traversability-analysis), this repository focuses on recent advancements (2024-2026), bridging the gap between classical geometry-based methods and modern learning-based approaches, including continuous traction-based costs, self-supervised methods, and foundation-model-enabled zero-shot generalization.

---

## Table of Contents

- [Classical and Geometrical Methods](#classical-and-geometrical-methods)
  - [Elevation Mapping and Grid-Based Representations](#elevation-mapping-and-grid-based-representations)
  - [Direction-Aware and SE(2) Traversability](#direction-aware-and-se2-traversability)
  - [Statistical and Probabilistic Planning](#statistical-and-probabilistic-planning)
  - [Stability and Safety-Constrained Methods](#stability-and-safety-constrained-methods)
  - [Hierarchical and Multi-Level Planning](#hierarchical-and-multi-level-planning)
- [Learning-Based Approaches](#learning-based-approaches)
  - [Self-Supervised Visual Methods](#self-supervised-visual-methods)
  - [Evidential and Traction-Based Learning](#evidential-and-traction-based-learning)
  - [Implicit Neural Representations](#implicit-neural-representations)
  - [Hybrid and Mixture-of-Experts](#hybrid-and-mixture-of-experts)
  - [Proprioceptive and Physics-Informed Methods](#proprioceptive-and-physics-informed-methods)
- [Other Selected Works](#other-selected-works)
- [Code, Datasets, and ROS 2 Integration](#code-datasets-and-ros-2-integration)
  - [Open-Source Implementations](#open-source-implementations)
  - [Datasets](#datasets)

---

## Classical and Geometrical Methods

Classical traversability estimation derives terrain properties directly from geometric sensor data (LiDAR point clouds, depth cameras) without learned components.

### Elevation Mapping and Grid-Based Representations

| Paper / Authors | Summary | Code / OS | Planner / Details |
| :--- | :--- | :--- | :--- |
| **Fankhauser et al.**<br>(ETH Zurich / ANYbotics) | Seminal work. Fuses LiDAR with robot pose to maintain a probabilistic 2.5D height map. | Yes, ROS 1 | Only grid mapping, no navigation. |
| **Wermelinger et al.** | Combines traversability map with robot-specific footprint costs. | Yes, ROS 1 | Hierarchical RRT* over gridmap. |

### Direction-Aware and SE(2) Traversability

| Paper / Authors | Summary | Code / OS | Planner / Details |
| :--- | :--- | :--- | :--- |
| **SEB-Naver**<br>(Li et al., ZJU FAST-Lab, IROS 2025) | Planning in SE(2) with MPC and Kalman filter for mapping. Treats traversability not as scalar, but orientation-dependent. | Yes, ROS 1 | **Input:** LiDAR Point Clouds<br>**Output:** SE(2) Map<br>**Planner:** MPC (with complex high-level trajectory generator). |

### Statistical and Probabilistic Planning

| Paper / Authors | Summary | Code / OS | Planner / Details |
| :--- | :--- | :--- | :--- |
| **SEM-RRT\***<br>(Dong et al., IROS 2025) | Replaces deterministic elevation map with a statistical one maintaining per-cell uncertainty bounds. | No Codebase | - |
| **Jardali et al.**<br>(ICRA 2024) | Uses Gaussian process to find safe locations (least covariance), setting them as subgoals. Mapless navigation. | Yes, ROS 1 | Navigates to subgoal with trivial PID. |

### Stability and Safety-Constrained Methods

| Paper / Authors | Summary | Code / OS | Planner / Details |
| :--- | :--- | :--- | :--- |
| **CAP**<br>(Zhang et al., IROS 2025) | Focuses on preventing tip-over via analytical force-closure/ZMP and stability pyramids. No grid sampling required. | - | **Input:** 2.5D Elevation Map (LiDAR)<br>**Output:** Essential safe trajectory<br>**Planner:** Graph-based optimization (End-to-end). |
| **T-CBF**<br>(Gupta and Xiao, IROS 2025) | Learning-based tip-over prevention. CNN takes grid heights, outputs scalar; kinodynamic model checks tip-over. Encodes traversability as Control Barrier Function in MPC. | - | **Input:** 2.5D Elevation Map (Depth)<br>**Output:** Essential safe trajectory<br>**Planner:** MPC (End-to-end). |

### Hierarchical and Multi-Level Planning

| Paper / Authors | Summary | Code / OS | Planner / Details |
| :--- | :--- | :--- | :--- |
| **Li et al.**<br>(ICRA 2026) | Multi-level terrain-aware planning with implicit map representation. | - | - |
| **TRG-Planner**<br>(Lee et al., KAIST, RA-L 2025) | Constructs traversal risk graph (nodes = stable areas). Similar to SEB-Naver but for legged robots using wavefront propagation. | - | **Input:** 2.5D Elevation Map (LiDAR)<br>**Output:** Essentially safe trajectory<br>**Planner:** A*-based (End-to-end). |

---

## Learning-Based Approaches

Recent trends shift toward self-supervision, traction-based evidential learning, and foundation-model generalizations to capture surface properties and deformability.

### Self-Supervised Visual Methods

| Paper / Authors | Summary | Code / OS | Planner / Details |
| :--- | :--- | :--- | :--- |
| **Wild Visual Navigation (WVN)**<br>(Mattamala et al., 2025) | Passes superpixels to DINOv2 for segmentation, followed by small MLP for camera-perspective traversability score. Online training via robot velocity. | Yes, ROS 1 | **Input:** RGB Images<br>**Output:** Traversability Score<br>**Planner:** SDF-based custom planner. |
| **WayFASTER**<br>(Gasparino et al., UIUC, ICRA 2024) | Receding horizon estimator for terrain classification and temporal layer for past frames. Discrete cost values. | Partial | **Input:** RGB, Depth Images<br>**Output:** 2D BEV Traversability Map<br>**Planner:** MPPI. |
| **SALON**<br>(Sivaprakasam et al., CMU, ICRA 2025) | Generates speed/cost maps with Gaussian Process (instead of MLP). Probabilistic costs handling out-of-distribution (OOD) data. Needs 1-click to seed, adapts online. | Yes, ROS 1 | **Input:** RGB Images<br>**Output:** 2D BEV Traversability Map<br>**Planner:** MPPI. |
| **V-STRONG**<br>(Jung et al., ICRA 2024) | Uses SAM to generate labels over non-traversed terrain for better self-supervised training. | - | **Input:** RGB Images<br>**Output:** 2D Traversability Map<br>**Planner:** MPPI. |
| **Follow the Footprints**<br>(Jeon et al., ICRA 2024) | Fuses geometry with visual semantics via footprint self-supervision. | - | - |

### Evidential and Traction-Based Learning

| Paper / Authors | Summary | Code / OS | Planner / Details |
| :--- | :--- | :--- | :--- |
| **EVORA**<br>(Cai et al., MIT ACL, T-RO 2024) | Evidential deep learning for distribution of distributions (higher-order Dirichlet). Separates aleatoric (e.g., tall grass) vs epistemic (e.g., ice) uncertainties. | Partial (Planner) | **Input:** RGB Images, 2.5D Elevation<br>**Output:** Dirichlet Distribution Map<br>**Planner:** MPPI. |
| **PIETRA**<br>(Cai et al., MIT ACL+GMU, RA-L 2025) | Extends EVORA. For OOD terrain, uses simple physics equations for guesses instead of maximum obstacle penalty. | No Codebase | **Input:** RGB Images, 2.5D Elevation<br>**Output:** Dirichlet Distribution Map<br>**Planner:** MPPI. |
| **MEDIRL**<br>(Song et al., SNU, ICRA 2026) | Achieves uncertainty decomposition via Maximum Entropy Deep IRL with a CVAE decoder ensemble. | - | - |

### Implicit Neural Representations

| Paper / Authors | Summary | Code / OS | Planner / Details |
| :--- | :--- | :--- | :--- |
| **TRAIL**<br>(Jia et al., MIT ACL, 2025) | Flips the paradigm: entire terrain mapped as neural field (SIREN). Uses gradient descent (e.g., Adam) to optimize paths directly instead of grids or MPC. | No Codebase | **Input:** LiDAR Point Clouds<br>**Output:** 3D Neural Field<br>**Planner:** Gradient Descent (End-to-end). |

### Hybrid and Mixture-of-Experts

| Paper / Authors | Summary | Code / OS | Planner / Details |
| :--- | :--- | :--- | :--- |
| **NavMoE**<br>(He et al., UMD+CMU, ICRA 2026) | Considers both geometric and semantic features via Mixture of Experts. Gating layers choose the expert which returns discrete cost with confidence. | No Codebase | Planner uses motion primitives. |
| **Leung et al.** | Combines semantic segmentation with probabilistic geometric estimation. | - | - |
| **GSAT**<br>(Cho et al., ICRA 2026) | Geometric traversability via anomaly detection to address positive-only labels in self-supervision. | - | - |

### Proprioceptive and Physics-Informed Methods

| Paper / Authors | Summary | Code / OS | Planner / Details |
| :--- | :--- | :--- | :--- |
| **Bourbon et al.**<br>(ICRA 2026) | Estimates motion resistance from proprioceptive feedback, transferring via visual perception. | - | - |
| **Yuan et al.**<br>(ICRA 2026) | Learns bumpiness (chassis acceleration) via incremental online learning. | - | - |
| **WildFusion**<br>(Liu et al., ICRA 2025) | Fuses vision, vibration, and tactile signals. | - | - |
| **FDM**<br>(Roth et al., ETH RSL, 2025) | Predicts future robot states from elevation maps for zero-shot MPPI planning. | - | - |
| **TADPO**<br>(Wu et al., CMU, ICRA 2026) | Extends PPO with terrain-aware demonstrations for off-road navigation. | - | - |
| **RANGER** | Monocular zero-shot semantic navigation using 3D foundation models and VLMs. | - | - |

---

## Other Selected Works

| Paper / Name | Summary | Code / OS | Planner / Details |
| :--- | :--- | :--- | :--- |
| **Irispath** | Work at IISc. Self-supervised, similar to WayFASTER (ResNet backbone, MLPs). Uses Z-acceleration as cost for self-supervision. | - | Outputs costmap. |
| **HDIF** | Pre-dates WVN (2203). Outputs BEV costmap terrain. Self-supervised using IMU for labeling. Trains offline with ResNet backbone. | - | - |
| **Nomad** | End-to-end trajectory generation via diffusion to handle exploration vs navigation. Goal masking allows unified network. Focuses on indoor/urban. | - | No costmap. |
| **MAMOTH** | - | - | - |

---

## Code, Datasets, and ROS 2 Integration

### Open-Source Implementations

Most methods lack public implementations, but the following foundational infrastructure packages and codebases are available:
- **`elevation_mapping_cupy` (ETH RSL):** GPU-accelerated, ROS 1+2 compatible.
- **`grid_map` (ANYbotics):** ROS 2. Universal 2.5D grid data structure.
- **`traversability_estimation` (ETH RSL):** ROS 2.
- **WVN:** ROS 1 + Docker + pretrained models.
- **WayFASTER:** MIT license, training codes.
- **SEB-Naver**
- **GP-Navigation**
- **TANGO, LeSTA, FDM, PACER, `mppi_numba` (EVORA's CUDA planner)**

### Datasets

- **STONE** (Park et al., ICRA 2026): 128-ch LiDAR + 6 cameras + 4D radar with automated 3D traversability annotation.
- **ORAD-3D** (Min et al., ICRA 2026): Largest off-road dataset spanning woodlands, farmlands, grasslands, diverse weather.
- **UT Austin VertiChal:** Platform-specific datasets for vertically challenging terrain.

--- 

Note: This work was done while searching and improving existing methods on traversibility estimation and uneven terrain navigation to autonomously navigate a robot on highly uneven terrain at RBCCPS, IISc Banglore. 
