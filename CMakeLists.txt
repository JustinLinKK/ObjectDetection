# CMakeList.txt : CMake project for ObjectDetection, include source and define
# project specific logic here.
#
# Torch prefix cmd:
# cmake -DCMAKE_PREFIX_PATH=(path of torch library)
cmake_minimum_required (VERSION 3.8 FATAL_ERROR)
enable_language(CUDA)
project ("ObjectDetection" LANGUAGES CXX CUDA)

if(NOT DEFINED CMAKE_CUDA20_STANDARD_COMPILE_OPTION)
  set(CMAKE_CUDA20_STANDARD_COMPILE_OPTION "")
  set(CMAKE_CUDA20_EXTENSION_COMPILE_OPTION "")
endif()


#Find correct cuda arch in https://developer.nvidia.com/cuda-gpus https://arnon.dk/matching-sm-architectures-arch-and-gencode-for-various-nvidia-cards/
#https://github.com/traveller59/spconv/issues/277
set(CMAKE_PREFIX_PATH "C:/OpenCV/libtorch")
#set the cudnn path
#find the torch
find_package(OpenCV REQUIRED)
find_package(Torch REQUIRED)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${TORCH_CXX_FLAGS}")

include_directories( ${OpenCV_INCLUDE_DIRS} )   
add_executable(example-app "example-app.cpp")
add_executable(hello "hello.cu")
if (CMAKE_VERSION VERSION_GREATER 3.12)
  set_property(TARGET hello PROPERTY CXX_STANDARD 20)
endif()
add_executable (ShowImage "ShowImage.cpp")
add_executable (CameraCapture "CameraCapture.cpp")
add_executable (PytorchExample "PytorchExample.cpp")
target_link_libraries(example-app "${TORCH_LIBRARIES}")
target_link_libraries( PytorchExample "${TORCH_LIBRARIES}")
target_link_libraries( ShowImage ${OpenCV_LIBS} )
target_link_libraries( CameraCapture ${OpenCV_LIBS} )
target_link_libraries( PytorchExample ${OpenCV_LIBS} )

# Request that samples be built with -std=c++14
# As this is a public compile feature anything that links to 
# Example will also build with -std=c++14

set_target_properties( PytorchExample PROPERTIES CUDA_SEPARABLE_COMPILATION ON)
set_property(TARGET example-app PROPERTY CXX_STANDARD 20)


# The following code block is suggested to be used on Windows.
# According to https://github.com/pytorch/pytorch/issues/25457,
# the DLLs need to be copied to avoid memory errors.
if (MSVC)
  file(GLOB TORCH_DLLS "${TORCH_INSTALL_PREFIX}/lib/*.dll")
  add_custom_command(TARGET example-app
                     POST_BUILD
                     COMMAND ${CMAKE_COMMAND} -E copy_if_different
                     ${TORCH_DLLS}
                     $<TARGET_FILE_DIR:example-app>)
  file(GLOB TORCH_DLLS "${TORCH_INSTALL_PREFIX}/lib/*.dll")
  add_custom_command(TARGET PytorchExample
                     POST_BUILD
                     COMMAND ${CMAKE_COMMAND} -E copy_if_different
                     ${TORCH_DLLS}
                     $<TARGET_FILE_DIR:example-app>)
endif (MSVC)

# Add source to this project's executable.


if (CMAKE_VERSION VERSION_GREATER 3.12)
  set_property(TARGET ShowImage PROPERTY CXX_STANDARD 20)
  set_property(TARGET CameraCapture PROPERTY CXX_STANDARD 20)
  set_property(TARGET PytorchExample PROPERTY CXX_STANDARD 20)
endif()

# TODO: Add tests and install targets if needed.
