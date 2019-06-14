cmake_minimum_required(VERSION 3.1)
project(CosseratPlugin VERSION 1.0)

include(cmake/environment.cmake)
find_package(SofaFramework REQUIRED)


set(HEADER_FILES
    src/initCosserat.h

    src/mapping/POEMapping.h
    src/mapping/POEMapping.inl

    
    )

set(SOURCE_FILES
    src/initCosserat.cpp

    src/mapping/POEMapping.cpp
    )

set(README_FILE "Cosserat.txt" )

find_package(SofaAdvancedConstraint QUIET)
if(SofaAdvancedConstraint_FOUND)
    set(HEADER_FILES ${HEADER_FILES}
#        src/POEMapper.h
#        src/POEMapper.inl
        )

    set(SOURCE_FILES ${SOURCE_FILES}
#        src/POEMapper.cpp
        )

endif()


add_library(${PROJECT_NAME} SHARED ${HEADER_FILES} ${SOURCE_FILES} ${README_FILES})

set_target_properties(${PROJECT_NAME} PROPERTIES COMPILE_FLAGS "-DSOFA_BUILD_COSSERAT")

target_include_directories(${PROJECT_NAME} PUBLIC "$<BUILD_INTERFACE:${CMAKE_BINARY_DIR}/include>")
target_include_directories(${PROJECT_NAME} PUBLIC "$<INSTALL_INTERFACE:include>")

target_link_libraries(${PROJECT_NAME} SofaSimpleFem SofaRigid)

## Install rules for the library and headers; CMake package configurations files
sofa_generate_package(
    NAME CosseratPlugin
    VERSION ${PROJECT_VERSION}
    TARGETS ${PROJECT_NAME}
    INCLUDE_SOURCE_DIR src
    INCLUDE_INSTALL_DIR CosseratPlugin/src
    EXAMPLE_INSTALL_DIR examples
    RELOCATABLE "plugins"
    )


option(COSSERAT_BUILD_TESTS "Build unit tests" ON )
if(COSSERAT_BUILD_TESTS)
    add_subdirectory(tests)
endif()

SET_PROPERTY(GLOBAL PROPERTY USE_FOLDERS ON)
SET_PROPERTY(TARGET ${PROJECT_NAME} PROPERTY FOLDER "plugins")

include(cmake/packaging.cmake)