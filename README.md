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
### The Drone Simulation 
```
export ROS_VERSION=melodic
mkdir drone_acrobatics_ws
cd drone_acrobatics_ws
export CATKIN_WS=./catkin_dda
mkdir -p $CATKIN_WS/src
cd $CATKIN_WS
catkin init
catkin config --extend /opt/ros/$ROS_VERSION
catkin config --merge-devel
catkin config --cmake-args -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_FLAGS=-fdiagnostics-color
cd src

git clone https://github.com/uzh-rpg/deep_drone_acrobatics.git
vcs-import < deep_drone_acrobatics/dependencies.yaml

#install extra dependencies (might need more depending on your OS)
sudo apt-get install libqglviewer-dev-qt5

```
Before continuing, make sure that your protobuf compiler version is 3.0.0. To check this out, type in a terminal protoc --version. If This is not the case, then check out this guide on how to do it.

Note that building the VINS-Mono package requires to install the Ceres Solver. You can find installation instructions [here](https://github.com/HKUST-Aerial-Robotics/VINS-Mono) or [here](http://ceres-solver.org/installation.html).


```
# Build and re-source the workspace
catkin build
. ../devel/setup.bash

# Create your learning environment
cd deep_drone_acrobatics
conda create --name drone_flow python=3.6
conda activate drone_flow
# Install (in an hacky way) python requirements
pip install -r pip_requirements.txt
conda install --file conda_requirements.txt

```

### Power Loop
Once you have installed the dependencies, you will be able to fly in simulation with our pre-trained checkpoint. You don't need necessarely need a GPU for execution. Note that if the network can't run at least at 15Hz, you won't be able to fly successfully.

Lauch a simulation! Open a terminal and type:

```
cd drone_acrobatics_ws
source catkin_dda/devel/setup.bash
roslaunch fpv_aggressive_trajectories simulation.launch

```
Run the Network in an other terminal:
```
cd
cd drone_acrobatics_ws
. ./catkin_dda/devel/setup.bash
conda activate drone_flow
python test_trajectories.py --settings_file=config/test_settings.yaml

```

### Train your own acrobatic controller
You can use the following commands to generate data in simulation and train your model on it. The trained checkpoint can then be used to control a physical platform (if you have one!).

#### Generate Data
Launch the simulation in one terminal
```
cd drone_acrobatics_ws
source catkin_dda/devel/setup.bash
roslaunch fpv_aggressive_trajectories simulation.launch

```
Launch data collection (with dagger) in an other terminal
```
cd
cd drone_acrobatics_ws
. ./catkin_dda/devel/setup.bash
conda activate drone_flow
python iterative_learning_trajectories.py --settings_file=config/dagger_settings.yaml
```

It is possible to change parameters (number of rollouts, history length, etc. ) in the file [dagger_settings.yaml](https://github.com/uzh-rpg/deep_drone_acrobatics/blob/master/controller_learning/config/dagger_settings.yaml). Keep in mind that if you change the network (for example by changing the history length), you will need to adapt the file [test_settings.yaml](https://github.com/uzh-rpg/deep_drone_acrobatics/blob/master/controller_learning/config/test_settings.yaml) for compatibility.

###Train the Network
If you want to train the network on data already collected (for example to do some parameter tuning) use the following commands. Make sure to adapt the settings file to your configuration.

```
cd
cd drone_acrobatics_ws
. ./catkin_dda/devel/setup.bash
conda activate drone_flow
python train.py --settings_file=config/train_settings.yaml

```

###Test the Network
To test the network you trained, adapt the [test_settings.yaml](https://github.com/uzh-rpg/deep_drone_acrobatics/blob/master/controller_learning/config/test_settings.yaml) with the new checkpoint path. Then follow the instruction to test!

Lauch a simulation! Open a terminal and type:
```
cd drone_acrobatics_ws
source catkin_dda/devel/setup.bash
roslaunch fpv_aggressive_trajectories simulation.launch

```

Run the Network in an other terminal:
```
cd
cd drone_acrobatics_ws
. ./catkin_dda/devel/setup.bash
conda activate drone_flow
python test_trajectories.py --settings_file=config/test_settings.yaml

```
