include_directories(${CMAKE_SOURCE_DIR}/common ${ZLIB_INCLUDE_DIRS})

add_library(rdr STATIC
    Exception.cxx 
    FdInStream.cxx 
    FdOutStream.cxx
    HexInStream.cxx
    HexOutStream.cxx
    InStream.cxx
    RandomStream.cxx
    ZlibInStream.cxx
    ZlibOutStream.cxx
    )

set(RDR_LIBRARIES ${ZLIB_LIBRARIES})
if(WIN32)
	set(RDR_LIBRARIES ${RDR_LIBRARIES} ws2_32)
endif()

target_link_libraries(rdr ${RDR_LIBRARIES})

if(UNIX)
  libtool_create_control_file(rdr)
endif()