# MOM6 src, config_src file and include files and directories
include(mom6_files.cmake)

### Build MOM6 library
set(libName "mom6")
list(APPEND mom6_lib_targets ${libName})
set(moduleDir "${CMAKE_CURRENT_BINARY_DIR}/include/mom6")

add_library(${libName} STATIC ${mom6_src_files})
add_library(${libName}::${libName} ALIAS ${libName})

set_target_properties(${libName} PROPERTIES Fortran_MODULE_DIRECTORY
                                            ${moduleDir})

target_link_libraries(${libName} PUBLIC fms::fms_8
                                        NetCDF::NetCDF_Fortran)

target_include_directories(${libName} PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/MOM6/config_src/dynamic>
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/MOM6/src/framework>)

target_include_directories(${libName} INTERFACE
  $<BUILD_INTERFACE:${moduleDir}>
  $<INSTALL_INTERFACE:include/mom6>)

if(OpenMP_Fortran_FOUND)
  target_link_libraries(${libName} PUBLIC OpenMP::OpenMP_Fortran)
endif()

# Install compiled Fortran modules
install(DIRECTORY ${moduleDir} DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

### Build NUOPC Driver library
set(libName "mom6nuopc")
list(APPEND mom6_lib_targets ${libName})
set(moduleDir "${CMAKE_CURRENT_BINARY_DIR}/include/mom6nuopc")

add_library(${libName} STATIC ${mom6_config_src_files})
add_library(${libName}::${libName} ALIAS ${libName})

set_target_properties(${libName} PROPERTIES Fortran_MODULE_DIRECTORY
                                            ${moduleDir})

target_link_libraries(${libName} PUBLIC mom6::mom6
                                        esmf
                                        fms::fms_8
                                        NetCDF::NetCDF_Fortran)

target_include_directories(${libName} PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/MOM6/config_src/dynamic>
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/MOM6/src/framework>)

target_include_directories(${libName} INTERFACE
  $<BUILD_INTERFACE:${moduleDir}>
  $<INSTALL_INTERFACE:include/mom6nuopc>)

if(OpenMP_Fortran_FOUND)
  target_link_libraries(${libName} PUBLIC OpenMP::OpenMP_Fortran)
endif()

# Install compiled Fortran modules
install(DIRECTORY ${moduleDir} DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

# Install libmom6.a and libmom6nuopc.a
install(
  TARGETS ${mom6_lib_targets}
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  COMPONENT Library)
