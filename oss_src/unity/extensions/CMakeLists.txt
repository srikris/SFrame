project(unity)
set(EXTENSIONS_LIST)
macro(make_extension NAME)
  list(APPEND EXTENSIONS_LIST "${NAME}")
  message(STATUS "Registering Extension: " ${NAME})
  make_library(${NAME} ${ARGV} REQUIRES unity boost SHARED)
endmacro()
#----------------------------
make_extension(internal_demo SOURCES internal_demo.cpp)
make_extension(additional_sframe_utilities SOURCES additional_sframe_utilities.cpp)
make_extension(random_sframe_generation SOURCES random_sframe_generation.cpp)
make_extension(grouped_sframe SOURCES grouped_sframe.cpp)

#----------------------------
set_property(DIRECTORY PROPERTY EXTENSIONS_LIST "${EXTENSIONS_LIST}")
