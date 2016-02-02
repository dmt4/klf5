# ###################################################### #
# CMake project file for klatexformula/src/klfkateplugin #
# ###################################################### #
# $Id: CMakeLists.txt 902 2014-08-09 23:44:15Z phfaist $
# ###################################################### #

# KLatexFormula KTextEditor Plugin

# NOTE: This CMakeLists.txt can either be included from the root CMakeLists.txt
#       to build the plugin, or the plugin can also be built separately by
#       calling directly this CMakeLists.txt as root build script, eg.
#         > cd src/klfkateplugin/
#         > mkdir build && cd build
#         build> cmake .. && make
#

project (klf)

# minimal requirements
cmake_minimum_required (VERSION 2.8.12 FATAL_ERROR)
set (QT_MIN_VERSION "5.4.0")
set(KF5_DEP_VERSION "5.10.0")

# KDE Application Version, managed by release script
set (KDE_APPLICATIONS_VERSION_MAJOR "15")
set (KDE_APPLICATIONS_VERSION_MINOR "12")
set (KDE_APPLICATIONS_VERSION_MICRO "1")
set (KDE_APPLICATIONS_VERSION "${KDE_APPLICATIONS_VERSION_MAJOR}.${KDE_APPLICATIONS_VERSION_MINOR}.${KDE_APPLICATIONS_VERSION_MICRO}")

# we need some parts of the ECM CMake helpers
find_package (ECM 1.1.0 REQUIRED NO_MODULE)
set (CMAKE_MODULE_PATH ${ECM_MODULE_PATH})
include(ECMOptionalAddSubdirectory)
include(ECMInstallIcons)
include(ECMSetupVersion)
include(ECMMarkNonGuiExecutable)
include(ECMGenerateHeaders)
include(ECMAddAppIcon) 
include(GenerateExportHeader)

include(CMakePackageConfigHelpers)
include(FeatureSummary)
include(WriteBasicConfigVersionFile)
include (CheckFunctionExists)

include(KDEInstallDirs)
include(KDECMakeSettings)
include(KDECompilerSettings)

find_package(Qt5 ${QT_MIN_VERSION} CONFIG REQUIRED Core DBus Widgets Script Sql)

if(BUILD_TESTING)
  find_package(Qt5Test ${QT_MIN_VERSION} CONFIG REQUIRED)
endif()

# Load the frameworks we need
find_package(KF5 "${KF5_DEP_VERSION}" REQUIRED COMPONENTS
  Config
  Crash
  I18n
  JobWidgets
  KIO
  Parts
  TextEditor
  WindowSystem
  XmlGui
  IconThemes
  KDELibs4Support
)


include_directories("${CMAKE_CURRENT_SOURCE_DIR}/../klftools")
include_directories("${CMAKE_CURRENT_SOURCE_DIR}/../klfbackend")

# mark_as_advanced(
# 	Automoc4_DIR
# 	KDE4_AUTH_POLICY_FILES_INSTALL_DIR
# 	KDE4_BUILD_TESTS
# 	KDE4_DATA_DIR
# 	KDE4_ENABLE_FINAL
# 	KDE4_ENABLE_FPIE
# 	KDE4_ENABLE_HTMLHANDBOOK
# 	KDE4_PLASMA_OPENGL_FOUND
# 	KDE4_SERIALIZE_TOOL
# 	KDE4_USE_COMMON_CMAKE_PACKAGE_CONFIG_DIR
# )

# klfkateplugin plugin target

set(ktexteditor_klf_PART_SRCS klfkateplugin.cpp klfkteparser.cpp klfkateplugin_config.cpp )
set(ktexteditor_klf_PART_UI   klfkatepluginconfigwidget.ui klfktepreviewwidget.ui)
ki18n_wrap_ui(ktexteditor_klf_PART_SRCS ${ktexteditor_klf_PART_UI} )

# kde4_install_icons(${ICON_INSTALL_DIR})
add_library(ktexteditor_klf MODULE ${ktexteditor_klf_PART_SRCS} )
kcoreaddons_desktop_to_json (ktexteditor_klf ktexteditor_klf.desktop)
# kcoreaddons_desktop_to_json (ktexteditor_klf_config ktexteditor_klf_config.desktop)
target_link_libraries(ktexteditor_klf KF5::TextEditor KF5::KIOCore KF5::I18n KF5::KDELibs4Support)

# if(KLF_ROOT_CMAKE_READ AND NOT KLF_LIBKLFBACKEND_STATIC)
#   # link to our klfbackend
#   target_link_libraries(ktexteditor_klf  klfbackend)
# elseif(NOT KLF_ROOT_CMAKE_READ)
#   # link to external klfbackend, we are being compiled externally
#   target_link_libraries(ktexteditor_klf  -lklfbackend)
# endif(KLF_ROOT_CMAKE_READ AND NOT KLF_LIBKLFBACKEND_STATIC)

install(TARGETS ktexteditor_klf DESTINATION ${PLUGIN_INSTALL_DIR}/ktexteditor )


# install(FILES klfkatepluginui.rc DESTINATION "${DATA_INSTALL_DIR}/ktexteditor_klf" )

# install(FILES ktexteditor_klf.desktop
#               ktexteditor_klf_config.desktop DESTINATION "${SERVICES_INSTALL_DIR}" )

