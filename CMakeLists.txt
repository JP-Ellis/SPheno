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
set(VERSION 4.0.0)

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
  set(CMAKE_Fortran_FLAGS_RELEASE "-w -O3 -march=native -mtune=native -ffree-line-length-none -ffixed-line-length-none")
  set(CMAKE_Fortran_FLAGS_DEBUG   "-Wall -pedantic -Og -g -ffree-line-length-none -ffixed-line-length-none")
elseif (Fortran_COMPILER_NAME MATCHES "f95")
  set(CMAKE_Fortran_FLAGS_RELEASE "-w -O3 -march=native -mtune=native -ffree-line-length-none -ffixed-line-length-none")
  set(CMAKE_Fortran_FLAGS_DEBUG   "-Wall -pedantic -Og -g -ffree-line-length-none -ffixed-line-length-none")
elseif (Fortran_COMPILER_NAME MATCHES "ifort")
  set (CMAKE_Fortran_FLAGS_RELEASE "-w -fast")
  set (CMAKE_Fortran_FLAGS_DEBUG   "-warn all -g")
else(Fortran_COMPILER_NAME MATCHES "gfortran")
  message(WARNING "Fortran compiler '" ${Fortran_COMPILER_NAME} "' not supported.  Use at your own risk.")
endif(Fortran_COMPILER_NAME MATCHES "gfortran")

## Precision (double or quad)
if(NOT DEFINED PRECISION)
  message(STATUS "No precision specified, assuming double precision.")
  set(PRECISION "DOUBLE")
endif(NOT DEFINED PRECISION)

if(PRECISION MATCHES "QUAD.*")
  message(STATUS "Using quadruple precision.")
  add_definitions(-DQUADRUPOLE)
elseif(PRECISION MATCHES "DOUBLE")
  message(STATUS "Using double precision.")
  add_definitions(-DONLYDOUBLE)
else(PRECISION MATCHES "QUAD.*")
  message(FATAL_ERROR "Supported precisions are DOUBLE and QUAD.")
endif(PRECISION MATCHES "QUAD.*")

## Add definitions
add_definitions(-DGENERATIONMIXING -DONLYDOUBLE -DSEESAWIII)

## Change the default installation directory to be where project's root.
if(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
  set(CMAKE_INSTALL_PREFIX "${CMAKE_SOURCE_DIR}")
endif(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)

################################################################################
## Main target
################################################################################
set(SPHENOSRCEXE src/SPheno4.f90)
set(SPHENOSRCS
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
  src/SPheno4.f90
  src/StandardModel.f90
  src/Stop3BodyDecays.f90
  src/SugraRuns.F90
  src/SusyDecays.f90
  src/SusyMasses.f90
  src/ThreeBodyPhaseSpace.f90
  src/ThreeBodyPhaseSpaceS.f90
  src/TwoLoopHiggsMass.f90
  )

add_library(${PROJECT_NAME}-lib ${SPHENOSRCS})
set_target_properties(${PROJECT_NAME}-lib
  PROPERTIES OUTPUT_NAME ${PROJECT_NAME}
  )

add_executable(${PROJECT_NAME}-exe ${SPHENOSRCEXE})
target_link_libraries(${PROJECT_NAME}-exe ${PROJECT_NAME}-lib)
set_target_properties(${PROJECT_NAME}-exe
  PROPERTIES OUTPUT_NAME ${PROJECT_NAME}
  )

install(TARGETS ${PROJECT_NAME}-lib ${PROJECT_NAME}-exe
        RUNTIME DESTINATION bin
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib
        )

message(STATUS "Added main SPheno target.")

################################################################################
## Generate models too
################################################################################
## Helper function to get all subdirectories
## From http://stackoverflow.com/a/7788165/1573761
macro(subdirlist result curdir)
  file(GLOB children RELATIVE ${curdir} ${curdir}/*)
  set(dirlist "")
  foreach(child ${children})
    if(IS_DIRECTORY ${curdir}/${child})
      list(APPEND dirlist ${child})
    endif()
  endforeach()
  set(${result} ${dirlist})
endmacro()

## The models are passed in as comma-separated list.
if(MODELS)
  ## If we are generating all models, get all of them from the models directory
  if(${MODELS} STREQUAL "ALL")
    subdirlist(MODELS "${CMAKE_SOURCE_DIR}/models")
  else(${MODELS} STREQUAL "ALL")
    string(REPLACE "," ";" MODELS ${MODELS})
  endif(${MODELS} STREQUAL "ALL")

  foreach(MODEL ${MODELS})
    ## First make sure the model directory exists.
    if(IS_DIRECTORY ${CMAKE_SOURCE_DIR}/models/${MODEL})

      ## If the model defines its own CMakeLists, then use that
      if(EXISTS ${CMAKE_SOURCE_DIR}/models/${MODEL}/CMakeLists.txt)
        add_subdirectory(models/${MODEL})

        message(STATUS "Added model '${MODEL}'.")

      else(EXISTS ${CMAKE_SOURCE_DIR}/models/${MODEL}/CMakeLists.txt)
        ## Otherwise, we expect that SPheno${MODEL}.f90 is the main file for the
        ## executable and all other files in the directory are for destined for
        ## the library.
        file(GLOB SRCEXE ${CMAKE_SOURCE_DIR}/models/${MODEL}/SPheno*.f90)
        file(GLOB_RECURSE SRCS ${CMAKE_SOURCE_DIR}/models/${MODEL}/*.f90 models/${MODEL}/*.F90)
        list(REMOVE_ITEM SRCS ${SRCEXE})

        add_library(${MODEL}-lib ${SRCS})
        target_link_libraries(${MODEL}-lib ${PROJECT_NAME}-lib)
        set_target_properties(${MODEL}-lib
          PROPERTIES OUTPUT_NAME ${MODEL}
          )

        add_executable(${MODEL}-exe ${SRCEXE})
        target_link_libraries(${MODEL}-exe ${MODEL}-lib)
        set_target_properties(${MODEL}-exe
          PROPERTIES OUTPUT_NAME ${MODEL}
          )

        install(TARGETS ${MODEL}-lib ${MODEL}-exe
          RUNTIME DESTINATION bin
          LIBRARY DESTINATION lib
          ARCHIVE DESTINATION lib
          )

        message(STATUS "Added model '${MODEL}'.")

      endif(EXISTS ${CMAKE_SOURCE_DIR}/models/${MODEL}/CMakeLists.txt)

    ## If the model directory doesn't exist, issue a warning
    else(IS_DIRECTORY ${CMAKE_SOURCE_DIR}/models/${MODEL})
      message(WARNING "These is no directory '${MODEL}' in models/.  This model will be ignored.")
    endif(IS_DIRECTORY ${CMAKE_SOURCE_DIR}/models/${MODEL})
  endforeach(MODEL)
endif(MODELS)
