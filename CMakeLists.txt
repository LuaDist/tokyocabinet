# Copyright (C) 2007-2009 LuaDist.
# Created by Peter Kapec
# Redistribution and use of this file is allowed according to the terms of the MIT license.
# For details see the COPYRIGHT file distributed with LuaDist.
# Please note that the package source code is licensed under its own license.

PROJECT ( tokyocabinet C )
CMAKE_MINIMUM_REQUIRED(VERSION 2.8)
INCLUDE ( dist.cmake )

# Find needed libraries
FIND_PACKAGE(ZLIB REQUIRED)
FIND_PACKAGE(BZip2 REQUIRED)
FIND_PACKAGE(Threads)

INCLUDE_DIRECTORIES(${ZLIB_INCLUDE_DIR} ${BZIP2_INCLUDE_DIR} ${CMAKE_USE_PTHREADS_INIT})

# Build TokyoCabinet library

ADD_DEFINITIONS(--std=gnu99)

ADD_DEFINITIONS(-DNDEBUG -D_GNU_SOURCE=1 -D_REENTRANT -D__EXTENSIONS__)

INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR})

SET(SRC_TOKYO_CABINET tcutil.c tchdb.c tcbdb.c tcfdb.c tctdb.c tcadb.c myconf.c md5.c)

ADD_LIBRARY(tokyocabinet SHARED ${SRC_TOKYO_CABINET} )
TARGET_LINK_LIBRARIES (tokyocabinet ${BZIP2_LIBRARIES} ${ZLIB_LIBRARIES} ${CMAKE_THREAD_LIBS_INIT} m c)


# Build tests

# uff...	is this OK???
SET(APPINC "\"-I${INSTALL_INC}\"")
SET(APPLIBS "\"-L${INSTALL_LIB} -ltokyocabinet -lbz2 -lz -lrt -lpthread -lm -lc\"")

ADD_DEFINITIONS(
	-D_TC_PREFIX="${CMAKE_INSTALL_PREFIX}"
	-D_TC_INCLUDEDIR=\"${INSTALL_INC}\"
	-D_TC_LIBDIR=\"${INSTALL_LIB}\" 
	-D_TC_BINDIR=\"${INSTALL_BIN}\"
	-D_TC_LIBEXECDIR=\"${INSTALL_LIB}\"
	-D_TC_APPINC=${APPINC}
	-D_TC_APPLIBS=${APPLIBS}
)

FOREACH(app   tcucodec tcttest tcumttest tcutest tctmttest  tctmgr tchtest tchmttest tchmgr tcftest tcfmgr tcfmttest tcbmttest tcbmgr tcbtest tcawmgr tcatest tcamgr )
	ADD_EXECUTABLE(${app} ${app}.c)
	TARGET_LINK_LIBRARIES(${app} tokyocabinet)
	
	INSTALL(TARGETS ${app} DESTINATION ${INSTALL_TEST})
ENDFOREACH(app)


# Build examples

FOREACH(example  tcutilex tchdbex tcbdbex tcfdbex tctdbex tcadbex tctchat tctsearch)
	ADD_EXECUTABLE(${example} example/${example}.c)
	TARGET_LINK_LIBRARIES (${example} tokyocabinet)

	INSTALL(TARGETS ${example} DESTINATION ${INSTALL_EXAMPLE})
ENDFOREACH(example)


# Install
INSTALL(FILES tcutil.h tchdb.h tcbdb.h tcfdb.h tctdb.h tcadb.h DESTINATION ${INSTALL_INC})
INSTALL(TARGETS tokyocabinet DESTINATION ${INSTALL_LIB})

INSTALL(FILES COPYING ChangeLog THANKS DESTINATION ${INSTALL_DOC})
INSTALL(DIRECTORY doc DESTINATION ${INSTALL_DOC})

