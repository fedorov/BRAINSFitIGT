project(SimpleDiffeomorphicRegistration)
cmake_minimum_required(VERSION 2.6)

if (WIN32)
  set(BUILD_SUBDIR ${CMAKE_BUILD_TYPE}/)
endif (WIN32)

if(0)
if(BUILD_SHARED_LIBS)
## The dynamicly loadable slicer module can be built
##only when ITK shared libraries are used.
#
# CREATE A loadable module for Slicer
add_library(BRAINSDemonWarpSlicerModule SHARED ${BRAINSDemonWarp_SOURCE})
set_target_properties(BRAINSDemonWarpSlicerModule PROPERTIES COMPILE_FLAGS "-Dmain=ModuleEntryPoint")
target_link_libraries(BRAINSDemonWarpSlicerModule ITKAlgorithms ITKIO ${OPTIONAL_DEBUG_LINK_LIBRARIES})
endif(BUILD_SHARED_LIBS)

add_library(BRAINSDemonWarpLIB itkSimpleDiffeomorphicRegistration.cxx)
target_link_libraries(BRAINSDemonWarpLIB ITKAlgorithms ITKIO ${OPTIONAL_DEBUG_LINK_LIBRARIES})
install(TARGETS BRAINSDemonWarpLIB
    RUNTIME DESTINATION bin
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib)

include_directories(${BRAINSDemonWarp_SOURCE_DIR} ${BRAINSDemonWarp_SOURCE_DIR}/Common)
set(SimpleDiffeomorphicRegistration_SOURCE lib_test.cxx)
add_executable( SimpleDiffeomorphicRegistrationCLP ${SimpleDiffeomorphicRegistration_SOURCE} )
target_link_libraries(SimpleDiffeomorphicRegistrationCLP BRAINSDemonWarpLIB)

set(LIB_TEST ${LAUNCH_EXE} ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${BUILD_SUBDIR}SimpleDiffeomorphicRegistrationCLP)

add_test(ValidateLIB ${LIB_TEST}
   ${BRAINSDemonWarp_SOURCE_DIR}/TestingBRAINSDemonWarp/ANON0006_20_T1_dbg_splayed.nii.gz
   ${BRAINSDemonWarp_SOURCE_DIR}/TestingBRAINSDemonWarp/ANON0006_20_T1_sag_twisted.nii.gz
   ${BRAINSDemonWarp_BINARY_DIR}/orientedImagesDemons_lib_test.nii.gz
)


endif(0)
