cmake_minimum_required(VERSION 3.21)
project(VTKJuliaWrap LANGUAGES CXX)

find_package(JlCxx REQUIRED)
find_package(VTK 9.7 REQUIRED COMPONENTS
            CommonCore
            CommonDataModel
            CommonExecutionModel
            FiltersCore
            FiltersGeneral
            FiltersFlowPaths
            FiltersSources
            FiltersGeometry
            IOXML
            IOEnSight
            IOCGNSReader
            IOLegacy
)

get_target_property(JlCxx_location JlCxx::cxxwrap_julia LOCATION)
get_filename_component(JlCxx_location "${JlCxx_location}" DIRECTORY)

set(CMAKE_INSTALL_RPATH
    "${CMAKE_INSTALL_PREFIX}/lib;${JlCxx_location}"
)

add_library(vtk_julia SHARED
            src/cpp/jlVTKGen.cxx
            src/cpp/JlClasses.cxx
            src/cpp/shim_vtkjl.cpp
            src/cpp/shim_auto.cpp
)

target_include_directories(vtk_julia PRIVATE src/cpp)
target_compile_features(vtk_julia PRIVATE cxx_std_17)

target_link_libraries(vtk_julia PRIVATE
                      JlCxx::cxxwrap_julia
                      JlCxx::cxxwrap_julia_stl
                      VTK::CommonCore
                      VTK::CommonDataModel
                      VTK::CommonExecutionModel
                      VTK::FiltersCore
                      VTK::FiltersGeneral
                      VTK::FiltersFlowPaths
                      VTK::FiltersSources
                      VTK::FiltersGeometry
                      VTK::IOXML
                      VTK::IOEnSight
                      VTK::IOCGNSReader
                      VTK::IOLegacy
)

install(TARGETS vtk_julia
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib
        RUNTIME DESTINATION lib
)

install(FILES src/jl/VTKGen.jl DESTINATION .)
