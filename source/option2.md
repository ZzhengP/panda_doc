Option 2: Install from source
==============================
The following guide is based on Franka Control Interface documentation. ([link](https://frankaemika.github.io/docs/index.html)).


This you want build the packages from source, uninstall first the existing installation of ``` libfranka ``` and ``` franka_ros ``` to avoid conflicts:
```sh
   sudo apt remove "*libfranka*"
```
## Building libfranka

Install first the following dependencies packages from Ubuntu:
```sh
sudo apt install build-essential cmake git libpoco-dev libeigen3-dev
```
Then, download the source code of ``` libfranka ```from [Github](https://github.com/frankaemika/libfranka):
```sh
cd ~
git clone --recursive https://github.com/frankaemika/libfranka
cd libfranka
```
In the source directory, create a build directory and run CMake:
```sh
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
cmake --build .
```
You now need to add the path of this library to you system path so that libfranka can be found at runtime. To do this, when in the build/ directory:
```sh
pwd
```
We then add this path to the end of our ``` ~/.bashrc ``` file, such an example is:
```sh
nano ~/.bashrc

# add the following line to the end of the file
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/home/<username>/libfranka/build"
# now save and exit the file

source ~/.bashrc
```

## Building the ROS package
Create a catkin workspace for your ``` ros ``` projetc.
```sh
cd ~
mkdir -p catkin_ws/src
cd catkin_ws
source /opt/ros/kinetic/setup.sh
catkin_init_workspace src
```
Then clone the ``` franka_ros ``` from [Github](https://github.com/frankaemika/franka_ros):
```sh
git clone --recursive https://github.com/frankaemika/franka_ros src/franka_ros
```
Install any missing dependencies and build the packages:
```sh
rosdep install --from-paths src --ignore-src --rosdistro melodic -y --skip-keys libfranka
catkin_make -DCMAKE_BUILD_TYPE=Release -DFranka_DIR:PATH=~/libfranka/build
source devel/setup.sh
```  

## Useful Package for MPC_ros project
qpOASES is required for solving quadratic optimization problem. A [ros-wrapper](https://github.com/kuka-isir/qpOASES) version is preferred here to use directly is ROS.

## Setting up the real-time Kernel
In order to control your robot using ``` libfranka ```, the controller program on the workstation PC must run with real-time priority under a ```PREEMPT_RT ``` kernel . This section describes the procedure of patching a kernel to support ```PREEMPT_RT ``` and creating an installation package.
***
**note**

NVIDIA binary drivers are not supported on PREEMPT_RT kernels.

***
- First, install the necessary dependencies:
  ```sh
  apt-get install build-essential bc curl ca-certificates fakeroot gnupg2 libssl-dev lsb-release libelf-dev bison flex
  ```
- Download the version of the real-time kernel and associated patch, the version of the real-  time kernel and the patch must be the same. Here, for ubuntu 18, i choose version 5.4.10
  ```sh
  wget https://mirrors.edge.kernel.org/pub/linux/kernel/v5.x/linux-5.4.19.tar.xz
  wget https://mirrors.edge.kernel.org/pub/linux/kernel/projects/rt/5.4/patch-5.4.19-rt11.patch.xz
  ```
- Unzip
  ```sh
  xz -cd linux-5.4.19.tar.xz | tar xvf -
  cd linux-5.4.19
  xzcat ../patch-5.4.19-rt11.patch.xz | patch -p1
  ```
- Configure your real-time kernel:
  ```sh
  cp /boot/config-5.3.0-40-generic .config
  make oldconfig
  Preemption Model

  1. No Forced Preemption (Server) (PREEMPT_NONE)

  2. Voluntary Kernel Preemption (Desktop) (PREEMPT_VOLUNTARY)

  3. Preemptible Kernel (Low-Latency Desktop) (PREEMPT__LL) (NEW)

  4. Preemptible Kernel (Basic RT) (PREEMPT_RTB) (NEW)

  > 5. Fully Preemptible Kernel (RT) (PREEMPT_RT_FULL) (NEW)
  ```

If preemptinble not found, try it in General Setting
- Compile:
```sh
   make -j20
   sudo make modules_install -j20
   sudo make install -j20
```

- Verifying the new kernel
Restart your system. The Grub boot menu should now allow you to choose your newly installed kernel. To see which one is currently being used, see the output of the ``` uname -a ``` command. It should contain the string ``` PREEMPT RT ``` and the version number you chose.

- Allow a user to set real-time permissions for its processes
After the ``` PREEMPT_RT ``` kernel is installed and running, add a group named realtime and add the user controlling your robot to this group:
```sh
sudo addgroup realtime
sudo usermod -a -G realtime $(whoami)
```
***
**tips**

Links for real-time kernel installation:
- <https://stackoverflow.com/questions/51669724/install-rt-linux-patch-for-ubuntu>
- <https://blog.csdn.net/lsky380/article/details/90769058>
- <https://blog.csdn.net/qq_33445388/article/details/116034745>
***
