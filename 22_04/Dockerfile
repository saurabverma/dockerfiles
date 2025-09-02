FROM ubuntu:22.04

ARG DEBIAN_FRONTEND=noninteractive

RUN touch /root/.bashrc && \
        echo "alias c='clear'" >> /root/.bashrc && \
        echo "alias r='reset'" >> /root/.bashrc && \
        echo "alias h='history'" >> /root/.bashrc && \
        echo "alias python='python3'" >> /root/.bashrc && \
        echo "alias hg='history | grep'" >> /root/.bashrc && \
        echo "alias copy='time rsync -avP'" >> /root/.bashrc && \
        echo "alias copy_test='rsync -avP --dry-run --bwlimit=100'" >> /root/.bashrc && \
        echo "alias update_all='apt update && apt upgrade -y && apt autoremove -y && snap refresh'" >> /root/.bashrc && \
        sed -i 's|#force_color_prompt=yes|force_color_prompt=yes|g' /root/.bashrc && \
        sed -i 's|http://archive.|http://sg.archive.|g' /etc/apt/sources.list && \
        /bin/bash -c "source /root/.bashrc" && \
        apt update -y && \
        apt upgrade -y

# ==================================================================
# essentials
# ------------------------------------------------------------------
RUN apt install -y --no-install-recommends \
        build-essential \
        python3-dev \
        python3-pip \
        python3-venv \
        python-is-python3 \
        unzip \
        git \
        gdb \
        vim \
        curl \
        wget \
        nano \
        less \
        gnupg \
        snapd \
        lsb-core \
        apt-utils

# # ==================================================================
# # ros
# # ------------------------------------------------------------------
RUN apt update && apt install -y locales && \
        locale-gen en_US en_US.UTF-8 && update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8 && export LANG=en_US.UTF-8 && \
        apt install -y software-properties-common && add-apt-repository universe -y && apt update && \
        export ROS_APT_SOURCE_VERSION=$(curl -s https://api.github.com/repos/ros-infrastructure/ros-apt-source/releases/latest | grep -F "tag_name" | awk -F\" '{print $4}') && \
        curl -L -o /tmp/ros2-apt-source.deb "https://github.com/ros-infrastructure/ros-apt-source/releases/download/${ROS_APT_SOURCE_VERSION}/ros2-apt-source_${ROS_APT_SOURCE_VERSION}.$(. /etc/os-release && echo $VERSION_CODENAME)_all.deb" && \
        dpkg -i /tmp/ros2-apt-source.deb && apt update && apt upgrade -y && \
        apt install -y ros-humble-desktop ros-dev-tools && \
        source /opt/ros/humble/setup.bash

# # ==================================================================
# # other dependencies
# # ------------------------------------------------------------------
# RUN apt install -y --no-install-recommends \
#         # ros-noetic-grid-map-ros \
#         ros-noetic-ecl-threads \
#         ros-noetic-velodyne-msgs \
#         ros-noetic-geographic-msgs \
#         ros-noetic-geodesy \
#         # ros-noetic-timed-roslaunch \
#         # libmrpt-dev \
#         liblua5.2-dev \
#         libgoogle-glog-dev \
#         libceres-dev \
#         libgsl-dev \
#         libpcap-dev \
#         dh-autoreconf \
#         apt-transport-https \
#         ca-certificates

# ==================================================================
# python and pip
# ------------------------------------------------------------------
RUN PIP_INSTALL="python -m pip --no-cache-dir install --upgrade" && \
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
RUN apt autoclean -y && \
        apt autoremove -y && \
        rm -rf /var/lib/apt/lists/* /tmp/* ~/*

WORKDIR /workspace/

ENTRYPOINT ["bash"]

# # Build image using (inside the directory with Dockerfile):
# docker build --tag="ubuntu_22.04:improved" .

# # Create container using (to be auto-removed on stop):
# # NOTE: See 'note' file for specific details
# docker run -d -it --rm -v <host_parent_folder>:/workspace/ --net=host --name ubuntu_22 ubuntu_22.04:improved

# # Star using container:
# docker start ubuntu_22

# # Run container using (in multiple terminals, as needed):
# docker exec -it ubuntu_22 bash

# # Stop using container:
# docker stop ubuntu_22
