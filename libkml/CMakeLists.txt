cmake_minimum_required (VERSION 2.6)
# ----------------------------------------------------------------------------
#  CMake file for libkml. See root CMakeLists.txt
#
# ----------------------------------------------------------------------------
project(kml)

set(CMAKE_CONFIGURATION_TYPES "Debug;Release" CACHE STRING "Configs" FORCE)
set(CMAKE_COLOR_MAKEFILE ON)

# set(CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake ${CMAKE_MODULE_PATH})
# include(common)

include_directories(${CMAKE_CURRENT_SOURCE_DIR})
include_directories(${CMAKE_CURRENT_BINARY_DIR})
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/src)

# uninstall target
configure_file(
    "${CMAKE_CURRENT_SOURCE_DIR}/cmake_uninstall.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake"
    IMMEDIATE @ONLY)

if(WIN32)
    set(LIB_INSTALL_PATH ${CMAKE_CURRENT_SOURCE_DIR}/)
elseif(UNIX)
    set(LIB_INSTALL_PATH /usr/)
endif()

# needed packages
find_package(ZLIB REQUIRED)
if(ZLIB_FOUND)
    include_directories(${ZLIB_INCLUDE_DIRS})
    #link_directories(${ZLIB_LIBRARIES})
    add_definitions(-DHAVE_ZLIB_H -DHAVE_ZLIB -DHAVE_LIBZ)
    add_definitions(-DZIP_SUPPORT)
    if(MSVC)        
        add_definitions(-DZLIB_DLL -DZLIB_INTERNAL)
    endif(MSVC)
endif(ZLIB_FOUND)

find_package(Boost REQUIRED)    #COMPONENTS system thread regex program
if(Boost_FOUND)
    include_directories(${Boost_INCLUDE_DIRS})
    add_definitions(-DHAVE_BOOST)
endif(Boost_FOUND)

find_package(EXPAT REQUIRED)
if(EXPAT_FOUND)
    include_directories(${EXPAT_INCLUDE_DIRS})
    add_definitions(-DHAVE_EXPAT)
endif(EXPAT_FOUND)

set(OGR_KMLHHEADERS
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/attributes.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/color32.h 
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/csv_splitter.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/date_time.h 
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/expat_handler.h 
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/expat_handler_ns.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/expat_parser.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/file.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/math_util.h 
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/memory_file.h 
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/mimetypes.h 
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/net_cache.h 
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/referent.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/string_util.h 
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/tempfile.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/time_util.h 
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/uri_parser.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/util.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/vec3.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/version.h 
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/xmlns.h 
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/xml_element.h 
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/xml_file.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/xml_namespaces.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/zip_file.h
)
source_group("Header Files\\base" FILES ${OGR_KMLHHEADERS})
    
set(OGR_KMLCSOURCES
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/attributes.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/csv_splitter.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/date_time.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/expat_handler_ns.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/expat_parser.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/file.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/math_util.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/mimetypes.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/referent.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/string_util.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/time_util.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/uri_parser.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/version.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/xml_namespaces.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/zip_file.cc
)

source_group("Source Files\\base" FILES ${OGR_KMLCSOURCES})
        
if(WIN32)
    set(OGR_KMLCSOURCES ${OGR_KMLCSOURCES} ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/file_win32.cc)
else(WIN32)
    set(OGR_KMLCSOURCES ${OGR_KMLCSOURCES} ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/file_posix.cc)
endif(WIN32)  

set(lib_hdrs ${lib_hdrs} ${OGR_KMLHHEADERS})
set(lib_srcs ${lib_srcs} ${OGR_KMLCSOURCES})

set(OGR_KMLHHEADERS
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/atom_util.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/convenience.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/csv_file.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/csv_parser.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/element_counter.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/feature_list.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/google_doc_list.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/google_maps_data.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/google_picasa_web.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/google_spreadsheets.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/gpx_trk_pt_handler.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/http_client.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/http_client_test_util.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/kml_feature_list_saver.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/kmz_check_links.h
)
source_group("Header Files\\convenience" FILES ${OGR_KMLHHEADERS})
   
set(OGR_KMLCSOURCES
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/atom_util.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/convenience.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/csv_file.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/csv_parser.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/feature_list.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/google_doc_list.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/google_maps_data.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/google_picasa_web.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/google_spreadsheets.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/http_client.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/convenience/kmz_check_links.cc
)
source_group("Source Files\\convenience" FILES ${OGR_KMLCSOURCES})

