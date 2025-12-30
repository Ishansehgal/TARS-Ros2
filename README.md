# TARS Robot - ROS2 Humble + MicroROS (ESP32)

> **Work in Progress** - This branch is under active development.

## Demo Videos

https://github.com/user-attachments/assets/one.mp4

https://github.com/user-attachments/assets/two.mp4

---

## Project Status

| Feature | Status |
|---------|--------|
| Digital Twin (Gazebo Simulation) | Complete |
| Real Robot Movement (ESP32 + MicroROS) | Complete |
| Sim-to-Real Feedback Bridge | Complete |
| Raspberry Pi Integration | Planned |
| Camera + Visual Odometry | Planned |
| Autonomous Navigation | Planned |

---

## Overview

This is a ROS2 Humble port of the [TARS-AI](https://github.com/TARS-AI-Community/TARS-AI) robot, enabling:
- **Digital Twin**: Run TARS in Gazebo Ignition simulation
- **Real Hardware**: Control the physical robot via ESP32 + MicroROS
- **Sim-to-Real**: Bridge sensor feedback from real robot to simulation

---

## Repository Structure

```
src/
├── tars_robot_description/     # Simulation Package (Gazebo)
│   ├── urdf/                   # Robot URDF/Xacro files
│   ├── meshes/                 # 3D mesh files (STL)
│   ├── launch/                 # Launch files
│   │   └── gazebo.launch.py    # Main simulation launcher
│   ├── config/                 # Controller configs
│   └── tars_robot_description/ # Python scripts
│       └── test_movement.py    # Movement control script
│
├── tars_v2_gazebo/             # Gazebo world files
│
├── tars_esp32_microros/        # ESP32 MicroROS Code
│   ├── tars_controller.cpp     # Upload to ESP32-S3
│   └── README.md               # Setup instructions
│
└── gazebo_ros2_control/        # Control plugin (dependency)
```

---

## Quick Start

### Prerequisites
- Ubuntu 22.04
- ROS2 Humble
- Gazebo Fortress (Ignition)
- MicroROS Agent (for real robot)

### 1. Clone the Repository
```bash
mkdir -p ~/tars_ws/src
cd ~/tars_ws/src
git clone -b ros2-humble-microros https://github.com/Ishansehgal/TARS-Ros2.git .
```

### 2. Install Dependencies
```bash
cd ~/tars_ws
rosdep install --from-paths src --ignore-src -r -y
```

### 3. Build
```bash
colcon build --symlink-install
source install/setup.bash
```

---

## Usage

### Run Simulation (Digital Twin)
```bash
ros2 launch tars_robot_description gazebo.launch.py
```

### Control Robot Movement
```bash
ros2 run tars_robot_description test_movement
```

### View Joint States
```bash
ros2 topic echo /joint_states
```

---

## Real Robot (ESP32 + MicroROS)

### Hardware Requirements
- ESP32-S3 DevKit
- PCA9685 PWM Driver
- 3x Servo Motors (Center lift + 2 leg drives)
- Power supply (5V for servos)

### Setup

1. Flash `tars_esp32_microros/tars_controller.cpp` to ESP32 using PlatformIO or Arduino IDE

2. Update WiFi credentials in the code:
   ```cpp
   char wifi_ssid[] = "YOUR_WIFI";
   char wifi_password[] = "YOUR_PASSWORD";
   char agent_ip[] = "YOUR_PC_IP";
   ```

3. Start MicroROS Agent on your PC:
   ```bash
   ros2 run micro_ros_agent micro_ros_agent udp4 --port 8888
   ```

4. Control with cmd_vel:
   ```bash
   ros2 topic pub /cmd_vel geometry_msgs/Twist "{linear: {x: 0.5}}"
   ```

---

## ROS2 Topics

| Topic | Type | Description |
|-------|------|-------------|
| `/cmd_vel` | `geometry_msgs/Twist` | Movement commands |
| `/servo_control` | `std_msgs/Int32MultiArray` | Direct servo control `[channel, angle]` |
| `/servo_feedback` | `std_msgs/Int32MultiArray` | Current servo positions |
| `/joint_states` | `sensor_msgs/JointState` | Simulation joint states |
| `/imu` | `sensor_msgs/Imu` | Simulated IMU data |

---

## Roadmap

- [x] Basic simulation in Gazebo
- [x] ESP32 MicroROS integration
- [x] Servo feedback publishing
- [ ] Raspberry Pi 5 integration
- [ ] Camera module for visual odometry
- [ ] Nav2 autonomous navigation

---

## Contributing

This is an experimental branch. Contributions are welcome:
1. Fork this repository
2. Create a feature branch
3. Submit a pull request

---

## License

MIT License - See LICENSE for details.

---

## Credits

- Original TARS-AI: [TARS-AI Community](https://github.com/TARS-AI-Community/TARS-AI)
- Inspired by: Interstellar (2014)
