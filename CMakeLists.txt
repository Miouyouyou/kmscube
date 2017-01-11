cmake_minimum_required(VERSION 2.8)

project(KMSCube)

include(FindPkgConfig)

pkg_search_module(DRM REQUIRED libdrm)
include_directories(${DRM_INCLUDE_DIRS})
add_executable(kmscube kmscube.c esTransform.c)
target_link_libraries(kmscube ${DRM_LIBRARIES} GLESv2 EGL gbm m)
