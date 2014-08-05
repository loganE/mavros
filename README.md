MAVROS
======

MAVLink extendable communication node for ROS
with proxy for Ground Control Station (e.g. [QGroundControl][1]).

ROS API documentation moved to [wiki.ros.org][9].


Feutures
--------

  - Communication with autopilot via serial port, UDP or TCP (e.g. [ArduPilot][2])
  - Internal proxy for Ground Control Station (serial, UDP, TCP)
  - [mavlink\_ros][3] compatible ROS topics (Mavlink.msg)
  - Plugin system for ROS-MAVLink translation
  - Parameter manipulation tool
  - Waypoint manipulation tool


Limitations
-----------

Only for linux. Depends on [Boost library][4] >= 1.46 (hydro on 12.04).
Catkin build system required (tested with ROS Hydro Medusa and Indigo Igloo).

This package are dependent on ros-\*-mavlink build from [mavlink-gbp-release][7].
Since 2014-06-19 it exists in hydro and indigo package index (so you can install via rosdep).


Connection URL
--------------

*New in 0.7.0*. Connection now defined by URL,
you can use any supported type for FCU and GCS.

Supported schemas:

  - Serial: `/path/to/serial/device[:baudrate]`
  - Serial: `serial:///path/to/serial/device[:baudrate][?ids=sysid,compid]`
  - UDP: `udp://[bind_host[:port]]@[remote_host[:port]][/?ids=sysid,compid]`
  - TCP client: `tcp://[server_host][:port][/?ids=sysid,compid]`
  - TCP server: `tcp-l://[bind_port][:port][/?ids=sysid,compid]`

Note: ids from URL overrides ids given by parameters.


Programs
--------

### mavros\_node -- main communication node

Main node. Allow disable GCS proxy by setting empty URL.

Run example:

    rosrun mavros mavros_node _fcu_url:=/dev/ttyACM0:115200 _gcs_url:=tcp-l://


### gcs\_bridge -- additional UDP proxy

Allows you to add a channel for GCS.
For example if you need to connect one GCS for HIL and the second on the tablet.

Previous name: `ros_udp`.

Example (HIL & DroidPlanner):

    rosrun mavros mavros_node _gcs_url:='udp://:14556@hil-host:14551' &
    rosrun mavros gcs_bridge _gcs_url:='udp://@nexus7'


### mavparam -- parameter manipulation

Just see `--help`.

Examples:

    rosrun mavros mavparam dump /tmp/apm.param
    rosrun mavros mavparam load /tmp/apm2.param


### mavwp -- mission manipulation

See `--help`.

Examples:

    rosrun mavros mavwp show -p
    rosrun mavros dump /tmp/mission.txt


### mavsafety -- safety tool

See `--help`.

Examples:

    rosrun mavros mavsafety arm
    rosrun mavros mavsafety disarm


Installation
------------

Use `wstool` utility for installation. In your workspace do:

    wstool set -t src mavros --git https://github.com/vooon/mavros.git
    wstool update -t src
    rosdep install --from-paths src --ignore-src --rosdistro hydro -y

Then use regular `catkin_make` for build and install.
Notes: since v0.5 (and [#35][8]) mavlink submodule moved to special ROS 3rd party package [ros-\*-mavlink][7].


### Installing ros-\*-mavlink from source

If rosdep could not install mavlink library, you could install it from source:

    mkdir -p ~/ros_deps/src
    cd ~/ros_deps
    rosinstall_generator mavlink | tee rosinstall.yaml
    wstool init src ./rosinstall.yaml
    catkin_make_isolated --install-space $ROSINSTALL --install -DCMAKE_BUILD_TYPE=Release

$ROSINSTALL must be writable for user or you can add `sudo -s` to last command.
Or you could build debian package by pulling right bloom branch from [mavlink-gbp-release][7]
(common naming: `debian/<rosdistro>/<osdistro>/<package>`) using `dh binary`.


CI Statuses
-----------

  - ROS Hydro: [![Hydro build status](http://jenkins.ros.org/buildStatus/icon?job=devel-hydro-mavros)](http://jenkins.ros.org/job/devel-hydro-mavros/)
  - ROS Indigo: [![Indigo build status](http://jenkins.ros.org/buildStatus/icon?job=devel-indigo-mavros)](http://jenkins.ros.org/job/devel-indigo-mavros/)
  - Travis Hydro (Coverity Scan): [![Hydro scan status](https://travis-ci.org/vooon/mavros.svg?branch=coverity_scan)](https://travis-ci.org/vooon/mavros)


Links
-----

  - [MAVLink][5] -- communication protocol
  - [mavlink\_ros][3] -- original ROS node (few messages, no proxy)
  - [ArduPilot][2] -- tested autopilot APM:Plane (default command set)
  - [QGroundControl][1] -- tested ground control station for linux
  - [DroidPlanner][6] -- tested GCS for Android


[1]: http://qgroundcontrol.org/
[2]: http://ardupilot.com/
[3]: https://github.com/mavlink/mavlink_ros
[4]: http://www.boost.org/
[5]: http://mavlink.org/mavlink/start
[6]: https://github.com/arthurbenemann/droidplanner/
[7]: https://github.com/vooon/mavlink-gbp-release
[8]: https://github.com/vooon/mavros/issues/35
[9]: http://wiki.ros.org/mavros
