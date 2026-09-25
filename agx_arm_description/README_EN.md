# agx_arm_description README

ROS 2 description package for the AgileX series of robotic arms. Through the unified Xacro entry file `urdf/agx_arm_description.urdf.xacro`, it supports a parameterized, flexible combination of multiple robotic arm models, end effectors, camera stands, and the RealSense D435 camera, and can be visualized as a URDF model in RViz2. USD format models are also provided, which can be used directly in simulation environments such as Isaac Sim.

## Table of Contents

- [Supported Robotic Arm Models](#supported-robotic-arm-models)
- [Package Structure](#package-structure)
- [Dependencies](#dependencies)
- [Installation](#installation)
- [Usage](#usage)
- [Launch Parameters](#launch-parameters)
- [Common Launch Commands](#common-launch-commands)
- [Camera Stand Mounting Logic](#camera-stand-mounting-logic)
- [Referencing from Other Launch Files](#referencing-from-other-launch-files)
- [Direct Xacro Parsing](#direct-xacro-parsing)
- [USD Models](#usd-models)
- [License](#license)

## Supported Robotic Arm Models

| `arm_type` | Model        | Available End Effectors                                      |
| ---------- | ------------ | ------------------------------------------------------------ |
| `piper`    | Piper        | `none` / `gripper` / `revo2_left` / `revo2_right` / `teach` / `pika` |
| `piper_h`  | Piper H      | `none` / `gripper` / `revo2_left` / `revo2_right` / `teach` / `pika` |
| `piper_l`  | Piper L      | `none` / `gripper` / `revo2_left` / `revo2_right` / `teach` / `pika` |
| `piper_x`  | Piper X      | `none` / `gripper` / `revo2_left` / `revo2_right` / `teach` / `pika` |
| `nero`     | Nero         | `none` / `gripper` / `revo2_left` / `revo2_right` / `teach` / `pika` |
| `revo2`    | Revo2 Dexterous Hand | Select `left` / `right` via the `revo2_side` parameter |

## Package Structure

```Plain
agx_arm_description/
├── agx_arm_urdf/ # git submodule (from agilexrobotics/agx_arm_urdf)
│ ├── piper/
│ │ ├── meshes/dae/
│ │ └── urdf/
│ ├── piper_h/
│ ├── piper_l/
│ ├── piper_x/
│ ├── nero/
│ └── revo2/
├── meshes/
│ └── realsense_mid_stand.dae # Camera stand 3D model
├── urdf/
│ ├── agx_arm_description.urdf.xacro # Unified entry Xacro file
│ ├── teach_pendant.urdf.xacro # Teach pendant model file
│ ├── pika2_gripper.urdf # Pika2 gripper model file
│ └── *.usd # USD format models for each robotic arm, supporting simulation environments
├── launch/
│ ├── display.launch.py # Main launch file with RViz2 visualization
├── config/
│ └── arm_config.yaml
├── rviz/
│ └── default.rviz
├── CMakeLists.txt
└── package.xml
```

## Dependencies

**ROS 2 packages:**

```bash
sudo apt install \
ros-$ROS_DISTRO-robot-state-publisher \
ros-$ROS_DISTRO-joint-state-publisher \
ros-$ROS_DISTRO-joint-state-publisher-gui \
ros-$ROS_DISTRO-rviz2 \
ros-$ROS_DISTRO-xacro
```

## Installation

### 1. Clone and initialize the submodule

```bash
cd ~/ros2_ws/src
# Clone this package
git clone https://github.com/agilexrobotics/agx_arm_sim.git
# Initialize the agx_arm_urdf submodule
cd agx_arm_description
git submodule update --init --recursive
```

### 2. Install dependencies and build

```bash
cd ~/ros2_ws
rosdep install --from-paths src --ignore-src -r -y
colcon build
source install/setup.bash
```

## Usage

### Launch Parameters

| Parameter           | Default  | Options                                                     | Description                                                   |
| -------------------- | -------- | ---------------------------------------------------------- | ------------------------------------------------------------ |
| `arm_type`          | `piper`  | `piper`/`piper_h`/`piper_l`/`piper_x`/`nero`/`revo2`       | Robotic arm model                                             |
| `end_effector`      | `none`   | `none`/`gripper`/`revo2_left`/`revo2_right`/`teach`/`pika` | End effector (not valid for the `revo2` model): `teach` is the teach pendant model, `pika` is the Pika2 electric gripper |
| `revo2_side`        | `right`  | `left`/`right`                                             | Only effective when `arm_type:=revo2`                        |
| `with_camera_stand` | `false`  | `true`/`false`                                             | Whether to load the camera stand                              |
| `with_camera`       | `false`  | `true`/`false`                                             | Whether to load the RealSense D435 (requires `with_camera_stand:=true` as well) |
| `use_gui`           | `true`   | `true`/`false`                                             | Whether to launch the joint state slider GUI                  |
| `rviz_config`       | built-in config | Any `.rviz` path                                     | Custom RViz2 config file                                       |

### Common Launch Commands

```bash
# Default (base piper, no end effector, no camera)
ros2 launch agx_arm_description display.launch.py

# Specify a model
ros2 launch agx_arm_description display.launch.py arm_type:=piper_h

# With gripper
ros2 launch agx_arm_description display.launch.py arm_type:=piper end_effector:=gripper

# With right-hand dexterous hand
ros2 launch agx_arm_description display.launch.py arm_type:=nero end_effector:=revo2_right

# revo2 left hand
ros2 launch agx_arm_description display.launch.py arm_type:=revo2 revo2_side:=left

# With Pika2 gripper
ros2 launch agx_arm_description display.launch.py arm_type:=piper end_effector:=pika

# With teach pendant
ros2 launch agx_arm_description display.launch.py arm_type:=piper end_effector:=teach

# With camera stand (no camera)
ros2 launch agx_arm_description display.launch.py \
arm_type:=piper end_effector:=gripper \
with_camera_stand:=true

# With camera stand + RealSense D435
ros2 launch agx_arm_description display.launch.py \
arm_type:=piper end_effector:=gripper \
with_camera_stand:=true with_camera:=true

# nero + gripper + camera (automatically uses the nero-specific mounting offset)
ros2 launch agx_arm_description display.launch.py \
arm_type:=nero end_effector:=gripper \
with_camera_stand:=true with_camera:=true
```

Visualizing nero + gripper + camera

```
ros2 launch agx_arm_description display.launch.py \
arm_type:=nero end_effector:=gripper \
with_camera_stand:=true with_camera:=true
```

![](./img/display_nero.png)

## Camera Stand Mounting Logic

By default, the camera stand is fixed to the end flange (gripper_base). For different robotic arm models, the corresponding mounting offset is automatically applied to ensure the camera is positioned accurately.

## Referencing from Other Launch Files

You can directly reference this package's parameterized URDF generation logic in your own launch files:

```python
from launch import LaunchDescription
from launch_ros.actions import Node
import xacro

def generate_launch_description():
    # Process the xacro file
    xacro_file = os.path.join(
        get_package_share_directory("agx_arm_description"),
        "urdf", "agx_arm_description.urdf.xacro"
    )
    robot_description_content = xacro.process_file(
        xacro_file,
        mappings={
            "arm_type": "piper",
            "end_effector": "gripper",
            "with_camera_stand": "true",
            "with_camera": "true"
        }
    ).toxml()

    # Launch robot_state_publisher
    return LaunchDescription([
        Node(
            package="robot_state_publisher",
            executable="robot_state_publisher",
            parameters=[{"robot_description": robot_description_content}],
        )
    ])
```

## Direct Xacro Parsing

> **Requirement**: This operation depends on the `xacro` tool provided by ROS2. If you haven't installed it yet, you can install it with:
>
> ```bash
> sudo apt install ros-$ROS_DISTRO-xacro
> ```
>
> Note: the xacro files in this repository use ROS package path lookup syntax (`$(find ...)`) and cannot be parsed with a standalone xacro library outside of ROS. You must use the official xacro tool provided in a ROS2 environment.

You can parse the xacro file directly on the command line for debugging, without ROS 2 launch:

```bash
# base piper
xacro urdf/agx_arm_description.urdf.xacro arm_type:=piper

# nero + gripper + camera
xacro urdf/agx_arm_description.urdf.xacro \
arm_type:=nero \
end_effector:=gripper \
with_camera_stand:=true \
with_camera:=true

# piper + teach pendant
xacro urdf/agx_arm_description.urdf.xacro arm_type:=piper end_effector:=teach

# piper + Pika2 gripper
xacro urdf/agx_arm_description.urdf.xacro arm_type:=piper end_effector:=pika

# output to file
xacro urdf/agx_arm_description.urdf.xacro arm_type:=piper > piper.urdf
```

### Converting with the ros2 xacro tool

You can also use the official ROS2 `ros2 xacro` tool to perform the conversion. This tool automatically handles ROS package path lookup and is suitable for use within a ROS2 workspace:

```bash
# using the nero model as an example, convert the full gripper+camera configuration to a urdf file
ros2 run xacro xacro \
urdf/agx_arm_description.urdf.xacro \
arm_type:=nero \
end_effector:=gripper \
with_camera_stand:=true \
with_camera:=true \
-o nero_full.urdf
```

### Converting directly with the Python xacro library

If you need to perform the conversion directly in Python code, you can use the `xacro` library's API, again using the nero model as an example:

```python
import xacro
import os
from ament_index_python.packages import get_package_share_directory

# Get the package's install path
pkg_share_dir = get_package_share_directory("agx_arm_description")
xacro_file_path = os.path.join(pkg_share_dir, "urdf", "agx_arm_description.urdf.xacro")

# Process the xacro file, passing in the nero model's configuration parameters
robot_urdf_content = xacro.process_file(
    xacro_file_path,
    mappings={
        "arm_type": "nero",
        "end_effector": "gripper",
        "with_camera_stand": "true",
        "with_camera": "true"
    }
).toxml()

# Save the generated urdf content to a file
with open("nero_full.urdf", "w", encoding="utf-8") as f:
    f.write(robot_urdf_content)
```

## USD Models

This package also provides USD format model files for each robotic arm, located in the `urdf/` directory. They can be imported directly into simulation environments such as Isaac Sim without any additional format conversion, working out of the box.

## License

MIT License
