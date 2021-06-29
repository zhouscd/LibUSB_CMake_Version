cmake_minimum_required(VERSION 3.10)
project(LibUSB C)

find_package(Threads REQUIRED)

if (UNIX)
set(CMAKE_C_FLAGS  "${CMAKE_C_FLAGS} -ludev")   # for usb driver
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -ludev")
endif()

if (WIN32)
set(OS_SOURCES
    os/threads_windows.h 
    os/threads_windows.c
    os/poll_windows.h 
    os/poll_windows.c
    libusb-1.0.def
    libusb-1.0.rc
    os/windows_common.h
    os/windows_nt_common.h 
    os/windows_nt_common.c
	os/windows_nt_shared_types.h
	os/windows_usbdk.h 
    os/windows_usbdk.c
	os/windows_winusb.h
    os/windows_winusb.c
    os/msvc/config.h
  )
set(LibUSB_HEADER_PATHS
    ./ 
    ./os 
    ./os/msvc
    )
else()
set(CMAKE_C_FLAGS ${CMAKE_C_FLAGS} "-ludev")
set(OS_SOURCES
    os/threads_posix.h 
    os/threads_posix.c
    os/poll_posix.h 
    os/poll_posix.c
    os/linux_usbfs.h 
    os/linux_usbfs.c
    os/linux_udev.c
  )

set(LibUSB_HEADER_PATHS
    ./ 
    ./os
    ./os/linux
    )
endif()

set(LibUSB_SOURCES
    libusb.h 
    libusbi.h
    version.h 
    version_nano.h
	core.c 
    descriptor.c 
    hotplug.h 
    hotplug.c 
    io.c 
    strerror.c 
    sync.c
  )

add_library(LibUSB ${LibUSB_SOURCES} ${OS_SOURCES})
target_link_libraries(LibUSB Threads::Threads)
target_include_directories(LibUSB PUBLIC ${LibUSB_HEADER_PATHS})
add_subdirectory(test)

