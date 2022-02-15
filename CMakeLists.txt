cmake_minimum_required (VERSION 3.8)
if (UNIX)
  add_definitions(-Wno-sign-compare -Wno-enum-compare)
endif(UNIX)
project(restserver)
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/build")
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
option(DBMS "Dbms" OFF)
include_directories("${CMAKE_SOURCE_DIR}")

include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup(TARGETS)
set(LibEugeneCore_SRC
		main.cpp
		src/handler.cpp)

add_library(LibEugeneCore "${LibEugeneCore_SRC}")
set_target_properties(LibEugeneCore PROPERTIES LINKER_LANGUAGE CXX)
target_include_directories(LibEugeneCore PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}")
target_link_libraries(LibEugeneCore
		CONAN_PKG::fmt
		CONAN_PKG::ms-gsl
		CONAN_PKG::catch2
		CONAN_PKG::spdlog
		CONAN_PKG::cpprestsdk)

add_executable(restserver
  main.cpp
  src/handler.cpp
  )

#set(Casablanca_LIBRARIES "-lboost_system -lcrypto -lssl -lcpprest")
#set(LINKING_LIBRARIES ${Casablanca_LIBRARIES})

#if(DBMS)
#	set(NEW_DB_SRC_DIR ${CMAKE_SOURCE_DIR}/dbms/src)
#	add_library(dbms SHARED
#		${NEW_DB_SRC_DIR}/Dbms.cpp
#		)
#	set(Dbms_dep_lib "-lpqxx -lpq")
#	set(LINKING_LIBRARIES ${LINKING_LIBRARIES} dbms ${Dbms_dep_lib})
#endif(DBMS)
#
#if (UNIX)
#	# Linking POSIX threads
#	find_package(Threads REQUIRED)
#	set(LINKING_LIBRARIES ${LINKING_LIBRARIES} Threads::Threads)
#endif (UNIX)

target_link_libraries(restserver PRIVATE LibEugeneCore)
