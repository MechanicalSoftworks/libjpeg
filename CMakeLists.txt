cmake_minimum_required(VERSION 3.17)

project(libjpeg
    LANGUAGES C CXX)
	
OPTION(ENABLE_12_BIT_SUPPORT "Build with support for 12-bit images. Mutally exclusive with 8-bit support." OFF)

if(NOT ${CMAKE_SIZEOF_VOID_P} EQUAL 8)
    message("Please switch to x64 build.")
    return()
endif()

set (LIBSOURCES jcapimin.c jcapistd.c jccoefct.c jccolor.c jcdctmgr.c jchuff.c
	jcinit.c jcmainct.c jcmarker.c jcmaster.c jcomapi.c jcparam.c
	jcphuff.c jcprepct.c jcsample.c jctrans.c jdapimin.c jdapistd.c
	jdatadst.c jdatasrc.c jdcoefct.c jdcolor.c jddctmgr.c jdhuff.c
	jdinput.c jdmainct.c jdmarker.c jdmaster.c jdmerge.c jdphuff.c
	jdpostct.c jdsample.c jdtrans.c jerror.c jfdctflt.c jfdctfst.c
	jfdctint.c jidctflt.c jidctfst.c jidctint.c jidctred.c jquant1.c
	jquant2.c jutils.c jmemmgr.c jmemansi.c jinclude.h jconfig.h 
	jpeglib.h jmorecfg.h jpegint.h jerror.h
)

set (CJPEGSOURCES cjpeg.c cdjpeg.c transupp.c rdswitch.c rdppm.c rdgif.c rdtarga.c rdbmp.c rdrle.c)
set (DJPEGSOURCES djpeg.c cdjpeg.c transupp.c rdcolmap.c wrppm.c wrgif.c wrtarga.c wrbmp.c wrrle.c)

add_library(libjpeg STATIC ${LIBSOURCES})

add_executable(cjpeg ${CJPEGSOURCES})
target_link_libraries(cjpeg	libjpeg)

add_executable(djpeg ${DJPEGSOURCES})
target_link_libraries(djpeg	libjpeg)

if (ENABLE_12_BIT_SUPPORT)
	add_compile_definitions(BITS_IN_JSAMPLE=12)
else()
	add_compile_definitions(BITS_IN_JSAMPLE=8)
endif()

if (MSVC)
	configure_file("jconfig.vc" "${CMAKE_CURRENT_SOURCE_DIR}/jconfig.h" COPYONLY)
else()
	configure_file("jconfig.cfg" "${CMAKE_CURRENT_SOURCE_DIR}/jconfig.h" COPYONLY)
endif()
