# Insert copyright here

CMAKE_MINIMUM_REQUIRED(VERSION 2.8)

# This is not required, but is still very useful
PROJECT(kmscube, [VERSION 0.0.1])
# Looks like there's no official way to set up the Project URI
# automatically though...
SET(PROJECT_UPSTREAM_URI ["https://cgit.freedesktop.org/mesa/kmscube/"])

INCLUDE(FindPkgConfig)

OPTION(ENABLE_GBM_MODIFIERS "Use GBM Modifiers" OFF)

# Obtain compiler/linker options for depedencies
PKG_CHECK_MODULES(DRM REQUIRED libdrm >= 2.4.71)
PKG_CHECK_MODULES(GBM REQUIRED gbm >= 13.0)
PKG_CHECK_MODULES(EGL REQUIRED egl)
PKG_CHECK_MODULES(GLES2 REQUIRED glesv2)

# Check for gst and enable cube-video conditionally:
PKG_CHECK_MODULES(GST
	gstreamer-1.0 >= 1.6.0
	gstreamer-plugins-base-1.0 >= 1.6.0
	gstreamer-app-1.0 >= 1.6.0
	gstreamer-allocators-1.0 >= 1.6.0
	gstreamer-video-1.0 >= 1.6.0
	glib-2.0
)

if (GST_FOUND)
	MESSAGE(STATUS "Have GStreamer support")
	MESSAGE(STATUS "Building cube-video support")
	ADD_DEFINITIONS(-DHAVE_GST)
	INCLUDE_DIRS(${GST_INCLUDE_DIRS})
endif (GST_FOUND)

CHECK_SYMBOL_EXISTS(gbm_bo_get_modifier gbm.h GBM_MODIFIERS)

if (NOT GBM_MODIFIERS AND ENABLE_GBM_MODIFIERS)
	MESSAGE(FATAL_ERROR "Your GBM library needs to define gbm_bo_get_modifier in order to use GBM modifiers")
endif (NOT GBM_MODIFIERS AND ENABLE_GBM_MODIFIERS)

INCLUDE_DIRECTORIES(${DRM_INCLUDE_DIRS})
ADD_EXECUTABLE(kmscube kmscube.c esTransform.c)
TARGET_LINK_LIBRARIES(kmscube ${DRM_LIBRARIES} GLESv2 EGL gbm m)
