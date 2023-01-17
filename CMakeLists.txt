# CMakeList.txt : CMake project for ObjectDetection, include source and define
# project specific logic here.
#
cmake_minimum_required (VERSION 3.8)

project ("ObjectDetection")
find_package( OpenCV REQUIRED )
include_directories( ${OpenCV_INCLUDE_DIRS} )
add_executable (ShowImage "ShowImage.cpp"  "ObjectDetection.h" )
add_executable (CameraCapture "CameraCapture.cpp")
target_link_libraries( ShowImage ${OpenCV_LIBS} )
target_link_libraries( CameraCapture ${OpenCV_LIBS} )

# Add source to this project's executable.


if (CMAKE_VERSION VERSION_GREATER 3.12)
  set_property(TARGET ShowImage PROPERTY CXX_STANDARD 20)
  set_property(TARGET CameraCapture PROPERTY CXX_STANDARD 20)
endif()

# TODO: Add tests and install targets if needed.
