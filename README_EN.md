# agx_arm_sim README

A ROS2 simulation and development toolkit for the AgileX series of robotic arms, providing a complete set of packages for algorithm development, simulation testing, and visual debugging. It integrates robotic arm description, motion planning, and simulation adaptation capabilities, helping developers quickly set up a robotic arm development environment and supporting the full development workflow from model visualization to motion planning and physics simulation.

## Repository Structure

This repository integrates several sub-packages to provide complete support for robotic arm development. The overall structure is as follows:

```Plain
agx_arm_sim/
├── agx_arm_description/   # Robotic arm URDF/Xacro description package
│   ├── Provides parameterized robotic arm models, supporting flexible configuration of multiple models and end effectors
│   ├── Includes USD format models that can be used directly in simulation environments such as Isaac Sim
│   └── Supports RViz2 visual debugging for quick verification of model configuration
├── Moveit2/               # MoveIt2 motion planning package
│   ├── Provides kinematics solving, collision detection, and trajectory planning capabilities
│   └── Adapted for the full range of robotic arm models, supporting simulation and testing of motion planning
├── realsense2_description/ # RealSense camera description package
│   └── Provides URDF models for cameras such as the RealSense D435, supporting camera simulation and visualization
└── .gitmodules            # Submodule configuration file, used to manage model resource submodules
```

## Download and Installation

### 1. Clone the repository and initialize submodules

```bash
cd ~/ros2_ws/src
# Clone this repository
git clone https://github.com/agilexrobotics/agx_arm_sim.git
# Initialize submodules to pull the complete model resources
cd agx_arm_sim
git submodule update --init --recursive
```

### 2. Install dependencies

```bash
# Install the required ROS2 dependency packages
sudo apt-get install ros-humble-control* ros-humble-joint-trajectory-controller ros-humble-joint-state-* ros-humble-gripper-controllers ros-humble-trajectory-msgs ros-humble-topic-based-ros2-control ros-humble-moveit*
```

### 3. Build the workspace

```bash
colcon build
# Configure environment variables
source install/setup.bash
```

## Supported Robotic Arm Models

This repository fully supports the entire AgileX series of robotic arms, allowing flexible switching between models and end-effector configurations via parameters. The specifically supported models are as follows:

| Model ID  | Model Name   | Supported End Effectors                                          |
| --------- | ------------ | ------------------------------------------------------------------ |
| `piper`   | Piper        | None / Electric Gripper / Revo2 Dexterous Hand / Teach Pendant / Pika2 Gripper |
| `piper_h` | Piper H      | None / Electric Gripper / Revo2 Dexterous Hand / Teach Pendant / Pika2 Gripper |
| `piper_l` | Piper L      | None / Electric Gripper / Revo2 Dexterous Hand / Teach Pendant / Pika2 Gripper |
| `piper_x` | Piper X      | None / Electric Gripper / Revo2 Dexterous Hand / Teach Pendant / Pika2 Gripper |
| `nero`    | Nero         | None / Electric Gripper / Revo2 Dexterous Hand / Teach Pendant / Pika2 Gripper |
| `revo2`   | Revo2 Dexterous Hand | Supports independent left/right hand configuration          |

## Supported Simulation Environments

This repository provides adaptation for several types of simulation and development environments to meet different development and testing needs. The currently supported environments are as follows:

| Simulation Environment      | Description                                                                                                                                                              |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Isaac Sim High-Fidelity Simulation | Provides a complete USD format robotic arm model that can be imported directly into the NVIDIA Isaac Sim environment, supporting high-precision physics simulation and adapting to embodied AI development needs |
| MoveIt2 Planning Simulation  | Using the MoveIt2 package, motion planning simulation tests for the robotic arm can be completed, supporting verification of collision detection and trajectory planning, enabling rapid development of robotic arm control algorithms |
| RViz2 Visual Debugging       | Supports model visualization and joint debugging of the robotic arm in RViz2, allowing quick verification of the correctness of the robotic arm model configuration without launching a full simulation |

| Robotic Arm Model | Isaac Sim | Moveit2 | Mujoco | Gazebo |
| ------------------ | --------- | ------- | ------ | ------ |
| `piper`            | ✅         | ✅       | ✅      | ✅      |
| `piper_h`          | ✅         | ✅       |        | ✅      |
| `piper_l`          | ✅         | ✅       |        | ✅      |
| `piper_x`          | ✅         | ✅       |        | ✅      |
| `nero`             | ✅         | ✅       | ✅      | ✅      |

## License

MIT License
