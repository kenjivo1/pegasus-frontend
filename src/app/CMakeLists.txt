find_package(Qt5 REQUIRED COMPONENTS
    Core
    Qml
    LinguistTools
)


# Find TS files, compile them to QM and generate a QRC file collecting them

file(GLOB PEGASUS_TS_FILES LIST_DIRECTORIES false "${PROJECT_SOURCE_DIR}/lang/*.ts")
qt5_add_translation(PEGASUS_QM_FILES ${PEGASUS_TS_FILES})

set(PEGASUS_LOCALE_QRC "${CMAKE_CURRENT_BINARY_DIR}/locales.qrc")
file(WRITE ${PEGASUS_LOCALE_QRC} "<!DOCTYPE RCC><RCC version=\"1.0\"><qresource prefix=\"i18n\">\n")

foreach(QM_FILE ${PEGASUS_QM_FILES})
    get_filename_component(QM_FILENAME ${QM_FILE} NAME)
    file(APPEND ${PEGASUS_LOCALE_QRC} "<file>${QM_FILENAME}</file>\n")
endforeach()

file(APPEND ${PEGASUS_LOCALE_QRC} "</qresource></RCC>")
qt_add_resources(PEGASUS_LOCALE_SRC ${PEGASUS_LOCALE_QRC})

add_library(pegasus-locales ${PEGASUS_LOCALE_SRC})


# Main runtime

add_executable(pegasus-fe main.cpp)
target_link_libraries(pegasus-fe PUBLIC
    Qt5::Core
    pegasus-assets
    pegasus-backend
    pegasus-locales
    pegasus-qml
)

include(PegasusCommonProps)
pegasus_add_common_props(pegasus-fe)