set(lib_hdrs ${lib_hdrs} ${OGR_KMLHHEADERS})
set(lib_srcs ${lib_srcs} ${OGR_KMLCSOURCES})

set(OGR_KMLHHEADERS
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/abstractlatlonbox.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/abstractview.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/atom.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/balloonstyle.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/colorstyle.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/container.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/document.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/element.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/extendeddata.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/feature.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/folder.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/geometry.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/gx_timeprimitive.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/gx_tour.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/hotspot.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/iconstyle.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/kml.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/kml22.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/kmldom.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/kml_cast.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/kml_factory.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/kml_funcs.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/kml_handler.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/kml_handler_ns.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/kml_ptr.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/labelstyle.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/linestyle.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/link.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/liststyle.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/model.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/networklink.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/networklinkcontrol.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/object.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/overlay.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/parser.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/parser_observer.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/placemark.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/polystyle.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/region.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/schema.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/serializer.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/snippet.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/stats_serializer.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/style.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/stylemap.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/styleselector.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/substyle.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/timeprimitive.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/vec2.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/visitor.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/visitor_driver.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/xal.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/xml_serializer.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/xsd.h
)
source_group("Header Files\\dom" FILES ${OGR_KMLHHEADERS})

set(OGR_KMLCSOURCES
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/abstractlatlonbox.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/abstractview.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/atom.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/balloonstyle.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/colorstyle.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/container.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/document.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/element.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/extendeddata.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/feature.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/folder.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/geometry.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/gx_timeprimitive.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/gx_tour.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/hotspot.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/iconstyle.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/kml.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/kml22.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/kml_cast.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/kml_factory.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/kml_handler.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/kml_handler_ns.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/labelstyle.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/linestyle.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/link.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/liststyle.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/model.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/networklink.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/networklinkcontrol.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/object.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/overlay.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/parser.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/placemark.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/polystyle.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/region.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/schema.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/serializer.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/snippet.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/style.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/stylemap.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/styleselector.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/substyle.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/timeprimitive.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/vec2.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/visitor.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/visitor_driver.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/xal.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/xml_serializer.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom/xsd.cc
)
source_group("Source Files\\dom" FILES ${OGR_KMLCSOURCES})

set(lib_hdrs ${lib_hdrs} ${OGR_KMLHHEADERS})
set(lib_srcs ${lib_srcs} ${OGR_KMLCSOURCES})

set(OGR_KMLHHEADERS
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/bbox.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/clone.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/engine_constants.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/engine_types.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/entity_mapper.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/feature_balloon.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/feature_view.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/feature_visitor.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/find.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/find_xml_namespaces.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/get_links.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/get_link_parents.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/href.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/id_mapper.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/id_mapper_internal.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/kml_cache.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/kml_file.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/kml_stream.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/kml_uri.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/kml_uri_internal.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/kmz_cache.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/kmz_file.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/link_util.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/location_util.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/merge.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/object_id_parser_observer.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/old_schema_parser_observer.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/parse_old_schema.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/schema_parser_observer.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/shared_style_parser_observer.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/style_inliner.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/style_inliner_internal.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/style_merger.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/style_resolver.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/style_splitter.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/style_splitter_internal.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/update.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/update_processor.h
)

source_group("Header Files\\engine" FILES ${OGR_KMLHHEADERS})

set(OGR_KMLCSOURCES
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/clone.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/entity_mapper.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/feature_balloon.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/feature_view.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/feature_visitor.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/find.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/find_xml_namespaces.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/get_links.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/get_link_parents.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/href.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/id_mapper.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/kml_cache.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/kml_file.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/kml_stream.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/kml_uri.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/kmz_cache.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/kmz_file.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/link_util.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/location_util.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/merge.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/parse_old_schema.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/style_inliner.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/style_merger.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/style_resolver.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/style_splitter.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/update.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine/update_processor.cc
)
source_group("Source Files\\engine" FILES ${OGR_KMLCSOURCES})

set(lib_hdrs ${lib_hdrs} ${OGR_KMLHHEADERS})
set(lib_srcs ${lib_srcs} ${OGR_KMLCSOURCES})

set(OGR_KMLHHEADERS
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/regionator/feature_list_regionator.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/regionator/feature_list_region_handler.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/regionator/regionator.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/regionator/regionator_qid.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/regionator/regionator_util.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/regionator/region_handler.h
)

source_group("Header Files\\regionator" FILES ${OGR_KMLHHEADERS})

set(OGR_KMLCSOURCES
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/regionator/feature_list_region_handler.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/regionator/regionator.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/regionator/regionator_util.cc
)
source_group("Source Files\\regionator" FILES ${OGR_KMLCSOURCES})   

