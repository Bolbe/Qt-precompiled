To build Qt static on a macos system, open a terminal and from github-build-tools repo, run:
scripts/Qt/macos/build-qt-static-macos.sh

- tar generated folder:
XZ_OPT='-9' tar -cJf Qt6.5.3-static.tar.xz Qt6.5.3-static
- copy in this folder next to the one containing the dynamic linked Qt:




==== Before the script above
How to build the Qt compiler for macOS:
Download qt opensource 6.5.3

wget https://download.qt.io/archive/qt/6.5/6.5.3/single/qt-everywhere-src-6.5.3.tar.xz

brew install cmake
brew install ninja

tar xf qt-everywhere-src-6.5.3.tar.xz
cd qt-everywhere-src-6.5.3

rm -rf qtquick3d qtquick3dphysics qtquickeffectmaker qtgrpc qtscxml qtnetworkauth qt5compat qt3d qtactiveqt qtcharts qtcoap qtconnectivity qtdatavis3d qtlanguageserver qtlocation qtmqtt qtlottie qtmultimedia qtopcua qtpositioning qtremoteobjects qtsensors qtserialbus qtserialport qtvirtualkeyboard qtwayland qttools qtwebengine qtdoc qtspeech qttranslations qtwebview qtwebchannel qtwebsockets qthttpserver 
Warning: keep qtQuick3d and its dependencies (shadertools, physics) because QtQuick needs quick3d !
In the end, keep:
drwx------  32 gargamel  staff   1.0K Jul 21 11:17 qtbase
drwx------  23 gargamel  staff   736B Sep 25  2023 qtdeclarative
drwx------  17 gargamel  staff   544B Sep 25  2023 qtimageformats
drwx------  14 gargamel  staff   448B Sep 25  2023 qtquicktimeline
drwx------  14 gargamel  staff   448B Sep 25  2023 qtshadertools
drwx------  15 gargamel  staff   480B Sep 25  2023 qtsvg

Patch 1:

Edit qtbase/src/3rdparty/libpng
Replace #include <fp.h> by #include <math.h>
#    if !defined(__MATH_H__) && !defined(__MATH_H) && !defined(__cmath__)
#      include <fp.h>
#    endif

Patch 2:
qtbase/cmake/FindWrapOpenGL.cmake
Replace the APPLE block with:

    if(APPLE)
        get_target_property(__opengl_fw_lib_path OpenGL::GL IMPORTED_LOCATION)
        if(__opengl_fw_lib_path AND NOT __opengl_fw_lib_path MATCHES "/([^/]+)\\.framework$")
            get_filename_component(__opengl_fw_path "${__opengl_fw_lib_path}" DIRECTORY)
        endif()

        if(NOT __opengl_fw_path)
            set(__opengl_fw_path "-framework OpenGL")
        endif()

        target_link_libraries(WrapOpenGL::WrapOpenGL INTERFACE ${__opengl_fw_path})
    else()
        target_link_libraries(WrapOpenGL::WrapOpenGL INTERFACE OpenGL::GL)
    endif()


./configure -static \
  -prefix "$HOME/Qt6.5.3-static" \
  -release \
  -opensource -confirm-license \
  -nomake examples -nomake tests \
  -platform macx-clang \
  -qt-libpng -qt-libjpeg -qt-zlib \
  -no-framework \
  -no-icu \
  -no-dbus \
  -sql-sqlite \
  -strip

cmake --build . --parallel $(sysctl -n hw.ncpu) 
cmake --install . 

Then use qmake found in $HOME/Qt6.5.3-static/bin