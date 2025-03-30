cmake_minimum_required(VERSION 3.14)
project(pyclothoids)

# Configurazioni base
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Trova pybind11
find_package(pybind11 REQUIRED)

# Trova Eigen3
find_package(Eigen3 REQUIRED)

# Path sorgenti Clothoids
set(CLOTHOIDS_SRC_DIR "${CMAKE_CURRENT_SOURCE_DIR}/src/Submodules/Clothoids")

# Lista file sorgente principali
set(SOURCES
    "${CLOTHOIDS_SRC_DIR}/AABBtree.cc"
    "${CLOTHOIDS_SRC_DIR}/Clothoid.cc"
    "${CLOTHOIDS_SRC_DIR}/ClothoidDistance.cc"
    # Aggiungi altri file .cc necessari
)

# Crea modulo Python
pybind11_add_module(G2lib
    src_py/_clothoids_cpp.cpp 
    ${SOURCES}
)

# Include directories
target_include_directories(G2lib PRIVATE
    ${pybind11_INCLUDE_DIRS}
    ${EIGEN3_INCLUDE_DIRS}
    "${CLOTHOIDS_SRC_DIR}"
)

# Opzioni specifiche per Windows
if(MSVC)
    target_compile_options(G2lib PRIVATE 
        /std:c++17 
        /EHsc
        /bigobj
    )
    target_link_options(G2lib PRIVATE /DEBUG)
endif()
