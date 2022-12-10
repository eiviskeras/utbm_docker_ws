FROM ros:melodic-ros-base

ENV PYTHON_VERSION 3.10.0
ENV CATKIN_WS /catkin_ws
ENV SICK_WS /sick_ws

# Set-up necessary Env vars for PyEnv
ENV PYENV_ROOT /root/.pyenv
ENV PATH $PYENV_ROOT/shims:$PYENV_ROOT/bin:$PATH

RUN apt-get update && apt-get install -q -y \
    vim curl wget make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev ca-certificates llvm libncurses5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev mecab-ipadic-utf8 \
    && rm -rf /var/lib/apt/lists/*

# Install pyenv
RUN set -ex \
    && curl https://pyenv.run | bash \
    && pyenv update \
    && pyenv install $PYTHON_VERSION \
    && pyenv global $PYTHON_VERSION \
    && pyenv rehash

RUN apt-get update && apt-get install -q -y \
 python-catkin-tools python-pip ros-melodic-nmea-navsat-driver ros-melodic-velodyne ros-melodic-gps-common \
 ros-melodic-camera-info-manager ros-melodic-image-transport ros-melodic-cv-bridge ros-melodic-image-pipeline \
 libdc1394-22-dev python-rosinstall-generator \
 && rm -rf /var/lib/apt/lists/*

#Install rosbags-convert (Used to convert from .bag to .db3) along with python 3.10 (Required) and dependencies for cameras in utbm-dataset
RUN pip install rosbags pyyml rospkg

#Setup ROS-workspaces
WORKDIR $SICK_WS/src
RUN git clone https://github.com/SICKAG/libsick_ldmrs.git && git clone -b melodic https://github.com/SICKAG/sick_ldmrs_laser.git
WORKDIR $SICK_WS
RUN /bin/bash -c '. /opt/ros/$ROS_DISTRO/setup.bash; catkin_make_isolated --install -DCMAKE_BUILD_TYPE=Release' 

WORKDIR $CATKIN_WS/src
RUN git clone https://github.com/epan-utbm/utbm_robocar_dataset && git clone https://github.com/ravich2-7183/bumblebee_xb3 && git clone https://github.com/ros-drivers/camera1394 && mv ./utbm_robocar_dataset/drivers/magellan_proflex500* $CATKIN_WS/src
WORKDIR $CATKIN_WS
RUN apt-get update && \
    rosdep update && \
    rosdep install -y \
      --from-paths \
        src/bumblebee_xb3 \
      --ignore-src \
    && rm -rf /var/lib/apt/lists/* \
    && catkin config \
      --extend /opt/ros/$ROS_DISTRO \
    && catkin build 
# Source ROS-Workspaces
RUN echo 'source /opt/ros/$ROS_DISTRO/setup.bash' >> /root/.bashrc \
  && echo 'source $CATKIN_WS/devel/setup.bash' >> /root/.bashrc \
  && echo 'source $SICK_WS/install_isolated/setup.bash --extend' >> /root/.bashrc
# Download utbm-dataset
RUN wget -P /catkin_ws/src/utbm_robocar_dataset/launch "https://drive.utbm.fr/s/f6mrskiZagqNo8G/download/utbm_robocar_dataset_20180713_noimage.bag"
