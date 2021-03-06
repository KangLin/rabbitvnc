project(rfb_win32)

set(RFB_WIN32_SOURCES
    AboutDialog.cxx
    LaunchProcess.cxx
    SInput.cxx
    CKeyboard.cxx
    LowLevelKeyEvents.cxx
    SocketManager.cxx
    MonitorInfo.cxx
    TCharArray.cxx
    Clipboard.cxx
    MsgWindow.cxx
    Threading.cxx
    CPointer.cxx
    OSVersion.cxx
    TsSessions.cxx
    CurrentUser.cxx
    RegConfig.cxx
    Win32Util.cxx
    DeviceContext.cxx
    Registry.cxx
    WMCursor.cxx
    DeviceFrameBuffer.cxx
    SDisplay.cxx
    WMHooks.cxx
    Dialog.cxx
    SDisplayCorePolling.cxx
    WMNotifier.cxx
    DIBSectionBuffer.cxx
    SDisplayCoreWMHooks.cxx
    WMPoller.cxx
    DynamicFn.cxx
    Security.cxx
    WMShatter.cxx
    EventManager.cxx
    Service.cxx
    WMWindowCopyRect.cxx
    CleanDesktop.cxx
)

set(INSTALLHEADER_FILES
    AboutDialog.h
    IntervalTimer.h
    Security.h
    BitmapInfo.h
    keymap.h
    Service.h
    CKeyboard.h
    LaunchProcess.h
    SInput.h
    CleanDesktop.h
    LocalMem.h
    SocketManager.h
    Clipboard.h
    LogicalPalette.h
    TCharArray.h
    CompatibleBitmap.h
    LowLevelKeyEvents.h
    Threading.h
    ComputerName.h
    ModuleFileName.h
    TrayIcon.h
    CPointer.h
    MonitorInfo.h
    TsSessions.h
    CurrentUser.h
    MsgBox.h
    Win32Util.h
    DeviceContext.h
    MsgWindow.h
    WMCursor.h
    DeviceFrameBuffer.h
    OSVersion.h
    WMHooks.h
    Dialog.h
    RegConfig.h
    WMNotifier.h
    DIBSectionBuffer.h
    Registry.h
    WMPoller.h
    DynamicFn.h
    SDisplay.h
    WMShatter.h
    EventManager.h
    SDisplayCoreDriver.h
    WMWindowCopyRect.h
    Handle.h
    SDisplayCorePolling.h
    IconInfo.h
    SDisplayCoreWMHooks.h
    )

add_library(${PROJECT_NAME} ${RFB_WIN32_SOURCES})

target_link_libraries(${PROJECT_NAME} PUBLIC rfb ws2_32.lib user32.lib comctl32.lib version.lib)

set_target_properties(${PROJECT_NAME} PROPERTIES
    RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin
    LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin
    ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib
    PUBLIC_HEADER "${INSTALLHEADER_FILES}" # Install head files
    VERSION ${BUILD_VERSION})

# See: http://www.it1352.com/478094.html
target_include_directories(${PROJECT_NAME}
    PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/win>
    )

if(MSVC)
    target_compile_options(${PROJECT_NAME} PUBLIC
        "$<$<C_COMPILER_ID:MSVC>:/utf-8>"
        "$<$<CXX_COMPILER_ID:MSVC>:/utf-8>")
endif()

GENERATE_EXPORT_HEADER(${PROJECT_NAME})
string(TOLOWER ${PROJECT_NAME} LOWER_PROJECT_NAME)
set(INSTALLHEADER_FILES ${INSTALLHEADER_FILES} 
    ${CMAKE_CURRENT_BINARY_DIR}/${LOWER_PROJECT_NAME}_export.h)
file(COPY ${CMAKE_CURRENT_BINARY_DIR}/${LOWER_PROJECT_NAME}_export.h
    DESTINATION ${CMAKE_BINARY_DIR})

# Install target
INSTALL(TARGETS ${PROJECT_NAME}
    EXPORT ${PROJECT_NAME}Config
    RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}"
    COMPONENT Runtime
    LIBRARY DESTINATION "${CMAKE_INSTALL_BINDIR}"
    COMPONENT Runtime
    ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}"
    PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/RabbitVNC/${PROJECT_NAME}
    INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
    )

export(TARGETS ${PROJECT_NAME}
    APPEND FILE ${CMAKE_BINARY_DIR}/${PROJECT_NAME}Config.cmake
    )
# Install cmake configure files
install(EXPORT ${PROJECT_NAME}Config
    DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake"
    )
write_basic_package_version_file(
    "${CMAKE_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake"
    VERSION ${BUILD_VERSION}
    COMPATIBILITY AnyNewerVersion)
install(FILES "${CMAKE_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake" DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake")

if(UNIX)
  libtool_create_control_file(${PROJECT_NAME})
endif()
