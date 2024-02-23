# F1tenth workspace

## Clone
```
git clone git@github.com:Autonomous-Racing-Labs/f1tenth_racecar_ws.git
cd f1tenth_racecar_ws
git submodule init
git submodule update
```

## Build with colcon
```
colcon build
```

## Create a Map
### Map From Scratch
To create a new map from scratch, we need to run cartographer in map-creation-mode, and let the vehicle drive slowly through the racetrack using gap_follow.
```
ros2 launch vehicle_control vehicle_control_launch.py
ros2 launch cartographer_ros cartographer.launch.py use_existing_map:=False
```

once, the map is good, it can be saved using commands
```
ros2 service call /finish_trajectory cartographer_ros_msgs/srv/FinishTrajectory "{trajectory_id: '0'}"
ros2 service call /write_state cartographer_ros_msgs/srv/WriteState "{filename: '/root/cartographer_ws/map.pbstream'}"
```
which creates the file /root/cartographer_ws/map.pbstream

from map.pbstream, a map.pgmm and map.yaml can be created using
```
ros2 run cartographer_ros cartographer_pbstream_to_ros_map -pbstream_filename=/root/cartographer_ws/map.pbstream -map_filestem=map
```
The files /root/cartographer_ws/map.pgmm and map.yaml should be created.

### Fixed Map
To use an existing map, cartographer needs to be run in localization-only mode:
```
ros2 launch vehicle_control vehicle_control_launch.py
ros2 launch cartographer_ros cartographer.launch.py use_existing_map:=True 
```

### TODO: Use existing map and detect changes
tbd.