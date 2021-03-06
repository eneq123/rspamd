# Librspamdserver
SET(LIBRSPAMDSERVERSRC
				buffer.c
				cfg_utils.c
				cfg_rcl.c
				dkim.c
				dns.c
				dynamic_cfg.c
				events.c
				fuzzy_backend.c
				html.c
				protocol.c
				proxy.c
				roll_history.c
				spf.c
				symbols_cache.c
				task.c
				url.c
				worker_util.c)

# Librspamd-server

#IF(WITH_DB)
#	LIST(APPEND LIBRSPAMDSERVERSRC kvstorage_bdb.c)
#ENDIF(WITH_DB)
#IF(WITH_SQLITE)
#	LIST(APPEND LIBRSPAMDSERVERSRC kvstorage_sqlite.c)
#ENDIF(WITH_SQLITE)
				
ADD_LIBRARY(rspamd-server ${LINK_TYPE} ${LIBRSPAMDSERVERSRC})
IF(NOT DEBIAN_BUILD)
	SET_TARGET_PROPERTIES(rspamd-server PROPERTIES VERSION ${RSPAMD_VERSION})
ENDIF(NOT DEBIAN_BUILD)
SET_TARGET_PROPERTIES(rspamd-server PROPERTIES LINKER_LANGUAGE C COMPILE_FLAGS "-DRSPAMD_LIB")
TARGET_LINK_LIBRARIES(rspamd-server rspamd-lua)
TARGET_LINK_LIBRARIES(rspamd-server rspamd-cdb)
TARGET_LINK_LIBRARIES(rspamd-server rspamd-util)
TARGET_LINK_LIBRARIES(rspamd-server sqlite3)
TARGET_LINK_LIBRARIES(rspamd-server rdns)   
IF(CMAKE_COMPILER_IS_GNUCC)
SET_TARGET_PROPERTIES(rspamd-server PROPERTIES COMPILE_FLAGS "-DRSPAMD_LIB -fno-strict-aliasing")
ENDIF(CMAKE_COMPILER_IS_GNUCC)

IF(WITH_DB)
	TARGET_LINK_LIBRARIES(rspamd-server db)
ENDIF(WITH_DB)

IF(OPENSSL_FOUND)
	TARGET_LINK_LIBRARIES(rspamd-server ${OPENSSL_LIBRARIES})
ENDIF(OPENSSL_FOUND)

IF(NO_SHARED MATCHES "OFF")
	INSTALL(TARGETS rspamd-server 
    	LIBRARY DESTINATION ${LIBDIR} 
    	PUBLIC_HEADER DESTINATION ${INCLUDEDIR})
ENDIF(NO_SHARED MATCHES "OFF")
