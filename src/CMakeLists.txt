cmake_minimum_required(VERSION 2.8.12)

set(SOURCE_FILES
    ../include/uv.h
    ../include/tree.h
    ../include/uv-errno.h
    ../include/uv-threadpool.h
    ../include/uv-version.h
    fs-poll.c
    heap-inl.h
    inet.c
    queue.h
    threadpool.c
    uv-common.c
    uv-common.h
    version.c
)

if(DETECTED_OS_WIN)
  set(SOURCE_FILES ${SOURCE_FILES}
      ../include/uv-win.h
      win/async.c
      win/atomicops-inl.h
      win/core.c
      win/dl.c
      win/error.c
      win/fs.c
      win/fs-event.c
      win/getaddrinfo.c
      win/getnameinfo.c
      win/handle.c
      win/handle-inl.h
      win/internal.h
      win/loop-watcher.c
      win/pipe.c
      win/thread.c
      win/poll.c
      win/process.c
      win/process-stdio.c
      win/req.c
      win/req-inl.h
      win/signal.c
      win/stream.c
      win/stream-inl.h
      win/tcp.c
      win/tty.c
      win/timer.c
      win/udp.c
      win/util.c
      win/winapi.c
      win/winapi.h
      win/winsock.c
      win/winsock.h
  )
  if(MSVC_VERSION < 1900)
	set(SOURCE_FILES ${SOURCE_FILES}
		win/snprintf.c
	)
  endif()
else()
  set(SOURCE_FILES ${SOURCE_FILES}
      ../include/uv-unix.h
	  ../include/uv-linux.h
	  ../include/uv-sunos.h
	  ../include/uv-darwin.h
	  ../include/uv-bsd.h
      ../include/uv-aix.h
      unix/async.c
      unix/atomic-ops.h
      unix/core.c
      unix/dl.c
      unix/fs.c
      unix/getaddrinfo.c
      unix/getnameinfo.c
      unix/internal.h
      unix/loop.c
      unix/loop-watcher.c
      unix/pipe.c
      unix/poll.c
      unix/process.c
      unix/signal.c
      unix/spinlock.h
      unix/stream.c
      unix/tcp.c
      unix/thread.c
      unix/timer.c
      unix/tty.c
      unix/udp.c
  )
endif()

if(DETECTED_OS_LINUX OR DETECTED_OS_MAC OR DETECTED_OS_ANDROID)
  set(SOURCE_FILES ${SOURCE_FILES}
      unix/proctitle.c
  )
endif()

if(DETECTED_OS_MAC)
  set(SOURCE_FILES ${SOURCE_FILES}
      unix/darwin.c
	  unix/fsevents.c
	  unix/darwin-proctitle.c
  )
endif()

if(DETECTED_OS_LINUX)
  set(SOURCE_FILES ${SOURCE_FILES}
      unix/linux-core.c
      unix/linux-inotify.c
      unix/linux-syscalls.c
      unix/linux-syscalls.h
  )
endif()

if(DETECTED_OS_ANDROID)
  set(SOURCE_FILES ${SOURCE_FILES}
      unix/linux-core.c
      unix/linux-inotify.c
      unix/linux-syscalls.c
      unix/linux-syscalls.h
      unix/pthread-fixes.c
      unix/android-ifaddrs.c
  )
endif()

if(DETECTED_OS_SOLARIS)
  set(SOURCE_FILES ${SOURCE_FILES}
      unix/sunos.c
  )
endif()

if(DETECTED_OS_AIX)
  set(SOURCE_FILES ${SOURCE_FILES}
      unix/aix.c
  )
endif()

if(DETECTED_OS_FREEBSD)
  set(SOURCE_FILES ${SOURCE_FILES}
      unix/freebsd.c
  )
endif()

if(DETECTED_OS_OPENBSD)
  set(SOURCE_FILES ${SOURCE_FILES}
      unix/openbsd.c
  )
