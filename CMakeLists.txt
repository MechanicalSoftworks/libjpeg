cmake_minimum_required(VERSION 3.16)

project(libjpeg
    LANGUAGES C CXX
	VERSION "6.1"
)

set(LIBNAME "jpeg")

function(MakeLibrary libname jsample_bits)
	set(LIBTARGET "${libname}${jsample_bits}")

	set (SOURCE_LIST jcapimin.c jcapistd.c jccoefct.c jccolor.c jcdctmgr.c jchuff.c
		jcinit.c jcmainct.c jcmarker.c jcmaster.c jcomapi.c jcparam.c
		jcphuff.c jcprepct.c jcsample.c jctrans.c jdapimin.c jdapistd.c
		jdatadst.c jdatasrc.c jdcoefct.c jdcolor.c jddctmgr.c jdhuff.c
		jdinput.c jdmainct.c jdmarker.c jdmaster.c jdmerge.c jdphuff.c
		jdpostct.c jdsample.c jdtrans.c jerror.c jfdctflt.c jfdctfst.c
		jfdctint.c jidctflt.c jidctfst.c jidctint.c jidctred.c jquant1.c
		jquant2.c jutils.c jmemmgr.c jmemansi.c
	)

	set (HEADER_LIST jinclude.h jconfig.h jpeglib.h jmorecfg.h jpegint.h jerror.h)

	add_library(${LIBTARGET} STATIC ${SOURCE_LIST} ${HEADER_LIST})
	set_property(TARGET ${LIBTARGET} PROPERTY POSITION_INDEPENDENT_CODE ON)
	set_target_properties(${LIBTARGET} PROPERTIES
		POSITION_INDEPENDENT_CODE ON
		OUTPUT_NAME ${LIBTARGET}
	)
	target_compile_definitions(${LIBTARGET} PUBLIC BITS_IN_JSAMPLE=${jsample_bits})

	if("${jsample_bits}" MATCHES "8")
		set_target_properties(${LIBTARGET} PROPERTIES PUBLIC_HEADER "${HEADER_LIST}")
	endif()

	install(TARGETS ${LIBTARGET}
		EXPORT ${LIBTARGET}Targets
	)

	include(CMakePackageConfigHelpers)
	write_basic_package_version_file(
		${LIBTARGET}ConfigVersion.cmake
		VERSION ${PACKAGE_VERSION}
		COMPATIBILITY ExactVersion
	)

	install(EXPORT ${LIBTARGET}Targets
		FILE ${LIBTARGET}Targets.cmake
		DESTINATION ${CMAKE_INSTALL_CMAKEDIR}/${LIBTARGET}
	)

	configure_file(${PROJECT_SOURCE_DIR}/cmake/Config.cmake.in ${CMAKE_CURRENT_BINARY_DIR}/${LIBTARGET}Config.cmake @ONLY)
	install(FILES "${CMAKE_CURRENT_BINARY_DIR}/${LIBTARGET}Config.cmake"
		"${CMAKE_CURRENT_BINARY_DIR}/${LIBTARGET}ConfigVersion.cmake"
		DESTINATION ${CMAKE_INSTALL_CMAKEDIR}/${LIBTARGET}
	)
endfunction()

MakeLibrary(${LIBNAME} "8")
MakeLibrary(${LIBNAME} "12")

set(CJPEG "cjpeg")
set(DJPEG "djpeg")

add_executable(${CJPEG} cjpeg.c cdjpeg.c transupp.c rdswitch.c rdppm.c rdgif.c rdtarga.c rdbmp.c rdrle.c)
target_link_libraries(${CJPEG} "${LIBNAME}8")
target_compile_definitions(${CJPEG} PUBLIC BITS_IN_JSAMPLE=8)

add_executable(${DJPEG} djpeg.c cdjpeg.c transupp.c rdcolmap.c wrppm.c wrgif.c wrtarga.c wrbmp.c wrrle.c)
target_link_libraries(${DJPEG} "${LIBNAME}8")
target_compile_definitions(${DJPEG} PUBLIC BITS_IN_JSAMPLE=8)

if (MSVC)
	configure_file("jconfig.vc" "${CMAKE_CURRENT_SOURCE_DIR}/jconfig.h" COPYONLY)
else()
	configure_file("jconfig.cfg" "${CMAKE_CURRENT_SOURCE_DIR}/jconfig.h" COPYONLY)
endif()

install(TARGETS ${CJPEG} ${DJPEG})
