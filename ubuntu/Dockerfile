FROM ubuntu:22.04

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y \
    git \
    wget \
    xz-utils \
    build-essential \
    ca-certificates \
    ninja-build \
    libfontconfig1-dev \
    libfreetype6-dev \
    libx11* \
    libxext-dev \
    libxfixes-dev \
    libxi-dev \
    libxrender-dev \
    libxcb* \
    libxkbcommon-x11-dev \
    mesa-common-dev \
    freeglut3 \
    freeglut3-dev \
    libglu1-mesa \
    libglu1-mesa-dev \
    libgl1-mesa-glx \
    libgl1-mesa-dev \
    python3 \
    chrpath \
    zlib1g-dev \
    liblzma-dev \
    libbz2-dev \
    && apt-get clean cache

ARG CMAKE_VERSION=3.31.6
RUN ARCH=$(uname -m) \
    && wget -q https://github.com/Kitware/CMake/releases/download/v${CMAKE_VERSION}/cmake-${CMAKE_VERSION}-linux-${ARCH}.tar.gz \
    && tar xf cmake-${CMAKE_VERSION}-linux-${ARCH}.tar.gz --strip-components=1 -C /usr/local \
    && rm cmake-${CMAKE_VERSION}-linux-${ARCH}.tar.gz \
    && cmake --version

ARG BUILD_JOBS=4
RUN wget -q https://download.qt.io/archive/qt/6.12/6.12.0/single/qt-everywhere-src-6.12.0.tar.xz \
    && tar xf qt-everywhere-src-6.12.0.tar.xz \
    && cd qt-everywhere-src-6.12.0 \
    && rm -rf qtcoap qtlanguageserver qtquick3d qtwebsockets qtconnectivity qtlocation \
    qtquick3dphysics qtspeech qtwebview qtdatavis3d qtlottie qtquickeffectmaker qt3d \
    qtmqtt qtquicktimeline qttasktree qt5compat qtdoc qtmultimedia qtremoteobjects \
    qtactiveqt qtgraphs qtnetworkauth qtscxml qttranslations qtgrpc qtopcua qtsensors \
    qtvirtualkeyboard qtcanvaspainter qthttpserver qtopenapi \
    qtserialbus qtwayland qtcharts qtpositioning qtserialport qtwebchannel \
    && ./configure \
        -opensource \
        -release \
        -confirm-license \
        -prefix /usr/local/Qt-6.12.0 \
        -nomake tests \
        -nomake examples \
        -no-icu \
        -no-dbus \
        -sql-sqlite \
        -xcb \
        -xcb-xlib \
        -bundled-xcb-xinput \
    && cmake --build . --parallel ${BUILD_JOBS} \
    && cmake --install . \
    && cd .. \
    && rm -rf qt-everywhere-src-6.12.0