endif()

if(DETECTED_OS_NETBSD)
  set(SOURCE_FILES ${SOURCE_FILES}
      unix/netbsd.c
  )
endif()

if(DETECTED_OS_MAC OR DETECTED_OS_FREEBSD OR DETECTED_OS_OPENBSD OR DETECTED_OS_NETBSD)
  set(SOURCE_FILES ${SOURCE_FILES}
      unix/kqueue.c
  )
endif()

add_library(${TARGET_NAME} ${SOURCE_FILES})
target_include_directories(${TARGET_NAME} PUBLIC "../include/" PRIVATE "./")

if(BUILD_SHARED_LIBS)
  target_compile_definitions(${TARGET_NAME}
    PRIVATE BUILDING_UV_SHARED
    INTERFACE USING_UV_SHARED
  )
endif()

if(NOT DETECTED_OS_WIN)
  if(DETECTED_OS_SOLARIS)
    set(LIB_PTHREAD_NAME "pthread")
  elseif(NOT DETECTED_OS_SOLARIS AND NOT DETECTED_OS_ANDROID)
    set(LIB_PTHREAD_NAME "pthread")
  else()
    set(LIB_PTHREAD_NAME "")
  endif()
endif()

if(DETECTED_OS_WIN)
  target_link_libraries(${TARGET_NAME} iphlpapi.lib)
  target_link_libraries(${TARGET_NAME} psapi.lib)
  target_link_libraries(${TARGET_NAME} userenv.lib)
  target_link_libraries(${TARGET_NAME} ws2_32.lib)
  target_compile_definitions(${TARGET_NAME} PRIVATE _WIN32_WINNT=0x0600)
  target_compile_definitions(${TARGET_NAME} PRIVATE _GNU_SOURCE)
else()
  target_link_libraries(${TARGET_NAME} m ${LIB_PTHREAD_NAME})
  target_compile_options(${TARGET_NAME} PRIVATE --std=gnu89 -pedantic)
  target_compile_definitions(${TARGET_NAME} PUBLIC _LARGEFILE_SOURCE)
  target_compile_definitions(${TARGET_NAME} PUBLIC _FILE_OFFSET_BITS=64)
endif()

if(DETECTED_OS_LINUX)
  target_link_libraries(${TARGET_NAME} dl rt)
  target_compile_definitions(${TARGET_NAME} PUBLIC _POSIX_C_SOURCE=200112)
  target_compile_definitions(${TARGET_NAME} PRIVATE _GNU_SOURCE)
endif()

if(DETECTED_OS_MAC)
  target_compile_definitions(${TARGET_NAME} PUBLIC _DARWIN_USE_64_BIT_INODE=1)
  target_compile_definitions(${TARGET_NAME} PRIVATE _DARWIN_UNLIMITED_SELECT=1)
endif()

if(DETECTED_OS_ANDROID)
  target_link_libraries(${TARGET_NAME} dl)
endif()

if(DETECTED_OS_SOLARIS)
  target_link_libraries(${TARGET_NAME} kstat nsl sendfile socket)
  target_compile_definitions(${TARGET_NAME} PRIVATE __EXTENSIONS__ _XOPEN_SOURCE=500)
endif()

if(DETECTED_OS_AIX)
  target_link_libraries(${TARGET_NAME} perfstat)
  target_compile_definitions(${TARGET_NAME} PRIVATE _ALL_SOURCE _XOPEN_SOURCE=500 _LINUX_SOURCE_COMPAT)
endif()

if(DETECTED_OS_FREEBSD OR DETECTED_OS_OPENBSD OR DETECTED_OS_NETBSD)
  target_link_libraries(${TARGET_NAME} kvm)
endif()

if(BUILD_SHARED_LIBS)
  install(TARGETS ${TARGET_NAME}
        RUNTIME DESTINATION ./
        LIBRARY DESTINATION ./ COMPONENT "binaries")
endif()
