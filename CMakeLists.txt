cmake_minimum_required(VERSION 3.17)

project(QtVTKProject)

#======================= INCLUSION OF Qt =======================#
set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTOUIC ON)
set(CMAKE_PREFIX_PATH $ENV{QTDIR})

find_package(Qt6Core REQUIRED)
find_package(Qt6Gui REQUIRED)
find_package(Qt6OpenGL REQUIRED)
find_package(Qt6Xml REQUIRED)
find_package(Qt6Widgets REQUIRED)

#======================= INCLUSION OF VTK ======================#
#set(VTK_DIR $ENV{VTK_DIR})
find_package(VTK COMPONENTS 
  CommonColor
  CommonCore
  CommonDataModel
  InteractionStyle
  RenderingContextOpenGL2
  RenderingCore
  RenderingFreeType
  RenderingGL2PSOpenGL2
  RenderingOpenGL2
  GUISupportQt
)

if (NOT VTK_FOUND)
  message(FATAL_ERROR "QtVTKProject: Unable to find the VTK build folder.")
endif()

#======================= INCLUSION OF Our Code ======================#
set(FORMS_DIR "${CMAKE_SOURCE_DIR}/forms")
set(INCLUDE_DIR "${CMAKE_SOURCE_DIR}/include")
set(SOURCE_DIR "${CMAKE_SOURCE_DIR}/src")
set_property(GLOBAL PROPERTY AUTOGEN_SOURCE_GROUP "Generated Files")

file(GLOB_RECURSE SOURCES
  "${INCLUDE_DIR}/*.h"
  "${SOURCE_DIR}/*.cpp"

)
file(GLOB_RECURSE UIS
  "${FORMS_DIR}/*.ui"
)

# Add the forms directory to the AUTOUIC search paths
set(CMAKE_AUTOUIC_SEARCH_PATHS ${CMAKE_AUTOUIC_SEARCH_PATHS} ${FORMS_DIR})

# Add the executable
if (WIN32) 
    add_executable(QtVTKProject WIN32 ${SOURCES} ${UIS})
elseif(UNIX)
    add_executable(QtVTKProject ${SOURCES} ${UIS})
endif()
source_group( "Forms" FILES ${UIS})


# Add the target includes
target_include_directories(QtVTKProject PRIVATE ${FORMS_DIR})
target_include_directories(QtVTKProject PRIVATE ${INCLUDE_DIR})
target_include_directories(QtVTKProject PRIVATE ${SOURCE_DIR})
find_package(Qt6 REQUIRED COMPONENTS OpenGLWidgets)
target_link_libraries( QtVTKProject Qt6::OpenGLWidgets)

#===================== LINKING LIBRARIES =======================#
target_link_libraries( QtVTKProject Qt6::OpenGL)
target_link_libraries( QtVTKProject Qt6::Xml)
target_link_libraries( QtVTKProject Qt6::Widgets)
target_link_libraries( QtVTKProject ${QT_LIBRARIES} ${OPENGL_LIBRARIES})
target_link_libraries( QtVTKProject
  VTK::CommonColor
  VTK::CommonCore
  VTK::CommonDataModel
  VTK::InteractionStyle
  VTK::RenderingContextOpenGL2
  VTK::RenderingCore
  VTK::RenderingFreeType
  VTK::RenderingGL2PSOpenGL2
  VTK::RenderingOpenGL2
  VTK::GUISupportQt
)
# vtk_module_autoinit is needed
vtk_module_autoinit(
  TARGETS QtVTKProject
  MODULES
  VTK::CommonColor
  VTK::CommonCore
  VTK::CommonDataModel
  VTK::InteractionStyle
  VTK::RenderingContextOpenGL2
  VTK::RenderingCore
  VTK::RenderingFreeType
  VTK::RenderingGL2PSOpenGL2
  VTK::RenderingOpenGL2
  VTK::GUISupportQt
)