set(lib_hdrs ${lib_hdrs} ${OGR_KMLHHEADERS})
set(lib_srcs ${lib_srcs} ${OGR_KMLCSOURCES}) 

set(OGR_KMLHHEADERS
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/xsd/xsd_complex_type.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/xsd/xsd_element.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/xsd/xsd_file.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/xsd/xsd_handler.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/xsd/xsd_primitive_type.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/xsd/xsd_schema.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/xsd/xsd_simple_type.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/xsd/xsd_type.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/xsd/xsd_util.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/xsd/xst_parser.h
)

source_group("Header Files\\xsd" FILES ${OGR_KMLHHEADERS})

set(OGR_KMLCSOURCES
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/xsd/xsd_complex_type.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/xsd/xsd_element.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/xsd/xsd_file.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/xsd/xsd_handler.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/xsd/xsd_primitive_type.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/xsd/xsd_util.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/xsd/xst_parser.cc
)
source_group("Source Files\\xsd" FILES ${OGR_KMLCSOURCES})   

set(lib_hdrs ${lib_hdrs} ${OGR_KMLHHEADERS})
set(lib_srcs ${lib_srcs} ${OGR_KMLCSOURCES}) 

set(OGR_KMLHHEADERS
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/dom.h
    ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/engine.h
)

set(lib_hdrs ${lib_hdrs} ${OGR_KMLHHEADERS})

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/third_party/uriparser-0.7.5/include)

set(OGR_KMLHHEADERS
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/uriparser-0.7.5/lib/UriCommon.h
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/uriparser-0.7.5/lib/UriIp4Base.h
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/uriparser-0.7.5/lib/UriNormalizeBase.h
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/uriparser-0.7.5/lib/UriParseBase.h
)
source_group("Header Files\\uriparser" FILES ${OGR_KMLHHEADERS})

set(OGR_KMLCSOURCES
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/uriparser-0.7.5/lib/UriCommon.c
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/uriparser-0.7.5/lib/UriCompare.c
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/uriparser-0.7.5/lib/UriEscape.c
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/uriparser-0.7.5/lib/UriFile.c
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/uriparser-0.7.5/lib/UriIp4.c
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/uriparser-0.7.5/lib/UriIp4Base.c
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/uriparser-0.7.5/lib/UriNormalize.c
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/uriparser-0.7.5/lib/UriNormalizeBase.c
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/uriparser-0.7.5/lib/UriParse.c
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/uriparser-0.7.5/lib/UriParseBase.c
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/uriparser-0.7.5/lib/UriQuery.c
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/uriparser-0.7.5/lib/UriRecompose.c
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/uriparser-0.7.5/lib/UriResolve.c
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/uriparser-0.7.5/lib/UriShorten.c
)
source_group("Source Files\\uriparser" FILES ${OGR_KMLCSOURCES})   

set(lib_hdrs ${lib_hdrs} ${OGR_KMLHHEADERS})
set(lib_srcs ${lib_srcs} ${OGR_KMLCSOURCES}) 

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/third_party/zlib-1.2.3/contrib)

set(OGR_KMLHHEADERS
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/zlib-1.2.3/contrib/minizip/crypt.h
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/zlib-1.2.3/contrib/minizip/ioapi.h    
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/zlib-1.2.3/contrib/minizip/mztools.h
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/zlib-1.2.3/contrib/minizip/unzip.h
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/zlib-1.2.3/contrib/minizip/zip.h
)
        
if(WIN32)
    set(OGR_KMLHHEADERS ${OGR_KMLHHEADERS} ${CMAKE_CURRENT_SOURCE_DIR}/third_party/zlib-1.2.3/contrib/minizip/iowin32.h)
#else(WIN32)
#    set(OGR_KMLHHEADERS ${OGR_KMLHHEADERS} ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/file_posix.cc)
endif(WIN32)  

source_group("Header Files\\minizip" FILES ${OGR_KMLHHEADERS})

set(OGR_KMLCSOURCES
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/zlib-1.2.3/contrib/minizip/ioapi.c
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/zlib-1.2.3/contrib/minizip/iomem_simple.c    
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/zlib-1.2.3/contrib/minizip/miniunz.c
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/zlib-1.2.3/contrib/minizip/minizip.c
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/zlib-1.2.3/contrib/minizip/mztools.c
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/zlib-1.2.3/contrib/minizip/unzip.c
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/zlib-1.2.3/contrib/minizip/zip.c
)
source_group("Source Files\\minizip" FILES ${OGR_KMLCSOURCES}) 
        
