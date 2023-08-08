cmake_minimum_required(VERSION 3.21)

project(libpebliss VERSION "1.0.0")

file(GLOB SOURCES "src/*.cpp")
file(GLOB HEADERS "include/libpebliss/*.h")
include_directories("include/libpebliss")

add_library(libpebliss ${SOURCES} ${HEADERS})

set_target_properties(libpebliss PROPERTIES CXX_STANDARD 20
                                          CXX_STANDARD_REQUIRED ON
                                          CXX_EXTENSIONS OFF

                                          PUBLIC_HEADER "${HEADERS}"
                                          RUNTIME_OUTPUT_DIRECTORY "bin"
                                          LIBRARY_OUTPUT_DIRECTORY "bin"
                                          ARCHIVE_OUTPUT_DIRECTORY "lib"

                                          DEBUG_POSTFIX "d"
                                          MINSIZEREL_POSTFIX "msr"
                                          RELWITHDEBINFO_POSTFIX "rwdi")

SET(PEBLISS_CMAKE_DIR ${CMAKE_INSTALL_LIBDIR}/cmake/pebliss CACHE STRING
        "Installation directory for cmake files, relative to ${CMAKE_INSTALL_PREFIX}.")

# Use GNU install directories if CMAKE_INSTALL_PREFIX not specified
include(GNUInstallDirs)

# Specify where library and headers will be installed
install(TARGETS libpebliss
        EXPORT libpeblissTargets
        ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}"
        RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}"
        LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}"
        PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/libpebliss)
target_include_directories(libpebliss PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
                                           $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>)

# Create libpeblissConfigVersion package used to specify installed package version
include(CMakePackageConfigHelpers)
write_basic_package_version_file("${CMAKE_CURRENT_BINARY_DIR}/cmake/libpeblissConfigVersion.cmake"
                                 VERSION ${VERSION}
                                 COMPATIBILITY AnyNewerVersion)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/cmake/libpeblissConfigVersion.cmake
        DESTINATION ${CMAKE_INSTALL_DATAROOTDIR}/libpebliss
        COMPONENT devel)

# Specifying config file that will be used to find a library using find_package().
export(TARGETS ${PROJECT_NAME}
       FILE libpeblissConfig.cmake)
install(EXPORT libpeblissTargets
        FILE libpeblissConfig.cmake
        DESTINATION ${CMAKE_INSTALL_DATAROOTDIR}/libpebliss)