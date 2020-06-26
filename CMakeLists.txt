# MPI, NetCDF, and ESMF required
find_package(MPI REQUIRED)
find_package(NetCDF REQUIRED Fortran)
find_package(ESMF MODULE REQUIRED)

if(OPENMP)
  find_package(OpenMP REQUIRE COMPONENTS Fortran)
endif()

# MOM6 src, config_src file and include files and directories
include(mom6_files.cmake)

set(lib_name "mom6")
set(module_dir "${CMAKE_CURRENT_BINARY_DIR}/include")

add_library(mom6 STATIC ${mom6_src_files}
                        ${mom6_config_src_files}
                        ${mom6_include_files})
add_library(${lib_name}::${lib_name} ALIAS ${lib_name})

set_target_properties(${lib_name} PROPERTIES
                                  Fortran_MODULE_DIRECTORY "${module_dir}")
set_target_properties(${lib_name} PUBLIC_HEADER "${mom6_include_files}")
target_include_directories(${LIBNAME} INTERFACE
  $<BUILD_INTERFACE:${module_dir}>
  $<INSTALL_INTERFACE:include>)
target_link_libraries(${lib_name} PUBLIC
  esmf
  FMS::FMS
  NetCDF::NetCDF_Fortran)

if(OpenMP_Fortran_FOUND)
  target_link_libraries(${lib_name} PUBLIC OpenMP::OpenMP_Fortran)
endif()

install(DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/${module_dir}
        DESTINATION ${CMAKE_INSTALL_PREFIX})

install(TARGETS ${lib_name}
    LIBRARY DESTINATION lib
    PUBLIC_HEADER DESTINATION include)
