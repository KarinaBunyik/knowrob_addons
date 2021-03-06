cmake_minimum_required(VERSION 2.4.6)
include($ENV{ROS_ROOT}/core/rosbuild/rosbuild.cmake)

rosbuild_init()

rosbuild_include(rosprolog utils)

#######################################
# reset paths in OWL files
rosbuild_find_ros_package(ias_knowledge_base)

set( OWL_PATH_PREFIX ${ias_knowledge_base_PACKAGE_PATH} )
fix_ros_destination_path(OWL_PATH_PREFIX)

file(GLOB inputs owl/*.in)
foreach( _in ${inputs} )
  string(REPLACE ".in" "" _out ${_in})
  configure_file( ${_in} ${_out} )
endforeach( _in )
#file(REMOVE owl/barcoo.owl.in)


rosbuild_find_ros_package(comp_barcoo)
set( LOCAL_PACKAGE_PATH ${comp_barcoo_PACKAGE_PATH} )
fix_ros_destination_path(LOCAL_PACKAGE_PATH)

file(GLOB inputs prolog/*.in)
foreach( _in ${inputs} )
  string(REPLACE ".in" "" _out ${_in})
  configure_file( ${_in} ${_out} )
endforeach( _in )

rosbuild_gensrv()

#set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
#set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)
#set(JAVA_OUTPUT_DIR ${PROJECT_SOURCE_DIR}/bin)


#######################################
# Classpath

add_jar_dir(lib)


# external dependencies in other packages
rosbuild_find_ros_package(knowrob_common)
add_classpath(${knowrob_common_PACKAGE_PATH}/lib/HermiT.jar)
add_runtime_classpath(${knowrob_common_PACKAGE_PATH}/lib/HermiT.jar)
add_classpath(${knowrob_common_PACKAGE_PATH}/lib/owlapi-bin.jar)
add_runtime_classpath(${knowrob_common_PACKAGE_PATH}/lib/owlapi-bin.jar)

rosbuild_find_ros_package(rosjava_jni)
add_classpath(${rosjava_jni_PACKAGE_PATH}/bin)


# generate launch script
rospack_add_java_executable(bin/barcoo_importer   edu.tum.cs.ias.knowrob.comp_barcoo.BarcooImporter)
rospack_add_java_executable(bin/barcoo_subscriber edu.tum.cs.ias.knowrob.comp_barcoo.BarcooSubscriber)
rospack_add_java_executable(bin/barcoo_tester edu.tum.cs.ias.knowrob.comp_barcoo.BarcooCategoryTester)
rospack_add_java_executable(bin/barcoo_annotation_remover edu.tum.cs.ias.knowrob.comp_barcoo.REMOVER)
rospack_add_java_executable(bin/barcoo_location_inferer edu.tum.cs.ias.knowrob.comp_barcoo.BarcooLocationInferer)

add_java_source_dir(${PROJECT_SOURCE_DIR}/src)