if(WIN32)
    set(OGR_KMLCSOURCES ${OGR_KMLCSOURCES} ${CMAKE_CURRENT_SOURCE_DIR}/third_party/zlib-1.2.3/contrib/minizip/iowin32.c)
#else(WIN32)
#    set(OGR_KMLHHEADERS ${OGR_KMLHHEADERS} ${CMAKE_CURRENT_SOURCE_DIR}/src/kml/base/file_posix.cc)
endif(WIN32)    

set(lib_hdrs ${lib_hdrs} ${OGR_KMLHHEADERS})
set(lib_srcs ${lib_srcs} ${OGR_KMLCSOURCES}) 

# ----------------------------------------------------------------------------------
# 				Define the library target:
# ----------------------------------------------------------------------------------

set(the_target "kml")

# COMMAND LINE OPTIONS
if(DEFINED KML_SHARED)
  option(KML_SHARED "Build shared lib" ${KML_SHARED})
else()
  option(KML_SHARED "Build shared lib" OFF)
endif()
if(DEFINED KML_STATIC)
  option(KML_STATIC "Build static lib" ${KML_STATIC})
else()
  option(KML_STATIC "Build static lib" ON)
endif()

if(KML_SHARED)
  add_library(${the_target} SHARED ${lib_srcs} ${lib_hdrs})
  if(MSVC)
    # msvc does not append 'lib' - do it here to have consistent name    
    set_target_properties(${the_target} PROPERTIES IMPORT_PREFIX "lib")
  endif()
elseif(KML_STATIC)
# does not work without changing name
  add_library(${the_target} STATIC ${lib_srcs} ${lib_hdrs})
endif()

add_custom_target(uninstall
    COMMAND ${CMAKE_COMMAND} -P ${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake)

set_target_properties(${the_target} PROPERTIES PREFIX "lib")

if(KML_SHARED AND WIN32)
  set_target_properties(${the_target} PROPERTIES DEFINE_SYMBOL KML_BUILD_DLL)
endif()

if(MSVC)
    string(REPLACE "/W4" "/W0" CMAKE_C_FLAGS "${CMAKE_C_FLAGS}")
    string(REPLACE "/W4" "/W0" CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}")
    # add_definitions(-DDISABLE_SOME_FLOATING_POINT)
    if(CMAKE_CL_64)
        set_target_properties(${the_target} PROPERTIES STATIC_LIBRARY_FLAGS "/machine:x64")
    endif()

endif()

if(UNIX)
     if(CMAKE_COMPILER_IS_GNUCXX OR CV_ICC)
        set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fPIC")
     endif()
endif()

add_definitions(-DUNICODE -D_UNICODE)    

if(WIN32)
  # Postfix of DLLs:
  set(LIB_DEBUG_POSTFIX d)
else()
  # Postfix of so's:
  set(LIB_DEBUG_POSTFIX)
endif()

if(CMAKE_CL_64)
set_target_properties(${the_target}
	PROPERTIES OUTPUT_NAME "${the_target}"
	DEBUG_POSTFIX "${LIB_DEBUG_POSTFIX}"
	ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/lib/x64
    LIBRARY_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/lib/x64
    RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/bin/x64
	)
else()
set_target_properties(${the_target}
	PROPERTIES OUTPUT_NAME "${the_target}"
	DEBUG_POSTFIX "${LIB_DEBUG_POSTFIX}"
	ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/lib/x86
    LIBRARY_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/lib/x86
    RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/bin/x86
	)
endif()   
	


if(ZLIB_FOUND)
    target_link_libraries(${the_target} ${ZLIB_LIBRARIES})
endif(ZLIB_FOUND)

if(EXPAT_FOUND)
    target_link_libraries(${the_target} ${EXPAT_LIBRARIES})
endif(EXPAT_FOUND) 

if(ENABLE_SOLUTION_FOLDERS)
	set_target_properties(${the_target} PROPERTIES FOLDER "libkml")
endif()	

# if(WIN32)
# install(TARGETS ${the_target} DESTINATION ${LIB_INSTALL_PATH})
# else()
# install(TARGETS ${the_target}
        # RUNTIME DESTINATION bin
        # ARCHIVE DESTINATION lib
        # LIBRARY DESTINATION lib )
# install(FILES ${lib_hdrs} DESTINATION include/kml)        
# install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/src/kml DESTINATION include
          # FILES_MATCHING PATTERN "*.h")
# endif()
    
