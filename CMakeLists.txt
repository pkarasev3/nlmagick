cmake_minimum_required(VERSION 2.8)
project(nlmagick)

enable_testing()

# search for Boost version
find_package( Boost COMPONENTS system filesystem program_options )

link_directories ( ${Boost_LIBRARY_DIRS} )
include_directories ( ${Boost_INCLUDE_DIRS} )

#set flags so that we have awesome compile time errors on all warnings
#notice the -Wl,--no-undefined helps with making sure all symbols are found during
#link time.
set(CMAKE_C_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wl,--no-undefined -fopenmp")

#   -Werror=unused-but-set-variable
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}  -Wall -Wl,--no-undefined -fopenmp")
set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -DBOOST_DISABLE_ASSERTS")

#set the default path for built executables to the "bin" directory
set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR}/bin)
#set the default path for built libraries to the "lib" directory
set(LIBRARY_OUTPUT_PATH ${CMAKE_BINARY_DIR}/lib)

# Macro for creating library ${name}:
# 1) put relative-path  .cpp and .h files into a lib
# 2) include and link external libs by what's in ./include/lib_name
macro(define_module name)

  project(${name})
  include_directories( ${EXTERNAL_INCLUDES} )
  include_directories( ${CMAKE_CURRENT_SOURCE_DIR}/include )
  include_directories( ${CMAKE_CURRENT_SOURCE_DIR}/src )

  file(GLOB lib_srcs "src/*.cpp" "src/*.c")
  file(GLOB lib_int_hdrs "src/*.h*")
  file(GLOB lib_ext_hdrs "include/${name}/*.h*" "include/*.h*")

  add_library(${name} ${${name}_LIB_TYPE} ${lib_srcs} ${lib_int_hdrs} ${lib_ext_hdrs} )
  target_link_libraries(${name} ${EXTERNAL_LIBS})

  set(${name}_INCLUDE_DIRS ${CMAKE_CURRENT_SOURCE_DIR}/include)
  set(${name}_LIBS ${name})

endmacro()

define_module(nlmagick)

add_subdirectory(samples)

add_subdirectory(unittest)

