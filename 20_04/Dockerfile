FROM ubuntu:20.04

RUN touch /root/.bashrc && \
        echo "alias c='clear'" >> /root/.bashrc && \
        echo "alias r='reset'" >> /root/.bashrc && \
        echo "alias h='history'" >> /root/.bashrc && \
        echo "alias python='python3'" >> /root/.bashrc && \
        echo "alias hg='history | grep'" >> /root/.bashrc && \
        echo "alias copy='time rsync -avP'" >> /root/.bashrc && \
        echo "alias copy_test='rsync -avP --dry-run --bwlimit=100'" >> /root/.bashrc && \
        echo "alias update_all='sudo apt update && sudo apt upgrade -y && sudo apt autoremove -y && sudo snap refresh'" >> /root/.bashrc && \
        sed -i 's|#force_color_prompt=yes|force_color_prompt=yes|g' /root/.bashrc && \
        sed -i 's|http://archive.|http://sg.archive.|g' /etc/apt/sources.list && \
        /bin/bash -c "source /root/.bashrc" && \
        apt-get update -y && \
        DEBIAN_FRONTEND=noninteractive apt-get upgrade -y

# ==================================================================
# essentials
# ------------------------------------------------------------------
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        build-essential \
        python3-dev \
        python3-pip \
        git \
        gdb \
        vim \
        wget \
        nano \
        less \
        gnupg \
        snapd \
        lsb-core \
        apt-utils

# ==================================================================
# ros
# ------------------------------------------------------------------
RUN echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list && \
        apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654 && \
        apt-get update -y && \
        DEBIAN_FRONTEND=noninteractive apt-get install -y ros-noetic-desktop-full && \
        echo "source /opt/ros/noetic/setup.bash" >> ~/.bashrc && \
        /bin/bash -c "source /root/.bashrc"

# ==================================================================
# gtsam
# ------------------------------------------------------------------
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        libboost-all-dev \
        libeigen3-dev \
        cmake \
        libtbb-dev \
        && \
        git clone https://github.com/borglab/gtsam.git && \
        mkdir -p gtsam/build && \
        cd gtsam/build && \
        cmake -DGTSAM_USE_SYSTEM_EIGEN=ON .. && \
        make check -j16 && \
        make install -j16 && \
        cd ../.. && \
        rm -rf gtsam

# ==================================================================
# flatbuffers
# ------------------------------------------------------------------
RUN git clone https://github.com/google/flatbuffers.git && \
        mkdir -p flatbuffers/build && \
        cd flatbuffers/build && \
        cmake .. && \
        make install -j16 && \
        cd ../.. && \
        rm -rf flatbuffers

# ==================================================================
# other dependencies
# ------------------------------------------------------------------
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        # ros-noetic-grid-map-ros \
        ros-noetic-ecl-threads \
        ros-noetic-velodyne-msgs \
        ros-noetic-geographic-msgs \
        ros-noetic-geodesy \
        # ros-noetic-timed-roslaunch \
        # libmrpt-dev \
        liblua5.2-dev \
        libgoogle-glog-dev \
        libceres-dev \
        libgsl-dev \
        libpcap-dev \
        dh-autoreconf \
        apt-transport-https \
        ca-certificates

# ==================================================================
# python and pip
# ------------------------------------------------------------------
RUN PIP_INSTALL="python3 -m pip --no-cache-dir install --upgrade" && \
        $PIP_INSTALL pip && \
        $PIP_INSTALL setuptools && \
        $PIP_INSTALL numpy && \
        $PIP_INSTALL scipy && \
        $PIP_INSTALL matplotlib && \
        $PIP_INSTALL jupyter && \
        $PIP_INSTALL tqdm && \
        $PIP_INSTALL pandas

# ==================================================================
# config & cleanup
# ------------------------------------------------------------------
RUN apt-get autoclean -y && \
        apt-get autoremove -y && \
        rm -rf /var/lib/apt/lists/* /tmp/* ~/*

WORKDIR /root/docker_mount/

ENTRYPOINT ["bash"]

# # Build image using (inside the directory with Dockerfile):
# docker build --tag="ubuntu_20.04:improved" .

# # Create container using (to be auto-removed on stop):
# # NOTE: See 'note' file for specific details
# docker run -d -it --rm -v <host_parent_folder>:/root/docker_mount/ --net=host --name ubuntu_20 ubuntu_20.04:improved

# # Star using container:
# docker start ubuntu_20

# # Run container using (in multiple terminals, as needed):
# docker exec -it ubuntu_20 bash

# # Stop using container:
# docker stop ubuntu_20
