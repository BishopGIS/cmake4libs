project ( libiconv )
cmake_minimum_required ( VERSION 2.8 )

set(CMAKE_CONFIGURATION_TYPES "Debug;Release" CACHE STRING "Configs" FORCE)
set(CMAKE_COLOR_MAKEFILE ON)

# Options
option ( ENABLE_EXTRA "Enable a few rarely used encodings" OFF)
option ( ENABLE_NLS "Translation of program messages to the user's native language is requested" OFF)
option ( ENABLE_RELOCATABLE "The package shall run at any location in the file system" ON )



# set path to additional CMake modules
set(CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake ${CMAKE_MODULE_PATH})

# uninstall target
configure_file(${CMAKE_MODULE_PATH}/cmake_uninstall.cmake.in ${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake IMMEDIATE @ONLY)

include ( configure )

# iconv.h
set ( BROKEN_WCHAR_H 0 )

if(${HAVE_MBSTATE_T})
    set ( USE_MBSTATE_T 1 )
else()
    set ( USE_MBSTATE_T 0 )
endif()

if (MSVC)
    set(HAVE_VISIBILITY 0)
endif()

configure_file ( ${CMAKE_MODULE_PATH}/config.h.cmake ${CMAKE_CURRENT_BINARY_DIR}/config.h )
configure_file ( ${CMAKE_MODULE_PATH}/iconv.h.cmake ${CMAKE_CURRENT_BINARY_DIR}/iconv.h )
configure_file ( ${CMAKE_SOURCE_DIR}/libcharset/include/libcharset.h.in ${CMAKE_CURRENT_BINARY_DIR}/libcharset.h )
configure_file ( ${CMAKE_SOURCE_DIR}/srclib/uniwidth.in.h ${CMAKE_CURRENT_BINARY_DIR}/uniwidth.h )
configure_file ( ${CMAKE_SOURCE_DIR}/srclib/unitypes.in.h ${CMAKE_CURRENT_BINARY_DIR}/unitypes.h )

# Dirty fix for MinGW
if ( MINGW )
  add_definitions ( -DELOOP=0 -DHAVE_DECL_STRERROR_R=0 )
  configure_file ( srclib/alloca.in.h ${CMAKE_CURRENT_BINARY_DIR}/srclib/alloca.h )
endif ()

if (MSVC)
    add_definitions ("-D_CRT_NONSTDC_NO_DEPRECATE -D_UNICODE -DUNICODE -D_CRT_SECURE_NO_WARNINGS -D_CRT_NON_CONFORMING_SWPRINTFS -D_CRT_SECURE_NO_DEPRECATE -D_WIN32_WINNT=0x501 -DWINVER=0x501 -D_LIB -DLIBDIR")
    add_definitions (-DPACKAGE_VERSION_MAJOR=1)
    add_definitions (-DPACKAGE_VERSION_MINOR=14) 
    add_definitions (-DPACKAGE_VERSION_SUBMINOR=0) 
    add_definitions (-DPACKAGE_VERSION_STRING="libiconv-1.14")
endif()

include_directories ( ${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_CURRENT_BINARY_DIR} include srclib )
add_definitions ( -DHAVE_CONFIG_H -DNO_XMALLOC -DBUILDING_DLL -DIN_LIBRARY -DLIBICONV_PLUG)

if(${ENABLE_RELOCATABLE})
    add_definitions ( -Dset_relocation_prefix=libcharset_set_relocation_prefix -Drelocate=libcharset_relocate -DENABLE_RELOCATABLE=1 )
else()
    add_definitions ( -DENABLE_RELOCATABLE=0 )
endif()

# libcharset
# option ( BUILD_LIBCHARSET "Build libcharset" OFF)
# if(BUILD_LIBCHARSET)

    # set ( SRC_LIBCHARSET
      # libcharset/lib/localcharset.c
      # libcharset/lib/relocatable.c
    # )

    # add_library ( charset ${SRC_LIBCHARSET} )
    # #set_target_properties ( charset PROPERTIES COMPILE_FLAGS -DBUILDING_LIBCHARSET)
    # add_definitions(-DBUILDING_LIBCHARSET)
# endif()

# libicrt
# option ( BUILD_LIBICRT "Build libicrt" OFF)
# if(BUILD_LIBICRT)
    # set ( SRC_LIBICRT 
      # srclib/allocator.c
      # srclib/areadlink.c
      # srclib/careadlinkat.c
      # srclib/malloca.c
      # srclib/progname.c
      # srclib/safe-read.c
      # srclib/uniwidth/width.c
      # srclib/xmalloc.c
      # srclib/xstrdup.c
      # srclib/xreadlink.c
      # srclib/canonicalize-lgpl.c
      # srclib/error.c
      # srclib/lstat.c
      # srclib/readlink.c
      # srclib/stat.c
      # srclib/strerror.c
      # srclib/strerror-override.c  
    # )
    # add_library ( icrt STATIC ${SRC_LIBICRT} )
    # add_definitions(-DBUILDING_LIBICRT)
# endif()

# libiconv
option ( BUILD_LIBICONV "Build libiconv" ON)
if(BUILD_LIBICONV)
    set ( SRC_LIBICONV
        lib/iconv.c
        libcharset/lib/localcharset.c
        lib/relocatable.c
        lib/relocatable.h
        lib/encodings.def
        lib/encodings_aix.def
        lib/encodings_dos.def
        lib/encodings_extra.def
        lib/encodings_local.def
        lib/encodings_osf1.def
        lib/translit.def
    )
    
    if(WIN32)
        set ( SRC_LIBICONV ${SRC_LIBICONV}
            windows/libiconv.rc
        )
    endif(WIN32)

option(ICONV_BUILD_SHARED "Set ON to build shared library" ON)
if(ICONV_BUILD_SHARED)
	add_library(libiconv SHARED ${SRC_LIBICONV})
else(ICONV_BUILD_SHARED)
	add_library(libiconv STATIC ${SRC_LIBICONV}) 
endif(ICONV_BUILD_SHARED)
    
    #target_link_libraries ( iconv charset )
    #set_target_properties ( iconv PROPERTIES COMPILE_FLAGS -DBUILDING_LIBICONV -DBUILDING_LIBCHARSE)
    add_definitions(-DBUILDING_LIBICONV -DBUILDING_LIBCHARSET)
    
    set_target_properties(libiconv
        PROPERTIES PROJECT_LABEL ${PROJECT_NAME}
        VERSION 1.14
        SOVERSION 1
        ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
        LIBRARY_OUTPUT_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
        RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR})    
        
    if(WIN32)
        if(CMAKE_CL_64)
            install(TARGETS libiconv
                RUNTIME DESTINATION bin
                ARCHIVE DESTINATION lib/x64
                LIBRARY DESTINATION lib/x64)
        else(CMAKE_CL_64)
            install(TARGETS libiconv
                RUNTIME DESTINATION bin
                ARCHIVE DESTINATION lib/x86
                LIBRARY DESTINATION lib/x86)
        endif(CMAKE_CL_64)
        install(FILES ${CMAKE_CURRENT_BINARY_DIR}/iconv.h DESTINATION include)
    else(WIN32)
        install(TARGETS libiconv
            RUNTIME DESTINATION bin
            ARCHIVE DESTINATION lib/gdal
            LIBRARY DESTINATION lib/gdal)
        install(FILES ${CMAKE_CURRENT_BINARY_DIR}/iconv.h DESTINATION include)
    endif(WIN32) 

endif(BUILD_LIBICONV)

# option ( BUILD_ICONV "Build iconv util" OFF)
# if(BUILD_ICONV)
    # add_executable ( iconvcli src/iconv_no_i18n.c )
    # target_link_libraries ( iconvcli iconv icrt charset )
    # set_target_properties ( iconvcli PROPERTIES OUTPUT_NAME iconv )
    # install_executable ( iconvcli )
# endif(BUILD_ICONV)   
    
add_custom_target(uninstall COMMAND ${CMAKE_COMMAND} -P ${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake)
