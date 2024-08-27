# F1tenth workspace

## Clone
```
git git@github.com:wette/wette_racecar_ws.git
cd wette_racecar_ws
git submodule init
git submodule update
```

## Build with colcon
```
colcon build
```

## Create a Map using google cartographer (works ok, but slam_toolbox works better)
### Map From Scratch
To create a new map from scratch, we need to run cartographer in map-creation-mode, and let the vehicle drive slowly through the racetrack using gap_follow.
```
ros2 launch vehicle_control vehicle_control_launch.py
ros2 launch cartographer_ros cartographer_new_map.launch.py
```

once, the map is good, it can be saved using commands
```
ros2 service call /finish_trajectory cartographer_ros_msgs/srv/FinishTrajectory "{trajectory_id: '0'}"
ros2 service call /write_state cartographer_ros_msgs/srv/WriteState "{filename: '/root/cartographer_ws/map.pbstream'}"
```
which creates the file /root/cartographer_ws/map.pbstream

from map.pbstream, a map.pgm and map.yaml can be created using
```
ros2 run cartographer_ros cartographer_pbstream_to_ros_map -pbstream_filename=/root/cartographer_ws/map.pbstream -map_filestem=map
```
The files /root/cartographer_ws/map.pgm and map.yaml should be created.

### Fixed Map
To use an existing map, cartographer needs to be run in localization-only mode:
```
ros2 launch vehicle_control vehicle_control_launch.py
ros2 launch cartographer_ros cartographer_existing_map.launch.py map_filename:=/root/cartographer_ws/map.pbstream
```



## Create a Map using slam_toolbox (works quite good)
Start Slam Toolbox with command
```
ros2 launch slam_toolbox online_async_launch.py slam_params_file:=/root/wette_racecar_ws/mapping_localization/mapper_params_online_async.yaml
```
Open rviz and add the SlamToobox Panel (Panels --> Add new panels). You can find a "save to file" button there.

### Localize in that map using slam_toolbox (has its own problems due to non-fixed map clashing with a fixed raceline)

```
ros2 launch slam_toolbox online_async_launch.py slam_params_file:=/root/wette_racecar_ws/mapping_localization/localization_params_online_async.yaml
```

### Localize in that map using AMCL Montecarlo Localization (from the nav2 stack - works quite good)
```
ros2 launch mapping_localization/localization_launch_amcl.py params_file:=mapping_localization/nav2_params.yaml map:=MindenCitySpeedway0408.yaml
```

If the map does not show up in rviz2, set Map->Topic->"Durability Policy" to "Transient Local" in the left rviz control pane.

## Helpful resources
- https://guni91.wordpress.com/2020/12/05/cartographer-ros2-installation/
- https://google-cartographer-ros.readthedocs.io/en/latest/algo_walkthrough.html
