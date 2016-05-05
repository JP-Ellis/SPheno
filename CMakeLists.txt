################################################################################
# CMake
# Created by Joshua Ellis <josh@jpellis.me>
################################################################################
cmake_minimum_required(VERSION 2.8)

################################################################################
## Project Properties
################################################################################
## Project name
project(SPheno Fortran)
## Project version
set(VERSION 3.4.0)

################################################################################
## Compilation Options
################################################################################
## Check the build type and make sure it is either `Release` or `Debug`.  The
##  default is `Release`.
if(NOT CMAKE_BUILD_TYPE)
  set (CMAKE_BUILD_TYPE RELEASE)
endif(NOT CMAKE_BUILD_TYPE)

## Have all the `.mod` files in a subdirectory.
set(CMAKE_Fortran_MODULE_DIRECTORY ${CMAKE_BINARY_DIR}/mods)

## Get the Fortran compiler and set its flags
get_filename_component(Fortran_COMPILER_NAME ${CMAKE_Fortran_COMPILER} NAME)
if(Fortran_COMPILER_NAME MATCHES "gfortran")
  set(CMAKE_Fortran_FLAGS_RELEASE "-funroll-all-loops -O3 -Wall -pedantic")
  set(CMAKE_Fortran_FLAGS_DEBUG   "-O0 -g -Wall -pedantic")
# elseif (Fortran_COMPILER_NAME MATCHES "ifort")
#   set (CMAKE_Fortran_FLAGS_RELEASE "")
#   set (CMAKE_Fortran_FLAGS_DEBUG   "")
else(Fortran_COMPILER_NAME MATCHES "gfortran")
  message(WARNING "Fortran compiler '" ${Fortran_COMPILER_NAME} "' not supported.  Use at your own risk.")
endif(Fortran_COMPILER_NAME MATCHES "gfortran")

## Add definitions
add_definitions(-DGENERATIONMIXING -DONLYDOUBLE -DSEESAWIII)


################################################################################
## Create Targets
################################################################################
set(SRCS
  src/BranchingRatios.f90
  src/Chargino3.f90
  src/Control.F90
  src/Couplings.f90
  src/DecayFunctions.f90
  src/EplusEminusProduction.F90
  src/Experiment.f90
  src/Gluino3.f90
  src/InputOutput.F90
  src/LHC_observables.f90
  src/LoopCouplings.f90
  src/LoopFunctions.f90
  src/LoopMasses.F90
  src/LowEnergy.f90
  src/Mathematics.f90
  src/MathematicsQP.f90
  src/Model_Data.f90
  src/Neutralino3.f90
  src/NMSSM_tools.f90
  src/RGEs.F90
  src/RPtools.f90
  src/Slepton3Body.f90
  src/StandardModel.f90
  src/Stop3BodyDecays.f90
  src/SugraRuns.F90
  src/SusyDecays.f90
  src/SusyMasses.f90
  src/ThreeBodyPhaseSpace.f90
  src/ThreeBodyPhaseSpaceS.f90
  src/TwoLoopHiggsMass.f90
  )

add_library(${PROJECT_NAME}-lib ${SRCS})
set_target_properties(${PROJECT_NAME}-lib
  PROPERTIES OUTPUT_NAME ${PROJECT_NAME}
  )

add_executable(${PROJECT_NAME}-exe src/SPheno3.f90)
target_link_libraries(${PROJECT_NAME}-exe ${PROJECT_NAME}-lib)
set_target_properties(${PROJECT_NAME}-exe
  PROPERTIES OUTPUT_NAME ${PROJECT_NAME}
  )

################################################################################
## Install
################################################################################
if(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
  set(CMAKE_INSTALL_PREFIX "${CMAKE_SOURCE_DIR}")
endif(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)

install(
  TARGETS ${PROJECT_NAME}-lib ${PROJECT_NAME}-exe
  RUNTIME DESTINATION "bin"
  LIBRARY DESTINATION "lib"
  ARCHIVE DESTINATION "lib"
  )

