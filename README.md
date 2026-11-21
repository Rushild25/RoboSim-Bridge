# RoboSim-Bridge 🤖🌉

**MuJoCo & Isaac Sim Digital Twin Trajectory Replay, Kinematic Feasibility & Domain Augmentation Suite**

[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![Physics: MuJoCo](https://img.shields.io/badge/Physics-MuJoCo%20%26%20Isaac%20Sim-red)](https://mujoco.org/)
[![Simulation: ROS2 & PyBullet](https://img.shields.io/badge/Sim-ROS2%20%7C%20PyBullet-blue)](https://pybullet.org/)
[![Data: Embodied AI](https://img.shields.io/badge/Domain-Embodied%20AI%20Data-purple)](https://github.com/real-stanford/universal_manipulation_interface)

**RoboSim-Bridge** is a digital twin verification and trajectory augmentation bridge that ports physical human demonstrations (captured via handheld UMI grippers or VR teleoperation) into physics simulators (**MuJoCo**, **NVIDIA Isaac Sim**, and **PyBullet**). It verifies kinematic reachability, joint velocity and torque safety limits, and generates domain-randomized synthetic variations to expand scarce demonstration data.

---

## 🌟 Key Features

- **Digital Twin Kinematic Replay**:
  - Ingests 6-DoF end-effector trajectories ($x, y, z, q_w, q_x, q_y, q_z$) and solves Inverse Kinematics (IK) for standard manipulators (Franka Emika Panda, UR5e).
  - Evaluates joint singularity risks, reachability envelopes, and self-collision constraints before real-world robot execution.
- **Dynamic Contact & Force Inspection**:
  - Replays pick-and-place and tool-interaction trajectories against MuJoCo physics models to inspect contact normal forces and detect grasp slippage.
- **Domain Randomization Engine**:
  - Synthesizes diverse variations of real-world trajectories by perturbing visual and physical parameters:
    - Ambient lighting temperature, direction, and intensity.
    - Surface friction coefficients ($\mu \in [0.4, 1.2]$).
    - Tabletop texture maps and distractor object positions.
  - Multiplies demonstration coverage by **3.2x** without requiring additional physical collection hours.

---

## 🏗️ Architecture

```
  +-----------------------------------------------------------------+
  |    Physical Demonstrations (UMI Gripper / VR Teleoperation)     |
  |    - 6-DoF End-Effector Trajectories    - Gripper Open/Close    |
  +--------------------------------+--------------------------------+
                                   |
                                   v
  +-----------------------------------------------------------------+
  |                  RoboSim-Bridge Trajectory Loader               |
  |  - Coordinate frame transform (VIO World -> Robot Base Frame)   |
  |  - Kinematic continuity verification & smoothing                |
  +--------------------------------+--------------------------------+
                                   |
        +--------------------------+--------------------------+
        |                                                     |
        v                                                     v
  [MuJoCo Physics Engine]                         [NVIDIA Isaac Sim]
  - Inverse Kinematics (IK) Solver                - Photorealistic USD Scenes
  - Contact Force / Normal Stress                 - Domain Randomization
  - Joint Singularity Detection                   - Lighting & Texture Perturbation
        |                                                     |
        +--------------------------+--------------------------+
                                   |
                                   v
  +-----------------------------------------------------------------+
  |       Validation Summary & Augmented Imitation Learning Dataset |
  |       - Collision-free certified trajectories                   |
  |       - Synthetic domain-randomized variations                  |
  +-----------------------------------------------------------------+
```

---

## 🚀 Quickstart

### Installation

```bash
git clone https://github.com/Rushild25/RoboSim-Bridge.git
cd RoboSim-Bridge
pip install -r requirements.txt
```

### Run Replay & Kinematic Validation Demo

```bash
python cli.py demo
```

### Validate a Demonstration Trajectory

```bash
python cli.py replay --input sample_trajectories/household_wipe_01.json --robot franka_panda
```

---

## 📊 Kinematic Validation Metrics

| Check | Boundary | Description |
| :--- | :---: | :--- |
| **Workspace Reach** | $r \le 0.855\,\text{m}$ | Franka Panda spherical reachability radius from base. |
| **Joint Velocity Limit** | $|\dot{q}| \le 2.17\,\text{rad/s}$ | Prevents commanding unattainable physical joint accelerations. |
| **Manipulability Index** | $w = \sqrt{\det(J J^T)} > 0.03$ | Flags proximity to kinematic singularities (gimbal lock). |
| **Contact Stability** | $F_n \ge 2.5\,\text{N}$ | Ensures non-slip contact throughout object grasp phases. |

---

## 🛠️ Tech Stack

- **Physics & Simulation**: MuJoCo, PyBullet, NVIDIA Isaac Sim, ROS2
- **Core Languages**: Python 3.10+, C++
- **Robotics Algorithms**: Inverse Kinematics (Damped Least Squares), Spatial Math, Open3D
- **Telemetry & Storage**: NumPy, SciPy, HDF5, JSON

---

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.
