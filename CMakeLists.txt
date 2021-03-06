# CMakeList.txt : CMake project for MonetDLiteJava, include source and define
# project specific logic here.
#
cmake_minimum_required (VERSION 3.0)

project(MonetDBLite-Java VERSION "1.0.0" LANGUAGES C)
set(C_STANDARD_REQUIRED ON)
set(CMAKE_C_STANDARD 99)

set(CMAKE_MODULE_PATH "${MONETDBLITE_SOURCE_DIR}/cmake" CACHE INTERNAL "Location of custom CMake modules")
if(NOT CMAKE_BUILD_TYPE) # Set default build options
	message(STATUS "Setting build type to Debug as none was selected")
	set(CMAKE_BUILD_TYPE Debug CACHE STRING "The type of build" FORCE)
endif()
include(BuildMacros REQUIRED)

if(WIN32)
	set(OBJECT_EXTENSION "obj")
	set(COMPILER_OPTION "/")
else()
	set(OBJECT_EXTENSION "o")
	set(COMPILER_OPTION "-")
endif()

add_definitions(${COMPILER_OPTION}DLIBGDK ${COMPILER_OPTION}DLIBMONETDB5 ${COMPILER_OPTION}DLIBSQL
				${COMPILER_OPTION}DLIBEMBEDDED ${COMPILER_OPTION}DHAVE_EMBEDDED)

if (MSVC)
	add_definitions(/D_CRT_SECURE_NO_WARNINGS)
else()
	if(CMAKE_VERSION VERSION_LESS 3.1)
		set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=c99")
	endif()
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -W -Wall -Wextra -Werror")
endif()

set_system_libraries()
# Replace backslashes on Windows
string(REPLACE "\\" "/" MONETDBLITE_OUTPUT_DIR_TRIMMED "${MONETDBLITE_OUTPUT_DIR}")
FILE(GLOB_RECURSE MONETDBLITE_OBJECTS "${MONETDBLITE_OUTPUT_DIR_TRIMMED}/*.${OBJECT_EXTENSION}")

include_directories("${CMAKE_SOURCE_DIR}/src" "${MONETDBLITE_OUTPUT_DIR}/include/monetdb")

add_library(monetdblitejava MODULE
			${MONETDBLITE_OBJECTS}
			"${CMAKE_SOURCE_DIR}/src/converters.c"
			"${CMAKE_SOURCE_DIR}/src/checknulls.c"
			"${CMAKE_SOURCE_DIR}/src/javaids.c"
			"${CMAKE_SOURCE_DIR}/src/jresultset.c"
			"${CMAKE_SOURCE_DIR}/src/nl_cwi_monetdb_embedded_env_MonetDBEmbeddedConnection.c"
			"${CMAKE_SOURCE_DIR}/src/nl_cwi_monetdb_embedded_env_MonetDBEmbeddedDatabase.c"
			"${CMAKE_SOURCE_DIR}/src/nl_cwi_monetdb_embedded_env_MonetDBEmbeddedPreparedStatement.c"
			"${CMAKE_SOURCE_DIR}/src/nl_cwi_monetdb_embedded_jdbc_EmbeddedDataBlockResponse.c"
			"${CMAKE_SOURCE_DIR}/src/nl_cwi_monetdb_embedded_jdbc_JDBCEmbeddedConnection.c"
			"${CMAKE_SOURCE_DIR}/src/nl_cwi_monetdb_embedded_mapping_NullMappings.c"
			"${CMAKE_SOURCE_DIR}/src/nl_cwi_monetdb_embedded_resultset_QueryResultSet.c"
			"${CMAKE_SOURCE_DIR}/src/nl_cwi_monetdb_embedded_tables_MonetDBTable.c")

target_link_libraries(monetdblitejava PRIVATE ${MATH_LIBRARIES} ${THREAD_LIBRARIES} ${DL_LIBRARIES} ${SOCKET_LIBRARIES})
