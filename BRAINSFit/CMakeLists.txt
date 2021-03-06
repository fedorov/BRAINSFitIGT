project(BRAINSFit)
set(LOCAL_PROJECT_NAME BRAINSFit)
cmake_minimum_required(VERSION 2.8)
cmake_policy(VERSION 2.8)

enable_testing()
include(Dart)
include(${BRAINS_CMAKE_HELPER_DIR}/PreventInSourceBuilds.cmake)
include(${BRAINS_CMAKE_HELPER_DIR}/CMakeBuildMacros.cmake)

###
SETIFEMPTY(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/lib)
SETIFEMPTY(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/lib)
SETIFEMPTY(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/bin)
SETIFEMPTY(CMAKE_BUNDLE_OUTPUT_DIRECTORY  ${CMAKE_CURRENT_BINARY_DIR}/bin)
link_directories(${CMAKE_LIBRARY_OUTPUT_DIRECTORY} ${CMAKE_ARCHIVE_OUTPUT_DIRECTORY})

SETIFEMPTY(MIDAS_REST_URL "http://midas.kitware.com/api/rest" CACHE STRING "The MIDAS server where testing data resides")
set(MIDAS_KEY_DIR "${PROJECT_SOURCE_DIR}/../BRAINSCommonLib/TestData")

###
CHECKIFSLICER3BUILD()  ## Call the convenience macro

if(NOT ITK_FOUND)
    find_package(ITK REQUIRED)
    include(${ITK_USE_FILE})
endif(NOT ITK_FOUND)

find_package(BRAINSCommonLib NO_MODULE REQUIRED)
include(${BRAINSCommonLib_USE_FILE})

#-----------------------------------------------------------------------------
# Output directories.
#
include(${BRAINS_CMAKE_HELPER_DIR}/CMakeBRAINS3BuildMacros.cmake)
#SETOPTIONALDEBUGIMAGEVIEWER()

# No need to add with nothing to compile. add_subdirectory(BRAINSFit_Common)
include(${BRAINS_CMAKE_HELPER_DIR}/IJMacros.txt)

configure_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/BRAINSFitCommonLib.h.in
  ${CMAKE_CURRENT_BINARY_DIR}/BRAINSFitCommonLib.h
  )

## ITKv4 requires that ITKReview is explicitly linked against
if(ITK_VERSION_MAJOR EQUAL 4 )
  set(ITKREVIEWLIB ITKReview)
endif(ITK_VERSION_MAJOR EQUAL 4 )

###
set(CLP BRAINSFit)
set(${CLP}_SOURCE BRAINSFit.cxx  ${BRAINSFit_SOURCE_DIR}/BRAINSFitPrimary.cxx)
CONFIGUREBRAINSORSLICERPROPERTIES(${CLP} BRAINSFitPrimary.xml "${${CLP}_SOURCE}" "")
target_link_libraries( ${CLP} BRAINSCommonLib ${ITKREVIEWLIB} ITKIO ITKAlgorithms ${OPTIONAL_DEBUG_LINK_LIBRARIES})

INSTALL(TARGETS BRAINSFit DESTINATION bin)

if(BUILD_TESTING)
  add_subdirectory(TestSuite)
endif(BUILD_TESTING)

include(InstallRequiredSystemLibraries)
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "BRAINSFit Image Registration")
set(CPACK_PACKAGE_VENDOR "University of Iowa Dept. of Psychiatry")
set(CPACK_PACKAGE_DESCRIPTION_FILE "${CMAKE_CURRENT_SOURCE_DIR}/ReadMe.txt" )
set(CPACK_PACKAGE_VERSION_MAJOR "1")
set(CPACK_PACKAGE_VERSION_MINOR "0")
set(CPACK_PACKAGE_VERSION_PATCH "0")
set(CPACK_PACKAGE_EXECUTABLES "BRAINSFit")
if(APPLE)
set(CPACK_GENERATOR "TGZ;PackageMaker")
elseif(LINUX)
set(CPACK_GENERATOR "TGZ;RPM")
endif()

include(CPack)
