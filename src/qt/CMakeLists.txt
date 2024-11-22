CMAKE_MINIMUM_REQUIRED(VERSION 3.10)

set(QT_MINIMUM_VERSION 5.5.1)
set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOUIC_SEARCH_PATHS ${CMAKE_CURRENT_SOURCE_DIR}/forms ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/forms ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/settings/forms)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

if (${CMAKE_SYSTEM_NAME} MATCHES "Linux")
    set(CMAKE_POSITION_INDEPENDENT_CODE ON)
endif ()

unset(Qt5Enables)
foreach(comp Widgets Core Gui Network LinguistTools DBus Svg Charts)
    find_package(Qt5${comp})
    set(Qt5Enables "${Qt5Enables};Qt5${comp}_FOUND")
    if(Qt5${comp}_FOUND)
        MESSAGE(STATUS "FOUND Qt5${comp}")
        include_directories(${include_directories} ${Qt5${comp}_INCLUDE_DIRS} ${QT_USE_FILE})
        add_definitions(${Qt5${comp}_DEFINITIONS})
        list(APPEND Qt5_LIBRARIES ${Qt5${comp}_LIBRARIES})
    else()
        MESSAGE(WARNING "Cant find Qt5${comp}")
    endif()
endforeach(comp)

if (Qt5Core_FOUND)
    if (Qt5Core_VERSION VERSION_LESS QT_MINIMUM_VERSION)
        MESSAGE(FATAL_ERROR "Minimum supported Qt5 version is ${QT_MINIMUM_VERSION}")
    else()
        MESSAGE(STATUS "Found Qt5 version: ${Qt5Core_VERSION}")
    endif()
else()
    MESSAGE(FATAL_ERROR "Could not find any Qt5 installation")
endif()

if (Qt5DBus_FOUND)
    add_compile_options("-DUSE_DBUS")
endif()

if (Qt5Charts_FOUND)
    add_compile_options("-DUSE_QTCHARTS")
endif()

find_package(Qrcode)
if (QRCODE_FOUND)
    include_directories ( ${QRCODE_INCLUDE_DIR} )
    link_directories ( ${QRCODE_LIBRARY_DIRS} )
endif()

find_program(MOC_BIN NAMES moc moc-qt5 qt5-moc
        PATHS /usr/lib/qt5/bin /usr/lib/x86_64-linux-gnu/qt5/bin /usr/local/opt/qt/bin /usr/local/opt/qt5/bin
        NO_DEFAULT_PATH
        NO_SYSTEM_ENVIRONMENT_PATH)
if (MOC_BIN)
    MESSAGE(STATUS "FOUND MOC ${MOC_BIN}")
endif ()

add_compile_options("-DQT_NO_KEYWORDS")

