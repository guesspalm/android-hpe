# For more information about using CMake with Android Studio, read the
# documentation: https://d.android.com/studio/projects/add-native-code.html

# Sets the minimum version of CMake required to build the native library.

# set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=gnu++11")

cmake_minimum_required(VERSION 3.4.1)

# Creates and names a library, sets it as either STATIC
# or SHARED, and provides the relative paths to its source code.
# You can define multiple libraries, and CMake builds them for you.
# Gradle automatically packages shared libraries with your APK.

set(EXT_INSTALL_PATH ${CMAKE_SOURCE_DIR}/third_party)
set(OPENCV_ANDROID_SDK ${EXT_INSTALL_PATH}/opencv-3.0.0)
set(MINIGLOG_DIR ${EXT_INSTALL_PATH}/miniglog)
set(DLIB_DIR ${EXT_INSTALL_PATH}/dlib)


################## OPENCV ####################
include_directories(${OPENCV_ANDROID_SDK}/include)
add_library(my_cv SHARED IMPORTED )
set_target_properties(my_cv PROPERTIES
    IMPORTED_LOCATION "${OPENCV_ANDROID_SDK}/libs/${ANDROID_ABI}/libopencv_java3.so")

################## glog ####################
include_directories(${MINIGLOG_DIR})
add_library(glog

            STATIC

            ${MINIGLOG_DIR}/glog/logging.cc
              )

################## dlib ####################
include_directories(${DLIB_DIR})
add_library(my_dlib
            STATIC

            ${DLIB_DIR}/dlib/threads/threads_kernel_shared.cpp
            ${DLIB_DIR}/dlib/entropy_decoder/entropy_decoder_kernel_2.cpp
            ${DLIB_DIR}/dlib/base64/base64_kernel_1.cpp
            ${DLIB_DIR}/dlib/threads/threads_kernel_1.cpp
            ${DLIB_DIR}/dlib/threads/threads_kernel_2.cpp

              )
include_directories(src/main/cpp)

add_library( # Sets the name of the library.
             head_pose_det

             # Sets the library as a shared library.
             SHARED

             # Provides a relative path to your source file(s).

             src/main/cpp/jni_head_pose_det.cpp
             src/main/cpp/imageutils_jni.cpp
             src/main/cpp/common/rgb2yuv.cpp

             src/main/cpp/common/yuv2rgb.cpp
             src/main/cpp/common/bitmap2mat2bitmap.cpp)


# Specifies libraries CMake should link to your target library. You
# can link multiple libraries, such as libraries you define in this
# build script, prebuilt third-party libraries, or system libraries.

target_link_libraries( # Specifies the target library.
                       head_pose_det

                        my_cv
                        my_dlib
                        glog
                       # Links the target library to the log library
                       # included in the NDK.
                       m
                       log
                       dl
                       z
                       jnigraphics
                       atomic
                        )