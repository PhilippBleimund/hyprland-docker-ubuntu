# Copyright 2025 Mark Mandel
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# Ubuntu 24.04.2 LTS -> noble
# changes to main version:
# libinput >= 1.26.0 is needet but apt only provides 1.25.0
#   needs libgtk-3-dev
# hyprlang needs g++14
# Hyprland needs
#   cmake >= 2.30
#   wayland-server>=1.22.90
#     compile wayland --> needs graphviz
#   wayland-protocols>=1.41
# libxcb-errors-dev does not exist for apt.

FROM ubuntu:noble


RUN apt update && \
	apt install -y wget curl meson wget build-essential ninja-build cmake-extras cmake gettext gettext-base fontconfig libfontconfig-dev libffi-dev libxml2-dev libdrm-dev libxkbcommon-x11-dev \ 
		libxkbregistry-dev libxkbcommon-dev libpixman-1-dev libudev-dev libseat-dev seatd libxcb-dri3-dev libegl-dev libgles2 libegl1-mesa-dev glslang-tools \
    libinput-bin libinput-dev libgtk-3-dev g++-14 gcc-14\ 
		libxcb-composite0-dev libavutil-dev libavcodec-dev libavformat-dev libxcb-ewmh2 libxcb-ewmh-dev libxcb-present-dev libxcb-icccm4-dev libxcb-render-util0-dev libxcb-res0-dev libxcb-xinput-dev libtomlplusplus3 \
		git libpugixml-dev \
		libwayland-dev wayland-protocols libgbm-dev libdisplay-info-dev hwdata libzip-dev libcairo2-dev librsvg2-dev libtomlplusplus-dev \
		libjxl-dev libmagic-dev libxcursor-dev libre2-dev \
		libsdbus-c++-dev libpam0g-dev libglvnd-dev libglvnd-core-dev file \
		qt6-base-dev libspa-0.2-dev libpipewire-0.3-dev \
		qt6-wayland-dev qt6-declarative-dev qt6-declarative-private-dev qt6-wayland-private-dev libspng-dev 

# build dependencies
RUN git clone https://gitlab.freedesktop.org/libinput/libinput/ && \
  cd libinput && git checkout 1.28.0 && \
  meson setup --prefix=/usr builddir/ && \
  ninja -C builddir/ && \
  ninja -C builddir/ install 

RUN mkdir cmake && cd cmake && \ 
  wget https://github.com/Kitware/CMake/releases/download/v4.0.2/cmake-4.0.2-linux-x86_64.sh && \
  chmod +x cmake-4.0.2-linux-x86_64.sh && \
  mkdir -p /opt/cmake-4.0.2 && \
  ./cmake-4.0.2-linux-x86_64.sh --skip-license --prefix=/opt/cmake-4.0.2

ENV PATH="/opt/cmake-4.0.2/bin:$PATH" 

RUN git clone https://gitlab.freedesktop.org/wayland/wayland.git && \
    cd wayland && \
    git checkout 1.23.1 && \
    meson build/ --prefix=/usr -Ddocumentation=false && \
    ninja -C build/ install 

RUN git clone https://gitlab.freedesktop.org/wayland/wayland-protocols.git && \
    cd wayland-protocols && \
    git checkout 1.44 && \
    meson build/ --prefix=/usr && \
    ninja -C build/ install 

# hyprland dependencies
RUN 	git clone https://github.com/hyprwm/hyprwayland-scanner && \
	cd hyprwayland-scanner && git checkout v0.4.4 && \
	cmake -DCMAKE_INSTALL_PREFIX=/usr -B build && \
	cmake --build build -j `nproc` && \
	cmake --install build 

RUN 	git clone https://github.com/hyprwm/hyprutils.git && \
	cd hyprutils && git checkout v0.7.0 && \
	cmake --no-warn-unused-cli -DCMAKE_BUILD_TYPE:STRING=Release -DCMAKE_INSTALL_PREFIX:PATH=/usr -S . -B ./build && \
	cmake --build ./build --config Release --target all -j`nproc 2>/dev/null || getconf NPROCESSORS_CONF` && \
	cmake --install build

RUN  	git clone https://github.com/hyprwm/aquamarine && \
	cd aquamarine && git checkout v0.8.0 && \
	cmake --no-warn-unused-cli -DCMAKE_BUILD_TYPE:STRING=Release -DCMAKE_INSTALL_PREFIX:PATH=/usr -S . -B ./build && \
	cmake --build ./build --config Release --target all -j`nproc 2>/dev/null || getconf _NPROCESSORS_CONF` && \
	cmake --install build

