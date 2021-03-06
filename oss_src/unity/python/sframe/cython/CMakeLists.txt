project(unity_cython)

include_directories(${NUMPY_INCLUDE_DIRS})

function(cython_add_graphlab_module2 _module_name _sources)
  set_source_files_properties( ${_sources} PROPERTIES CYTHON_IS_CXX TRUE)
  cython_add_module( ${_module_name} ${_sources} )
  set_property(TARGET ${_module_name} APPEND_STRING PROPERTY LINK_FLAGS " -Wl,-rpath,$ORIGIN -Wl,-rpath,$ORIGIN/.. -Wl,-rpath,${CMAKE_CURRENT_BINARY_DIR} ")
  target_link_libraries(${_module_name} boost zeromq ${UNITY_EXTENSION_LIBRARY})
  add_dependencies(${_module_name} release_binaries)
endfunction()


add_custom_target(cython_targets ALL)
file(GLOB cython_sources RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} "*.pyx")
# list(REMOVE_ITEM cython_sources "cy_pylambda_workers.pyx")

foreach(source ${cython_sources})
  set(target "")
  string(REPLACE ".pyx" "" target ${source})
  cython_add_graphlab_module2(${target} ${source})
  add_dependencies(cython_targets ${target})
endforeach()

target_link_libraries(cy_server unity_server)

# Take care of cy_pylambda_workers explicitly. 
# set_source_files_properties("cy_pylambda_workers.pyx" PROPERTIES CYTHON_IS_CXX TRUE)
# cython_add_module(cy_pylambda_workers "cy_pylambda_workers.pyx")
# set_property(TARGET cy_pylambda_workers APPEND_STRING PROPERTY LINK_FLAGS " -Wl,-rpath,$ORIGIN -Wl,-rpath,$ORIGIN/.. -Wl,-rpath,${CMAKE_CURRENT_BINARY_DIR} ")
# target_link_libraries(cy_pylambda_workers pylambda_worker)
# add_dependencies(cy_pylambda_workers release_binaries)




ADD_CUSTOM_TARGET (oss_clean_cython)
ADD_CUSTOM_COMMAND(
  COMMENT "clean cython"
  COMMAND
  rm -f *.so *.dbg *.a *.o *.dll *.pyd *.obj *.cxx *.py *.pyc
  WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
  TARGET oss_clean_cython
)
if(WIN32)
  make_copy_target(cython_deps ALL
    TARGETS
    ${UNITY_EXTENSION_LIBRARY}
    unity_server
    unity_toolkits_shared
    FILES
    ${INSTALLATION_BINARY_FILES}
    ${INSTALLATION_SYSTEM_BINARY_FILES}
    )
endif()

