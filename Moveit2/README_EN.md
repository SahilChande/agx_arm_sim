# Moveit2 README

This directory contains a collection of MoveIt2 motion planning configuration packages for the AgileX series of robotic arms. It provides an independent MoveIt2 configuration for each robotic arm model, including complete configuration for kinematics solving, collision detection, and trajectory planning, supporting motion planning development for robotic arms as well as co-simulation with Gazebo and Isaac Sim.

## Directory Structure

This directory contains independent MoveIt2 configuration packages for each model. Each configuration package corresponds to one robotic arm model and contains the motion planning configuration for that model:

- `nero_gripper_moveit_config`: MoveIt2 configuration package for the Nero robotic arm
- `piper_gripper_moveit_config`: MoveIt2 configuration package for the Piper robotic arm
- `piper_h_gripper_moveit_config`: MoveIt2 configuration package for the Piper H robotic arm
- `piper_l_gripper_moveit_config`: MoveIt2 configuration package for the Piper L robotic arm
- `piper_x_gripper_moveit_config`: MoveIt2 configuration package for the Piper X robotic arm

## Usage, Using Nero as an Example

### 1. Launching the MoveIt2 demo standalone

You can directly launch the MoveIt2 demo node to debug motion planning in RViz2, without launching an external simulation environment:

```bash
# Launch the MoveIt2 demo node for the Nero robotic arm
ros2 launch nero_gripper_moveit_config demo.launch.py
```

Once launched, RViz2 automatically loads the Nero robotic arm model. You can use the interactive markers provided by MoveIt2 to drag the arm's end effector, and motion planning is completed automatically so you can verify the planning result.

![](./img/nero_moveit.png)

### 2. Co-simulation with Gazebo

To combine MoveIt2 with Gazebo for co-simulation and motion planning, follow these steps:

```bash
# Launch the co-simulation
ros2 launch nero_gripper_moveit_config gazebo_moveit.launch.py 
```

Once launched, Gazebo and RViz2 automatically load the Nero robotic arm model. You can use the floating interactive markers provided by MoveIt2 to drag the arm's end effector and plan motion; the model in Gazebo moves in sync.

![](./img/nero_gazebo.png)

### 3. Co-simulation with Isaac Sim

You can combine MoveIt2 with Isaac Sim to perform high-fidelity physics simulation, testing the real physical effects of motion planning. Follow these steps:

#### Step 1: Launch Isaac Sim and load the model

1. Open the NVIDIA Isaac Sim simulation environment
2. Import the Nero robotic arm's USD format model from this repository (located in the usd resource directory of the `agx_arm_description` package)
3. Configure Isaac Sim's ActionGraph to launch the ROS2 bridge nodes:
   1. Add a ROS2 subscriber node for joint control, with the topic name set to `/isaac_joint_command`
   2. Add a ROS2 publisher node for joint state, with the topic name set to `/isaac_joint_states`
   3. Once complete, start the Isaac Sim simulation

#### Step 2: Modify the MoveIt2 configuration to adapt to Isaac Sim

Before launching the MoveIt2 nodes, you need to modify the MoveIt2 hardware configuration to adapt to Isaac Sim's ROS2 topic interface:

1. Open the ros2_control configuration file in the MoveIt2 configuration package:

```bash
gedit ~/ros2_ws/src/Moveit2/nero_gripper_moveit_config/config/nero_description.ros2_control.xacro
```

1. Modify the hardware configuration section, replacing the default mock hardware with topic-based bridge hardware, and update the topic names to match Isaac Sim's bridge topics:

```xml
<hardware>
<!-- Comment out the default mock hardware, replace with topic-based bridge hardware -->
<!-- <plugin>mock_components/GenericSystem</plugin> -->
<plugin>topic_based_ros2_control/TopicBasedSystem</plugin>
<!-- Update the topic names to match Isaac Sim's ROS2 bridge topics -->
<param name="joint_commands_topic">/isaac_joint_commands</param>
<param name="joint_states_topic">/isaac_joint_states</param>
</hardware>
```

1. After saving your changes, rebuild the workspace for the configuration to take effect:

```bash
cd ~/ros2_ws
colcon build
source install/setup.bash
```

#### Step 3: Launch the MoveIt2 planning nodes

In a ROS2 terminal, launch the MoveIt2 nodes in order:

```bash
# 1. Launch the MoveIt2 move_group node, which handles motion planning requests
ros2 launch nero_gripper_moveit_config move_group.launch.py
```

Open a new terminal and launch the RViz2 visualization node:

```bash
# 2. Launch the RViz2 visualization node, for viewing the robotic arm state and planning results
ros2 launch nero_gripper_moveit_config moveit_rviz.launch.py
```

#### Step 4: Complete the co-simulation

Once launched, the robotic arm's joint states in Isaac Sim are automatically synchronized with MoveIt2, and you can interact with motion planning in RViz2:

- Drag the interactive marker to specify the target pose for the robotic arm
- MoveIt2 automatically performs collision detection and trajectory planning
- The completed planned trajectory is automatically sent to Isaac Sim, driving the simulated robotic arm to move, achieving a high-fidelity co-simulation test

![](./img/nero_isaac.png)

## License

MIT License
