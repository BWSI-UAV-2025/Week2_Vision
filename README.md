# Configuring Pi 5 camera with ROS2

WARNING: Do not install or remove cameras when Pi is powered on, always shutdown and power off the Pi when moving the camera cables.

Remove any previously installed versions of libcamera:
```
sudo apt purge libcamera*
sudo apt purge ros-jazzy-libcamera*
rm -rf ~/camera_ws/build ~/camera_ws/install ~/camera_ws/log 
```

Install Dependencies:
```
sudo apt install -y libavdevice-dev libepoxy-dev
sudo apt install -y ros-jazzy-camera-info-manager
sudo apt install -y python3-colcon-meson
sudo apt install -y python3-rosdep
sudo apt install -y python3-colcon-common-extensions
sudo apt install -y libboost-dev
sudo apt install -y libgnutls28-dev openssl libtiff5-dev pybind11-dev
sudo apt install -y qtbase5-dev libqt5core5a libqt5gui5 libqt5widgets5
sudo apt install -y meson cmake
sudo apt install -y python3-yaml python3-ply
sudo apt install -y libglib2.0-dev libgstreamer-plugins-base1.0-dev
```

Build ROS package and libcamera:
```
# create workspace
mkdir -p ~/camera_ws/src
cd ~/camera_ws/src

# raspberrypi fork with support for newer camera modules
git clone https://github.com/raspberrypi/libcamera.git
# camera_ros repository
git clone https://github.com/christianrauch/camera_ros.git

# resolve binary dependencies and build workspace
source /opt/ros/$ROS_DISTRO/setup.bash
cd ~/camera_ws/
rosdep install -y --from-paths src --ignore-src --rosdistro $ROS_DISTRO --skip-keys=libcamera
colcon build
```

Run node:
```
ros2 run camera_ros camera_node
```

Visualize images using rqt, type "rqt" in a terminal, then go to "Plugins->Visualization->Image View", and then select the appropriate topic in the dropdown menu

#### Setup multiple cameras:

Modify /boot/firmware/config.txt by commenting out the line that says camera_auto_detect=1 and then adding the following lines below:
```
dtoverlay=imx708,cam0
dtoverlay=imx708,cam1
#camera_auto_detect=1
```

Then when running the ros node specify the camera index:
```
ros2 run camera_ros camera_node --ros-args -p camera:=0 --remap /camera/image_raw:=/camera_0/image_raw 
ros2 run camera_ros camera_node --ros-args -p camera:=1 --remap /camera/image_raw:=/camera_1/image_raw
```
 The above instructions were derived from: https://github.com/TanmayChhatbar/ros2_car/blob/feature/camera_setup/README_ELI5.md

# ROSBag Tutorial
- https://docs.ros.org/en/jazzy/Tutorials/Beginner-CLI-Tools/Recording-And-Playing-Back-Data/Recording-And-Playing-Back-Data.html

# Pi 5 40-pin header pinout
- https://vilros.com/pages/raspberry-pi-5-pinout?srsltid=AfmBOopVzVMbwz0I5-UpZ_XfvBQhRmFrYsZVaXcGirtb1_0MbhkkYi2G
  
# Additional resources
- More Computer Vision jupyter notebooks: https://github.com/jotaraul/jupyter-notebooks-for-computer-vision
- Cyrill Stachniss Youtube Channel: https://www.youtube.com/watch?v=nRVuLFQ_Bng
- MIT Visual Navigation Course (may be too advanced): https://vnav.mit.edu/ 
