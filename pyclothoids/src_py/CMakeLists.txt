cmake_minimum_required(VERSION 3.14)
project(G2lib)

# Configurazioni di base
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_VERBOSE_MAKEFILE ON)

# Trova dipendenze
find_package(pybind11 REQUIRED)
find_package(Eigen3 3.3 REQUIRED)

# Path sorgenti Clothoids
set(CLOTHOIDS_SRC_DIR "${CMAKE_SOURCE_DIR}/src/Clothoids/src")

# File sorgente del wrapper Python
set(G2LIB_SOURCES
    "${CMAKE_CURRENT_SOURCE_DIR}/src/python-Triangle2D.cc"
    "${CMAKE_CURRENT_SOURCE_DIR}/src/python-LineSegment.cc"
    "${CMAKE_CURRENT_SOURCE_DIR}/src/python-G2lib.cc"
    "${CMAKE_CURRENT_SOURCE_DIR}/src/python-Clothoid.cc"
    "${CMAKE_CURRENT_SOURCE_DIR}/src/python-CircleArc.cc"
    "${CMAKE_CURRENT_SOURCE_DIR}/src/python-Biarc.cc"
    "${CMAKE_CURRENT_SOURCE_DIR}/src/python-BaseCurve.cc"
    "${CMAKE_CURRENT_SOURCE_DIR}/src/python-AABBtree.cc"
    "${CMAKE_CURRENT_SOURCE_DIR}/src/python-ClothoidSpline-Solver.cc"
    "${CLOTHOIDS_SRC_DIR}/Clothoid.cc"  # File sorgente originale
    "${CLOTHOIDS_SRC_DIR}/AABBtree.cc"  # File sorgente originale
)

# Crea modulo Python
pybind11_add_module(${PROJECT_NAME} 
    ${G2LIB_SOURCES}
)

# Include directories
target_include_directories(${PROJECT_NAME} PRIVATE
    "${CMAKE_CURRENT_SOURCE_DIR}/include"
    "${CLOTHOIDS_SRC_DIR}"
    ${pybind11_INCLUDE_DIRS}
    ${Eigen3_INCLUDE_DIRS}
)

# Opzioni di compilazione
if(MSVC)
    target_compile_options(${PROJECT_NAME} PRIVATE
        /std:c++17
        /EHsc
        /bigobj
        /DWIN32
        /D_WINDOWS
    )
else()
    target_compile_options(${PROJECT_NAME} PRIVATE
        -Wall
        -Wextra
        -Wpedantic
    )
endif()

# Linker options
if(IPOPT_FOUND)
    target_compile_definitions(${PROJECT_NAME} PRIVATE 
        IPOPT_CLOTHOID_SPLINE
        HAVE_CSTDDEF
    )
    target_link_libraries(${PROJECT_NAME} PRIVATE ${IPOPT_LIBRARIES})
endif()

# Configurazione Eigen/LM
target_compile_definitions(${PROJECT_NAME} PRIVATE 
    LMSOLVE_CLOTHOID_SPLINE
)
