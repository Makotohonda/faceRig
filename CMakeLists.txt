cmake_minimum_required(VERSION 3.15)

# Suppress the generation of MinSizeRel and RelWithDebInfo.
set(CMAKE_CONFIGURATION_TYPES "Debug;Release" CACHE STRING "limited configs" FORCE)

# Suppress generation of ZERO_CHECK project.
set(CMAKE_SUPPRESS_REGENERATION true)

# Specify version of compiler.
set(CMAKE_CXX_STANDARD 14)

# Set project name.
project(Demo)

# Set output directory paths.
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG ${CMAKE_CURRENT_BINARY_DIR})
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELEASE ${CMAKE_CURRENT_BINARY_DIR})

find_package(OpenCV REQUIRED)

# If the package has been found, several variables will
# be set, you can find the full list with descriptions
# in the OpenCVConfig.cmake file.
# Print some message showing some of them
message(STATUS "OpenCV library status:")
message(STATUS "    version: ${OpenCV_VERSION}")
message(STATUS "    libraries: ${OpenCV_LIBS}")
message(STATUS "    include path: ${OpenCV_INCLUDE_DIRS}")

set(LIVE2D_DIR /Users/sky/CProject/CubismSdkForNative-4-beta.1)
# Set directory paths.
set(CORE_DIR ${LIVE2D_DIR}/Core)
set(FRAMEWORK_DIR ${LIVE2D_DIR}/Framework)
set(THIRD_PARTY_DIR ${LIVE2D_DIR}/Samples/OpenGL/thirdParty)
set(STB_DIR ${THIRD_PARTY_DIR}/stb)
set(GLEW_DIR ${THIRD_PARTY_DIR}/glew-2.1.0)
set(GLFW_DIR ${THIRD_PARTY_DIR}/glfw-3.3)

# Add the sourcecodes for this project.
AUX_SOURCE_DIRECTORY(Demo DIR_SRCS)

# Set preprocessor definitions for CubismNativeFramework.
set(FRAMEWORK_DEFINITIOINS -DCSM_TARGET_MAC_GL -DGLEW_STATIC)

# Give the thirdparty OpenGL library include paths to CubismNativeFramework.
set(FRAMEWORK_GLEW_PATH ${GLEW_DIR}/include)
set(FRAMEWORK_GLFW_PATH ${GLFW_DIR}/include)

# Add CubismNativeFramework.
add_subdirectory(${FRAMEWORK_DIR} ${CMAKE_CURRENT_BINARY_DIR}/Framework)

# Add preprocessor definition.
add_definitions(-DCSM_TARGET_MAC_GL -DGLEW_STATIC)

# When this flag is turned on, the renderer will draw for each texture that has LAppModel.
# Otherwise, will draw to the main framebuffer.
#add_definitions(-DUSE_MODEL_RENDER_TARGET)
# When this flag is turned on, the renderer will draw to the texture. The texture only has LAppView.
# This flag has higher priority than DUSE_MODEL_RENDER_TARGET.
#add_definitions(-DUSE_RENDER_TARGET)

# Add include paths.
include_directories(
    ${CORE_DIR}/include
    ${FRAMEWORK_DIR}/src
    ${STB_DIR}/include
    ${GLEW_DIR}/include
    ${GLFW_DIR}/include
    ${OpenCV_INCLUDE_DIRS})

# Add library paths.
link_directories(
    ${CORE_DIR}/lib/macos
    ${GLEW_DIR}/lib
    ${GLFW_DIR}/lib)

# Set sourcecodes.
add_executable(Demo ${DIR_SRCS})

# Set output file name.
set_target_properties(Demo PROPERTIES OUTPUT_NAME_DEBUG "Demo_Debug")

# Set libraries for linking.
find_package(OpenGL REQUIRED)
target_link_libraries(Demo
                      Live2DCubismCore.a
                      Framework
                      libGLEW.dylib
                      libglfw.dylib
                      ${OPENGL_LIBRARIES}
		      ${OpenCV_LIBS})