RUN 	git clone https://github.com/hyprwm/hyprlang && \
	cd hyprlang && git checkout v0.6.1 && \
	CC=gcc-14 CXX=g++-14 cmake --no-warn-unused-cli -DCMAKE_BUILD_TYPE:STRING=Release -DCMAKE_INSTALL_PREFIX:PATH=/usr -S . -B ./build && \
	CC=gcc-14 CXX=g++-14 cmake --build ./build --config Release --target hyprlang -j`nproc 2>/dev/null || getconf _NPROCESSORS_CONF` && \
	CC=gcc-14 CXX=g++-14 cmake --install ./build

RUN 	git clone https://github.com/hyprwm/hyprcursor && \
	cd hyprcursor && git checkout v0.1.12 && \
	CC=gcc-14 CXX=g++-14 cmake --no-warn-unused-cli -DCMAKE_BUILD_TYPE:STRING=Release -DCMAKE_INSTALL_PREFIX:PATH=/usr -S . -B ./build && \
	CC=gcc-14 CXX=g++-14 cmake --build ./build --config Release --target all -j`nproc 2>/dev/null || getconf _NPROCESSORS_CONF` && \
	CC=gcc-14 CXX=g++-14 cmake --install build

RUN	git clone https://github.com/hyprwm/hyprgraphics && \
	cd hyprgraphics/ && git checkout v0.1.3 && \
	CC=gcc-14 CXX=g++-14 cmake --no-warn-unused-cli -DCMAKE_BUILD_TYPE:STRING=Release -DCMAKE_INSTALL_PREFIX:PATH=/usr -S . -B ./build && \
	CC=gcc-14 CXX=g++-14 cmake --build ./build --config Release --target all -j`nproc 2>/dev/null || getconf NPROCESSORS_CONF` && \
	CC=gcc-14 CXX=g++-14 cmake --install build

RUN 	git clone --recursive https://github.com/hyprwm/Hyprland && \
	cd Hyprland && git checkout v0.48.1 && \
	make all && make install

RUN 	cd /Hyprland/subprojects/hyprland-protocols && \
	meson setup build && \
	ninja -C build && \
	ninja -C build install

# Hyprland Utils

RUN 	git clone https://github.com/hyprwm/hyprlock && \
	cd hyprlock && git checkout v0.8.1 && \
	cmake --no-warn-unused-cli -DCMAKE_BUILD_TYPE:STRING=Release -S . -B ./build && \
	cmake --build ./build --config Release --target hyprlock -j`nproc 2>/dev/null || getconf _NPROCESSORS_CONF` && \
	cmake --install build

RUN 	git clone https://github.com/hyprwm/hyprpaper && \
	cd hyprpaper && git checkout v0.7.4 && \
	cmake --no-warn-unused-cli -DCMAKE_BUILD_TYPE:STRING=Release -DCMAKE_INSTALL_PREFIX:PATH=/usr -S . -B ./build && \
	cmake --build ./build --config Release --target hyprpaper -j`nproc 2>/dev/null || getconf _NPROCESSORS_CONF` && \
	cmake --install ./build

RUN 	git clone https://github.com/hyprwm/hypridle && \
	cd hypridle && git checkout v0.1.6 && \
	cmake --no-warn-unused-cli -DCMAKE_BUILD_TYPE:STRING=Release -S . -B ./build && \
	cmake --build ./build --config Release --target hypridle -j`nproc 2>/dev/null || getconf _NPROCESSORS_CONF` && \
	cmake --install build

RUN 	git clone --recursive https://github.com/hyprwm/xdg-desktop-portal-hyprland && \
	cd xdg-desktop-portal-hyprland && git checkout v1.3.9 && \
	cmake -DCMAKE_INSTALL_LIBEXECDIR=/usr/lib -DCMAKE_INSTALL_PREFIX=/usr -B build && \
	cmake --build build && \
	cmake --install build

RUN git clone https://github.com/hyprwm/hyprland-qtutils && \
    cd hyprland-qtutils && git checkout v0.1.3 && \
    cmake --no-warn-unused-cli -DCMAKE_BUILD_TYPE:STRING=Release -DCMAKE_INSTALL_PREFIX:PATH=/usr -S . -B ./build && \
    cmake --build ./build --config Release --target all -j`nproc 2>/dev/null || getconf NPROCESSORS_CONF` && \
    cmake --install build

RUN apt install -y libpolkit-agent-1-dev libpolkit-qt6-1-dev

RUN git clone https://github.com/hyprwm/hyprpolkitagent && \
    cd hyprpolkitagent && git checkout v0.1.2 && \
    cmake --no-warn-unused-cli -DCMAKE_BUILD_TYPE:STRING=Release -DCMAKE_INSTALL_PREFIX:PATH=/usr -S . -B ./build && \
    cmake --build ./build --config Release --target all -j`nproc 2>/dev/null || getconf NPROCESSORS_CONF` && \
    cmake --install build

ENTRYPOINT ["/usr/bin/bash"]
