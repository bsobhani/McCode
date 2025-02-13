cmake_minimum_required(VERSION 3.17.0)
cmake_policy(VERSION 3.17.0)

# This will install McStas components
project(python-tools-mcgui C)

message(STATUS "Configure ${CMAKE_PROJECT_NAME} ${FLAVOR} ${PROJECT_NAME}")

# Choose between McStas or McXtrace
option(BUILD_MCSTAS   "Choose the McStas flavor" off)
option(BUILD_MCXTRACE "Choose the McXtrace flavor (trumps BUILD_MCSTAS)" Off)

# Set module path
set(CMAKE_MODULE_PATH "${CMAKE_MODULE_PATH}" "${CMAKE_CURRENT_SOURCE_DIR}/cmake/Modules/")

# Set McCode values (from mkdist or defaults)
include(MCUtil)

if (${BUILD_MCXTRACE})
  set(FLAVOR "mcxtrace")
  set(CPACK_PACKAGE_HOMEPAGE_URL "http://mcxtrace.org/")
elseif(${BUILD_MCSTAS})
  set(FLAVOR "mcstas")
  set(CPACK_PACKAGE_HOMEPAGE_URL "http://mcstas.org/")
else()
  message(FATAL_ERROR "No flavor specified!\n"
    "Please use either -DBUILD_MCSTAS=1 or -DBUILD_MCXTRACE=1")
endif()

setupMCCODE("${FLAVOR}")

# Select prefix
if(${BUILD_MCSTAS})
  set(P "mc")
else()
  set(P "mx")
endif()

set(TOOLS_NAME "tools/Python/${P}gui")

if(WINDOWS)
  set(TOOLS_LIB "${lib}/${TOOLS_NAME}")
else()
  set(TOOLS_LIB "${CMAKE_INSTALL_PREFIX}/${FLAVOR}/${MCCODE_VERSION}/${TOOLS_NAME}")
endif()


# Set some extra options for mccode_config
set(CC "${CMAKE_C_COMPILER}")
set(CFLAGS "-g -O2 -lm ${CMAKE_C_FLAGS}")

# CPack configuration
set(CPACK_PACKAGE_NAME          "${FLAVOR}-tools-python-${P}gui-${MCCODE_VERSION}")
set(CPACK_RESOURCE_FilE_LICENSE "${PROJECT_SOURCE_DIR}/COPYING")
set(CPACK_PACKAGE_CONTACT       "jsbn@fysik.dtu.dk")

set(CPACK_PACKAGE_VERSION       "${MCCODE_VERSION}")
set(CPACK_PACKAGE_VERSION_MAJOR "1")
set(CPACK_PACKAGE_VERSION_MINOR "0")


# NSIS
set(NSIS_NAME "Python-based mcgui (mcgui-py), ${MCCODE_STRING}")
set(CPACK_NSIS_PACKAGE_NAME "${NSIS_NAME}")
set(CPACK_NSIS_DISPLAY_NAME "${NSIS_NAME}")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "${NSIS_NAME}")

# Debian
set(CPACK_DEBIAN_PACKAGE_DEPENDS "${FLAVOR}-${MCCODE_VERSION}, ${FLAVOR}-tools-perl-cmdline-${MCCODE_VERSION}, ${FLAVOR}-tools-python-mccodelib-${MCCODE_VERSION}, python3, python3-pyqt5.qsci")

# RPM
if (RPMPROFILE)
   if (RPMPROFILE STREQUAL "fedora")
        set(PYDEPS "python3, python3-qt5, python3-qscintilla-qt5")
   else()
	set(PYDEPS ${FLAVOR}-miniconda3-${MCCODE_VERSION})
   endif()
else()
	set(PYDEPS ${FLAVOR}-miniconda3-${MCCODE_VERSION})
endif()

set(CPACK_RPM_PACKAGE_REQUIRES
  "${FLAVOR}-${MCCODE_VERSION}, ${FLAVOR}-tools-perl-cmdline-${MCCODE_VERSION}, ${FLAVOR}-tools-python-mccodelib-${MCCODE_VERSION}, ${FLAVOR}-miniconda3-${MCCODE_VERSION}"
  )
set(CPACK_RPM_PACKAGE_REQUIRES "qt5-qtbase-gui")

include(CPack)

set(WORK "${PROJECT_BINARY_DIR}/work")


# Configure fallback script
configure_file("${CMAKE_CURRENT_SOURCE_DIR}/mcgui.in" "${WORK}/${P}gui" @ONLY)

# Configure launcher
configure_file("${CMAKE_CURRENT_SOURCE_DIR}/McCode-py.desktop.in" "work/${FLAVOR}-${MCCODE_VERSION}-py.desktop")

if(WINDOWS)
  set(BINS "${PROJECT_SOURCE_DIR}/mcgui.py")
else()
  set(BINS "${PROJECT_SOURCE_DIR}/mcgui.py" "${WORK}/${P}gui")
endif()


if(WINDOWS)
  # Python files
  foreach(name viewclasses.py run-icon.png compile-icon.png widgets.py ${FLAVOR}-py.png)
    install(
      FILES "${PROJECT_SOURCE_DIR}/${name}"
      DESTINATION "${lib}/${TOOLS_NAME}"
      )
  endforeach()
  install(
    FILES ${BINS}
    DESTINATION "${lib}/${TOOLS_NAME}"
    RENAME "${P}gui.py"
    PERMISSIONS
    OWNER_READ OWNER_WRITE OWNER_EXECUTE
    GROUP_READ GROUP_EXECUTE
    WORLD_READ WORLD_EXECUTE
  )
endif()

if(NOT WINDOWS)
  # Python files
  foreach(name viewclasses.py run-icon.png compile-icon.png widgets.py ${FLAVOR}-py.png)
    install(
      FILES "${PROJECT_SOURCE_DIR}/${name}"
      DESTINATION "${FLAVOR}/${MCCODE_VERSION}/${TOOLS_NAME}"
      )
  endforeach()
  install(
    FILES ${BINS}
    DESTINATION "${FLAVOR}/${MCCODE_VERSION}/${TOOLS_NAME}"
    PERMISSIONS
    OWNER_READ OWNER_WRITE OWNER_EXECUTE
    GROUP_READ GROUP_EXECUTE
    WORLD_READ WORLD_EXECUTE
  )

  add_custom_target(
    "CREATE_${PROJECT_NAME}_SYMLINK" ALL DEPENDS "${WORK}/${P}gui"
    )

  install(
    PROGRAMS "${WORK}/${P}gui"
    DESTINATION ${FLAVOR}/${MCCODE_VERSION}/bin
    RENAME "${P}gui${DOT_PYTHON_SUFFIX}"
    )

  install(
    FILES "${WORK}/${FLAVOR}-${MCCODE_VERSION}-py.desktop" "${FLAVOR}-py.png"
    DESTINATION "${FLAVOR}/${MCCODE_VERSION}/launchers/"
  )

endif()
