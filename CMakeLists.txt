set(TARGET_NAME "jpeg-6b")

message("### ${TARGET_NAME}")

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
set_target_properties(libjpeg PROPERTIES FOLDER "thirdparty/libjpeg")

add_executable(cjpeg ${CJPEGSOURCES})
target_link_libraries(cjpeg	libjpeg)
set_target_properties(cjpeg PROPERTIES FOLDER "thirdparty/libjpeg")
copy_target_to_bin(cjpeg)

add_executable(djpeg ${DJPEGSOURCES})
target_link_libraries(djpeg	libjpeg)
set_target_properties(djpeg PROPERTIES FOLDER "thirdparty/libjpeg")
copy_target_to_bin(djpeg)

if (MSVC)
	configure_file("jconfig.vc" "${CMAKE_CURRENT_SOURCE_DIR}/jconfig.h" COPYONLY)
else()
	configure_file("jconfig.cfg" "${CMAKE_CURRENT_SOURCE_DIR}/jconfig.h" COPYONLY)
endif()
