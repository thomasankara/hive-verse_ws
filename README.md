# hive-verse\_ws

A ROS 2 **Humble** + **Gazebo Harmonic** simulation workspace dedicated to spawning and exercising Hive Robotics’ differential‑drive delivery robot (and its future traffic partners) in a variety of virtual worlds.

> **Scope** – *Simulation only*. No real‑sensor drivers, navigation logic or business code lives here; it stays in the production workspace.

---

## ✨ Key Goals

|  #  | Goal                                                       | Notes                                                    |
| --- | ---------------------------------------------------------- | -------------------------------------------------------- |
|  1  | **Launch any world** quickly                               | Worlds stored in `hive_worlds/worlds` (indoor & outdoor) |
|  2  | **Spawn 1…N robots** with clean namespaces                 | `robot1/`, `robot2/` …                                   |
|  3  | **Expose all simulated sensors** over ROS 2 topics         | Airy LiDAR ×2, Hesai JT16 LiDAR, IMU, camera (optional)  |
|  4  | **Command robots** via `/cmd_vel` or direct joint velocity | `ros2` ↔ Gz bridge handles the transport                 |
|  5  | **Animate the parcel casier**                              | Revolute joint + `ros2_control` position controller      |
|  6  | **Publish a virtual sensor** `/casier_open` (Bool)         | Implemented in `hive_casier_sensors`                     |
|  7  | **Traffic playback** (cars / people / bikes)               | Future phases 10‑12                                      |

---

## 🗂 Workspace Layout

```
hive-verse_ws/
├── scripts/                       # helper & bootstrap scripts
│   └── install_requirements.sh  
└── src/
    ├── hive_worlds/               # .sdf worlds, textures
    │   └── worlds/
    │       ├── warehouse.world
    │       └── campus.world
    ├── hive_robot_description/    # URDF / Xacro & meshes
    │   ├── urdf/
    │   └── meshes/
    ├── hive_robot_gazebo/         # spawn scripts, ros2_control cfg, plugins
    │   └── launch/
    ├── hive_multi_spawn_tools/    # bring‑up launchers & helper scripts
    │   └── launch/
    └── hive_casier_sensors/       # python nodes for virtual sensors
        ├── casier_state_node.py
        └── launch/
```

### Core Packages

| Package                  | Responsibility                                                            |
| ------------------------ | ------------------------------------------------------------------------- |
| `hive_worlds`            | Store + version worlds (SDF + resources)                                  |
| `hive_robot_description` | Fully modular robot model incl. casier joint                              |
| `hive_robot_gazebo`      | `ros2_control` parameters, spawn utilities, custom plugins if needed      |
| `hive_multi_spawn_tools` | General bring‑up (`sim_bringup.launch.py`) with args `world` & `n_robots` |
| `hive_casier_sensors`    | Publishes `/casier_open` by thresholding `door_hinge` joint state         |

---

## 🔧 Dependencies

* **Ubuntu 22.04** + ROS 2 Humble (`ros-humble-desktop-full`)
* **Gazebo Harmonic** `gz-harmonic`
* ROS–Gz bridge `ros-humble-ros-gzharmonic`
* Colcon / vcs

> See `docs/INSTALL.md` (to‑be‑added) for the exact apt commands.

---

## 🚀 Quick Start

```bash
# create workspace if not yet cloned
mkdir -p ~/hive-verse_ws/src && cd ~/hive-verse_ws

git clone <this‑repo‑url> src/hive-verse_ws

# build everything
colcon build --symlink-install
source install/setup.bash

# launch 1 robot in the warehouse world
ros2 launch hive_multi_spawn_tools sim_bringup.launch.py \
          world:=warehouse n_robots:=1
```

*Publish* `/robot1/cmd_vel` to drive; echo `/robot1/casier_open` to check casier state.

---

## 🛣 Roadmap

| Phase                        | Deliverables                                       | Status |
| ---------------------------- | -------------------------------------------------- | ------ |
|  0 Bootstrap                 | Workspace skeleton, Harmonic install               | ✅ done |
|  1 Robot URDF clean‑up       | Airy×2, Hesai JT16, passive casters                | ☐      |
|  2 Casier joint + controller | `door_hinge` + `door_position_controller`          | ☐      |
|  3 Virtual sensor node       | `/casier_open` Bool topic                          | ☐      |
|  4 Worlds & multi‑spawn      | `warehouse.world`, `campus.world`, bring‑up launch | ☐      |
|  5 ROS ↔ Gz bridge           | Bridge YAML + latency test                         | ☐      |
|  6 Smoke tests               | `run_demo.sh`, CI action                           | ☐      |
|  7 Docs                      | This README + per‑package READMEs                  | ☐      |
|  8 CI Quality                | ament\_lint, headless sim check                    | ☐      |
|  10 Actors (pedestrians)     | SDF `<actor>` + trajectories                       | ☐      |
|  11 Traffic vehicles         | Fuel car models + traffic editor                   | ☐      |
|  12 Bicycles & mixed traffic | Cycling actors or controlled bike model            | ☐      |

Legend : **✅ done**  |  **☐ todo**

---

## 🤝 Contributing

1. Fork ➜ branch ➜ PR.
2. Run `colcon test` locally; CI must pass.
3. Use conventional commits (`feat:`, `fix:`, `docs:`...).

---

## 📄 Licence

*Choose an OSI‑approved licence. MIT / Apache‑2.0 recommended.*
