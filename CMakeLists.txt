# Copyright (C) 2007-2013 LuaDist.
# Created by Peter Kapec
# Redistribution and use of this file is allowed according to the terms of the MIT license.
# For details see the COPYRIGHT file distributed with LuaDist.
# Please note that the package source code is licensed under its own license.

project ( tokyocabinet C )
cmake_minimum_required ( VERSION 2.8 )
include ( cmake/dist.cmake )

# Find needed libraries
find_package ( ZLIB REQUIRED )
find_package ( BZip2 REQUIRED )
find_package ( Threads )

include_directories ( ${ZLIB_INCLUDE_DIR} ${BZIP2_INCLUDE_DIR} ${CMAKE_USE_PTHREADS_INIT} )

# Build TokyoCabinet library

add_definitions ( --std=gnu99 )

add_definitions ( -DNDEBUG -D_GNU_SOURCE=1 -D_REENTRANT -D__EXTENSIONS__ )

include_directories ( ${CMAKE_CURRENT_SOURCE_DIR} )

set ( SRC_TOKYO_CABINET tcutil.c tchdb.c tcbdb.c tcfdb.c tctdb.c tcadb.c myconf.c md5.c )

add_library ( tokyocabinet ${SRC_TOKYO_CABINET} )
target_link_libraries ( tokyocabinet ${BZIP2_LIBRARIES} ${ZLIB_LIBRARIES} ${CMAKE_THREAD_LIBS_INIT} m c )

# Build tests

# uff...	is this OK???
set ( APPINC "\"-I${INSTALL_INC}\"" )
set ( APPLIBS "\"-L${INSTALL_LIB} -ltokyocabinet -lbz2 -lz -lrt -lpthread -lm -lc\"" )

add_definitions ( -D_TC_PREFIX="${CMAKE_INSTALL_PREFIX}" -D_TC_INCLUDEDIR=\"${INSTALL_INC}\" 
  -D_TC_LIBDIR=\"${INSTALL_LIB}\" -D_TC_BINDIR=\"${INSTALL_BIN}\" -D_TC_LIBEXECDIR=\"${INSTALL_LIB}\" 
  -D_TC_APPINC=${APPINC} -D_TC_APPLIBS=${APPLIBS} )

 foreach(FILE_NAME 
	tcamttest.c
	tcatest.c
	tcbmttest.c
	tcbtest.c
	tcfmttest.c
	tcftest.c
	tchmttest.c
	tchtest.c
	tctmttest.c
	tcttest.c
	tcumttest.c
	tcutest.c
	)
 	get_filename_component( NAME ${FILE_NAME} NAME_WE )
 	add_executable( ${NAME} ${FILE_NAME} )
	target_link_libraries ( ${NAME} tokyocabinet )
	install ( TARGETS ${NAME} DESTINATION ${INSTALL_TEST} COMPONENT Test )
endforeach(FILE_NAME)


# Build examples
foreach(FILE_NAME tcutilex tchdbex tcbdbex tcfdbex tctdbex tcadbex)
	add_executable( ${FILE_NAME} example/${FILE_NAME}.c )
	target_link_libraries ( ${FILE_NAME} tokyocabinet )
	install ( TARGETS ${FILE_NAME} DESTINATION ${INSTALL_EXAMPLE} COMPONENT Example )
endforeach(FILE_NAME)

# Install
install_header ( tcutil.h tchdb.h tcbdb.h tcfdb.h tctdb.h tcadb.h )
install_library ( tokyocabinet )
install_data ( COPYING ChangeLog )
install_doc ( doc/ )
