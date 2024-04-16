# Mocap ROS2

MOCAP (NATNET protocol) position publisher in ROS2.

Connects to an IP and port running Motive and publishes all the rigid bodies set in the software, including their IDs. Edit the parameter file if required in the `config` folder.

The package is based on the NatNet SDK sample: [NatNet SDK](https://optitrack.com/support/downloads/developer-tools.html#natnet-sdk).

## Overview

The `mocap_ros2` package provides a ROS2 node for publishing position data using the MOCAP (Motion Capture) NATNET protocol. It connects to a specified IP and port running OptiTrack Motive, publishing position information for all rigid bodies configured in the Motive software, along with their respective IDs.

## Prerequisites

- ROS2 (Robot Operating System 2)
- OptiTrack Motive with configured rigid bodies

## Installation

1. Clone this repository to your ROS2 workspace:

   ```bash
   https://github.com/Massiccio1/mocap_ros2.git
   ```

2. Build the package:

   ```bash
   colcon build --symlink-install
   ```

3. Edit the parameter file in the `config` folder to set the correct IP and port for your OptiTrack Motive setup: mocap_ros2/mocap_client/config/params.yaml

   ```bash
   # Dummy parameters for testing
    dummy_send: false #true. Set it to false to connect to the mocap.
    send_rate: 360
    number_of_bodies: 1 # Same values will be sent for all the bodies
    dummy_x: 0.0
    dummy_y: 0.0
    dummy_z: 1.0
    dummy_qx: 0.0
    dummy_qy: 0.0
    dummy_qz: 0.0
    dummy_qw: 1.0
    # Connection parametrs
    server_address: "192.168.1.33" #"192.168.4.31"
    multicast_address: "239.255.42.99" #default value : 239.255.42.99
    connection_type: 0 #default value: 0. 0 = multicast, 1 = unicast.
    server_command_port: 1510 #default value: 1510
    server_data_port: 1511 #default value: 1511
    pub_topic: "/mocap/rigid_bodies"
    record: false #true. Record flag for Motive software
    record_file_name: ""

   ```

## Usage

1. Ensure OptiTrack Motive is running with configured rigid bodies.
2. Launch the `mocap_ros2` node:

   ```bash
   ros2 launch mocap_client mocap.launch.py
   ```

3. The node will connect to Motive, publish position data for all rigid bodies, and output their respective IDs.

## Configuration

Edit the parameters in the `config/mocap_ros2_params.yaml` file to customize IP, port, and other settings as needed.

## Python

After building adn sourcing the source code add the following includes

```
from mocap_interfaces.msg import RigidBodies, RigidBody
```

Subscribe to the topic

```
self.optitrack_subscriber= self.create_subscription(
            RigidBodies, "/mocap/rigid_bodies", self.optitrack_pose_callback, qos.qos_profile_sensor_data)

The node publishes Ros2 messages with the following structures
```

RigidBodies:

```
RigidBody[] rigid_bodies
```

RigidBody:

```
int32 id
bool tracking
float64 mean_error
geometry_msgs/PoseStamped pose_stamped

```

To access data as `geometry_msgs/PoseStamped` with a given `ID` use the following code snippet:

```
    def optitrack_pose_callback(self, msg):
        """Callback function for optitrack pose"""
        for rb in msg.rigid_bodies:
            if rb.id == ID:
                self.pose = rb.pose_stamped
```

## License

This software is released under the MIT License. See the [LICENSE](LICENSE) file for details.

## Author

- [Hannibal Paul](https://github.com/hpaul360)

## Acknowledgments

The package is based on the NatNet SDK sample provided by OptiTrack.
