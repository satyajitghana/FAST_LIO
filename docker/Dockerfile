FROM ros:noetic-ros-core-focal

# install bootstrap tools
RUN apt-get update && apt-get install --no-install-recommends -y \
    build-essential \
    python3-rosdep \
    python3-rosinstall \
    python3-vcstools \
    && rm -rf /var/lib/apt/lists/*

# bootstrap rosdep
RUN rosdep init && \
    rosdep update --rosdistro $ROS_DISTRO

# install ros packages
RUN apt-get update && apt-get install -y --no-install-recommends \
    ros-noetic-ros-base \
    && rm -rf /var/lib/apt/lists/*

# r2live stuff
RUN apt-get update && apt-get install -y --no-install-recommends \
    ros-noetic-cv-bridge ros-noetic-tf ros-noetic-message-filters ros-noetic-image-transport \ 
    ros-noetic-pcl-conversions ros-noetic-pcl-ros ros-noetic-perception ros-noetic-rviz

# build tools and libraries
RUN apt-get update && apt-get install -y --no-install-recommends \
    libgoogle-glog-dev libatlas-base-dev libeigen3-dev cmake \
    curl wget vim build-essential unzip mesa-utils libgl1-mesa-glx

# livox_ros_driver

WORKDIR /opt/livox_build

RUN wget -O livox.zip https://github.com/Livox-SDK/Livox-SDK/archive/refs/tags/v2.3.0.zip && unzip livox.zip

RUN cd Livox-SDK-2.3.0/build && cmake .. && make && make install

RUN wget -O livox_ros_driver.zip https://github.com/Livox-SDK/livox_ros_driver/archive/refs/tags/v2.6.0.zip && mkdir -p ws_livox/src && unzip livox_ros_driver.zip -d ws_livox/src

RUN /bin/bash -c '. /opt/ros/noetic/setup.bash; cd ws_livox && catkin_make'

# fast lio

RUN echo "source /opt/ros/noetic/setup.sh\nsource /opt/livox_build/ws_livox/devel/setup.sh" >> /opt/bashrc

WORKDIR /opt/catkin_ws/src/FAST_LIO

ADD . .

RUN cd ../.. && /bin/bash -c '. /opt/bashrc; catkin_make'

RUN cat /opt/catkin_ws/devel/setup.sh >> /opt/ros/noetic/setup.bash