# Why isn't this done automatically??
if (${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
    set(CMAKE_AUTOMOC_MOC_OPTIONS "-DQ_OS_MAC")
endif()

file(GLOB LOCAL_QT_HEADERS ${CMAKE_CURRENT_SOURCE_DIR}/*.h)
source_group("QtHeaders" FILES ${LOCAL_QT_HEADERS})

find_package(Protobuf REQUIRED)
if(Protobuf_FOUND)
    message(STATUS "Found Protobuf compiler: ${Protobuf_PROTOC_EXECUTABLE}")
    message(STATUS "Found Protobuf library: ${Protobuf_LIBRARIES}")
endif()
include_directories(${PROTOBUF_INCLUDE_DIRS})
include_directories(${CMAKE_CURRENT_BINARY_DIR})

PROTOBUF_GENERATE_CPP(PROTO_SRCS PROTO_HDRS ${CMAKE_CURRENT_SOURCE_DIR}/paymentrequest.proto)

SET(QT_SOURCES
        ${CMAKE_CURRENT_SOURCE_DIR}/bantablemodel.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/bitcoinaddressvalidator.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/bitcoinunits.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/clientmodel.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/csvmodelwriter.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/guiutil.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/intro.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/networkstyle.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/notificator.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/optionsdialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/optionsmodel.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/peertablemodel.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/platformstyle.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/qvalidatedlineedit.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/qvaluecombobox.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/rpcconsole.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/trafficgraphwidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/utilitydialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/addressbookpage.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/addresstablemodel.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/askpassphrasedialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/coincontroldialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/coincontroltreewidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/editaddressdialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/openuridialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/paymentrequestplus.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/paymentserver.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/recentrequeststablemodel.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/transactiondesc.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/transactionfilterproxy.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/transactionrecord.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/transactiontablemodel.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/walletmodel.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/walletmodeltransaction.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/blobfishgui.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/loadingdialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/pwidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/focuseddialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/guitransactionsutils.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/snackbar.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/navmenuwidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/lockunlock.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/receivedialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/furabstractlistitemdelegate.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/txviewholder.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/qtutils.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/expandablebutton.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/topbar.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/txrow.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/addressfilterproxymodel.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/dashboardwidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/addresslabelrow.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/contactdropdownrow.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/contactsdropdown.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/optionbutton.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/mninfodialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/coldstakingmodel.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/mnmodel.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/masternodewizarddialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/mnrow.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/masternodeswidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/sendchangeaddressdialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/sendconfirmdialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/sendcustomfeedialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/sendchangeaddressdialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/sendmultirow.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/send.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/addnewaddressdialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/addnewcontactdialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/requestdialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/receivewidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/myaddressrow.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/csrow.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/addressholder.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/tooltipmenu.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/addresseswidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/defaultdialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/coldstakingwidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/settings/settingsbackupwallet.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/settings/settingsbittoolwidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/settings/settingsconsolewidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/settings/settingsdisplayoptionswidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/settings/settingsfaqwidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/settings/settingsinformationwidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/settings/settingsmainoptionswidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/settings/settingsmultisenddialog.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/settings/settingsmultisendwidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/settings/settingssignmessagewidgets.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/settings/settingswalletoptionswidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/settings/settingswalletrepairwidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/settings/settingsexportcsv.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/settings/settingswidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/welcomecontentwidget.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/splash.cpp
        )

# Workaround until the old rpcconsole UI window is fully removed
set_property(SOURCE ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/settings/settingsconsolewidget.cpp PROPERTY SKIP_AUTOMOC ON)
execute_process(
        COMMAND ${MOC_BIN} -o moc_settingsconsolewidget.cpp settingsconsolewidget.h
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/settings
)

execute_process(
        COMMAND ${MOC_BIN} -o moc_pfborderimage.cpp pfborderimage.h
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/blobfish
)
list(APPEND QT_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/moc_pfborderimage.cpp)

if (${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
    list(APPEND QT_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/macdockiconhandler.mm)
    list(APPEND QT_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/macnotificationhandler.mm)
endif()

add_library(qt_stuff STATIC ${BitcoinHeaders} ${QtHeaders} ${QT_SOURCES} ${PROTO_SRCS} ${PROTO_HDRS})
target_include_directories(qt_stuff PUBLIC ${CMAKE_SOURCE_DIR}/src
        ${CMAKE_SOURCE_DIR}/src/leveldb/include
        ${CMAKE_SOURCE_DIR}/src/univalue/include
        ${CMAKE_SOURCE_DIR}/src/secp256k1/include
        ${CMAKE_CURRENT_SOURCE_DIR}
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish
        ${CMAKE_CURRENT_SOURCE_DIR}/blobfish/settings
        ${OPENSSL_INCLUDE_DIR}
        ${BerkeleyDB_INCLUDE_DIRS}
        )
set_property(TARGET qt_stuff PROPERTY CXX_STANDARD 11)

file(GLOB QT_TRANSLATIONS_FILES ${CMAKE_CURRENT_SOURCE_DIR}/locale/*.ts)

get_filename_component(QM_FILES_BINARY_DIR "${CMAKE_CURRENT_SOURCE_DIR}/locale" ABSOLUTE)
set_source_files_properties(${QT_TRANSLATIONS_FILES} PROPERTIES OUTPUT_LOCATION "${QM_FILES_BINARY_DIR}")

QT5_ADD_TRANSLATION(QM ${QT_TRANSLATIONS_FILES})
add_custom_target(translations_target ALL DEPENDS ${QM})

QT5_ADD_RESOURCES(QRC_RESOURCE blobfish.qrc)
QT5_ADD_RESOURCES(QRC_LOCALE_RESOURCE blobfish_locale.qrc)

add_executable(blobfish-qt blobfish.cpp ${QM} ${QRC_RESOURCE} ${QRC_LOCALE_RESOURCE})
add_dependencies(blobfish-qt translations_target libunivalue libsecp256k1 leveldb leveldb_sse42 memenv)
target_include_directories(blobfish-qt PUBLIC ${ENDIAN_INCLUDES})
target_link_libraries(blobfish-qt
        qt_stuff
        univalue
        SERVER_A UTIL_A WALLET_A CLI_A COMMON_A BITCOIN_CRYPTO_A ZEROCOIN_A
        leveldb leveldb_sse42 memenv secp256k1
        ${BerkeleyDB_LIBRARIES} ${OPENSSL_LIBRARIES} ${Boost_LIBRARIES} ${PROTOBUF_LIBRARIES} ${LIBEVENT_LIB}
        pthread
        )
if (${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
    target_link_libraries(blobfish-qt "-framework Cocoa")
endif()
if(GMP_FOUND)
    target_link_libraries(blobfish-qt ${GMP_LIBRARY})
    target_include_directories(blobfish-qt PUBLIC ${GMP_INCLUDE_DIR})
endif()
if(ZMQ_FOUND)
    target_link_libraries(blobfish-qt ZMQ_A ${ZMQ_LIB})
    target_include_directories(blobfish-qt PUBLIC ${ZMQ_INCLUDE_DIR})
endif()
if (QRCODE_FOUND)
    target_link_libraries(blobfish-qt ${QRCODE_LIB})
    target_include_directories(blobfish-qt PUBLIC ${QRCODE_INCLUDE_DIR})
endif()
if(MINIUPNP_FOUND)
    target_compile_definitions(blobfish-qt PUBLIC "-DSTATICLIB -DMINIUPNP_STATICLIB")
    target_link_libraries(blobfish-qt ${MINIUPNP_LIBRARY})
    target_include_directories(blobfish-qt PUBLIC ${MINIUPNP_INCLUDE_DIR})
endif()

target_link_libraries(blobfish-qt Qt5::Gui Qt5::Core Qt5::Widgets Qt5::Network Qt5::Svg ${QT_LIBRARIES})
if (Qt5DBus_FOUND)
    target_link_libraries(blobfish-qt Qt5::DBus ${QT_LIBRARIES})
endif()
if (Qt5Charts_FOUND)
    target_link_libraries(blobfish-qt Qt5::Charts ${QT_LIBRARIES})
endif()
