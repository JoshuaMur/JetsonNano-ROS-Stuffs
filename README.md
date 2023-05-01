# JetsonNano-ROS-Stuffs
Installation of ROS on Jetson Nano

### 1. Setup your sources.list
``` sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list' ```

### 2. Set up your keys
```
sudo apt install curl # if you haven't already installed curl
curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -

```

### 3. Configure keys. 
This step is to make sure our system can recognize this path is safe to download the documents, or the documents we download will be canceled.

```
sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654

```

### 4. Installation
 ``` sudo apt-get update ```
 
 ### 5. Install ROS Melodic
 ``` 
 sudo apt install ros-melodic-desktop-full
 ```
 
 Install if if you need 2D and 3D simulations, otherwise install with
 
 ``` 
 sudo apt install ros-melodic-desktop
 ```

### 6. Add Environment Setup
For the ROS environment variables to be automatically added to your bash session every time a new shell is launched:
```
echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

### 7. Dependencies for building packages
```
sudo apt install python-rosdep python-rosinstall python-rosinstall-generator python-wstool build-essential
```
Install rosdep
```
sudo apt install python-rosdep
```

Initialize rosdep
``` sudo rosdep init
rosdep update

```



