# AGV V2.1 patch notes

## Fix for gazebo_ros2_control parameter parser error

The launch file now removes XML comments from the expanded URDF before publishing
`robot_description`, and explicitly marks the parameter as a string. This avoids
the Humble `gazebo_ros2_control` parser failure triggered by YAML-special text in
XML comments (for example `name: value` and `###`). No links, joints, geometry,
sensor plugins, control interfaces, or controller names are changed by this fix.

Replace the package source in your workspace with this archive, then rebuild:

```bash
cd ~/Downloads/Test/v2.1/agv_v2_fixed
rm -rf build install log
source /opt/ros/humble/setup.bash
colcon build --symlink-install --packages-select articubot_one
source install/setup.bash
ros2 launch articubot_one launch_sim.launch.py
```
