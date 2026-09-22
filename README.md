# Real-Time Human–Robot Safety System

A ROS 2–based system for real-time human sensing, human and robot dynamics
estimation, collision-risk assessment, and risk-aware speed control of an Indy7
collaborative robot.

This repository is the project hub for the complete system. The implementation
is maintained in two focused repositories so that the perception and robot
control pipelines can be developed independently while sharing a common ROS 2
interface.

## System architecture

```text
Camera input
    │
    ▼
Human sensing and 3D pose estimation
    │
    ▼
Human dynamics and directional effective mass ─┐
                                                ├──► Collision-risk assessment
Indy7 state, dynamics, and effective mass ──────┘              │
                                                               ▼
                                                     Speed-scale command
                                                               │
                                                               ▼
                                                            Indy7
```

## Repositories

| Repository | Responsibility | Access |
| --- | --- | --- |
| [`hrc_ws`](https://github.com/ukhyeon/hrc_ws) | Human sensing, 3D pose estimation, human dynamics, visualization, and shared ROS 2 interfaces | Private while restricted third-party code is being replaced |
| [`indy_ws`](https://github.com/ukhyeon/indy_ws) | Indy7 communication, robot dynamics, collision-risk assessment, and robot speed control | Public |

`indy_ws` depends on the `hrc_interfaces` package provided by `hrc_ws`.

## Key features

- Camera-based human-body sensing and 3D pose estimation
- Human and robot directional effective-mass estimation
- Candidate collision-pair evaluation
- ISO/TS 15066–related collision-force assessment
- Risk-aware, smoothed robot speed scaling
- Runtime latency and processing-rate measurement
- Experiment analysis and visualization utilities

## Getting the project

The repository includes a
[`human-robot-safety.repos`](human-robot-safety.repos) manifest for
[`vcstool`](https://github.com/dirk-thomas/vcstool). It downloads both component
repositories into one local source directory without duplicating their Git
history.

> [!NOTE]
> The complete manifest currently requires collaborator access to `hrc_ws`.
> Public users can browse and clone `indy_ws`; the human-side repository will be
> opened after its restricted third-party code is replaced or separately
> cleared for redistribution.

```bash
sudo apt install python3-vcstool

git clone https://github.com/ukhyeon/human-robot-safety-system.git
cd human-robot-safety-system
mkdir -p src
vcs import src < human-robot-safety.repos
```

To inspect the checked-out versions:

```bash
vcs status src
```

To update both repositories:

```bash
vcs pull src
```

## Build order

The shared ROS 2 interfaces must be available before building the robot-side
workspace. Follow the detailed dependency and model setup instructions in each
component repository.

```bash
source /opt/ros/<ros-distro>/setup.bash

cd src/hrc_ws
colcon build --symlink-install
source install/setup.bash

cd ../indy_ws
colcon build --symlink-install
source install/setup.bash
```

Replace `<ros-distro>` with the ROS 2 distribution installed on the system.

## Project management

Use this hub repository for concerns that span the complete system:

- system architecture and integration documentation;
- compatible component versions and releases;
- end-to-end setup and demonstration instructions;
- system-level milestones, issues, and roadmap.

Use the component repositories for implementation-specific issues and changes.
Pin `version` entries in `human-robot-safety.repos` to a release tag or commit
when an experiment must be exactly reproducible.

## Requirements and safety

The complete demonstration requires external model assets, a compatible camera
setup, and access to an Indy7 robot controller. Some body-model assets have
separate licenses and are not distributed through this hub repository.

> [!CAUTION]
> Robot commands can cause physical motion. Verify the controller address,
> workspace clearance, emergency-stop operation, and speed limits before running
> hardware-facing nodes. Test changes at conservative speeds in a controlled
> environment.

## Current status

The system is under active development. Some component configuration paths and
hardware parameters still reflect the original development environment; review
the component README files before attempting reproduction on another machine.
