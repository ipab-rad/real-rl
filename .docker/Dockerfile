ARG ROS_DISTRO=noetic
FROM ros:${ROS_DISTRO}-ros-core
MAINTAINER Aditya Kamireddypalli "adityakamireddypalli@gmail.com"

# set default DDS to Cyclone
ENV TERM xterm-256color

# install libfranka
RUN apt update && \
    apt install -y build-essential git libpoco-dev libeigen3-dev && \
    git clone --recursive --branch 0.8.0 https://github.com/frankaemika/libfranka.git  && \
    cd libfranka && \
    # git checkout 0.8.0 && \
    # git submodule update && \
    mkdir build && cd build && \
    cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_TESTS=OFF  .. && \
    cmake --build . -j$(nproc) && \
    cpack -G DEB && \
    ls && \
    sudo dpkg -i libfranka-*.deb

# set up workspace
ENV ROS_UNDERLAY /root/panda_ws
ENV WORKSPACE /root/workspace
WORKDIR $ROS_UNDERLAY

# install realsense
RUN apt-get update && apt-get install -y software-properties-common && \ 
    rm -rf /var/lib/apt/lists/* && \ 
    apt-key adv --keyserver keyserver.ubuntu.com --recv-key F6E65AC044F831AC80A06380C8B3A55A6F3EFCDE || apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-key F6E65AC044F831AC80A06380C8B3A55A6F3EFCDE && \
    add-apt-repository "deb https://librealsense.intel.com/Debian/apt-repo $(lsb_release -cs) main" -u && \
    apt-get install -y librealsense2-dkms librealsense2-utils librealsense2-dev librealsense2-dbg

# install ros dependencies
RUN . /opt/ros/${ROS_DISTRO}/setup.sh && \
    apt-get -q update && \
    apt-get install -y python3-catkin-tools python3-pip  && \
    apt install -y python3-rosdep python3-rosinstall \
    python3-rosinstall-generator python3-wstool build-essential \
    python-is-python3

RUN DEBIAN_FRONTEND=noninteractive \
    apt-get install -y ros-${ROS_DISTRO}-rqt ros-${ROS_DISTRO}-rqt-common-plugins

RUN DEBIAN_FRONTEND=noninteractive \
    apt install -y spacenavd && \
    apt install -y ros-${ROS_DISTRO}-spacenav-node && \
    apt install -y ros-${ROS_DISTRO}-py-trees-msgs \
    ros-${ROS_DISTRO}-py-trees-ros \
    ros-${ROS_DISTRO}-rqt-py-trees && \
    apt-get install -y clangd-12 && \
    update-alternatives --install /usr/bin/clangd clangd /usr/bin/clangd-12 100 && \
    python3 -m pip install absl-py scipy networkx py_trees&& \
    # Clear apt-cache to reduce image size
    rm -rf /var/lib/apt/lists/*

# installing drake
RUN DEBIAN_FRONTEND=noninteractive \
    apt-get -q update && \
    apt-get install -y --no-install-recommends ca-certificates gnupg \
    lsb-release wget && \
    wget -qO- https://drake-apt.csail.mit.edu/drake.asc | gpg --dearmor - \
    | tee /etc/apt/trusted.gpg.d/drake.gpg >/dev/null && \
    echo "deb [arch=amd64] https://drake-apt.csail.mit.edu/$(lsb_release -cs) $(lsb_release -cs) main" \
    | tee /etc/apt/sources.list.d/drake.list >/dev/null && \
    apt-get update && \
    apt-get install -y --no-install-recommends drake-dev && \
    export PATH="/opt/drake/bin${PATH:+:${PATH}}" && \
    export PYTHONPATH="/opt/drake/lib/python$(python3 -c 'import sys; print("{0}.{1}".format(*sys.version_info))')/site-packages${PYTHONPATH:+:${PYTHONPATH}}"

# python management
RUN git clone https://github.com/pyenv/pyenv.git /pyenv
ENV PYENV_ROOT /pyenv
RUN /pyenv/bin/pyenv install 3.10.4


WORKDIR $WORKSPACE
COPY . .
WORKDIR ${WORKSPACE}/src/reasoning/serl/serl_launcher/

# RUN command -v /pyenv/bin/pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH" && \
RUN echo $(ls)

RUN eval "$(/pyenv/bin/pyenv init -)" && \
    /pyenv/bin/pyenv local 3.10.4 && \
    python -m pip install --upgrade pip && \
    pip install -e . && \
    pip install -r requirements.txt

WORKDIR ${WORKSPACE}

# setting up the workspace
RUN apt-get update && \
    rosdep init && \
    rosdep update && \
    rosdep install --from-paths src --ignore-src -r -y --rosdistro ${ROS_DISTRO}


RUN rm -rf ${ROS_UNDERLAY}

# create entrypoint
COPY .docker/ros_entrypoint.sh /sbin/ros_entrypoint.sh
RUN echo "source /sbin/ros_entrypoint.sh" >> ~/.bashrc
ENTRYPOINT ["/sbin/ros_entrypoint.sh"]
