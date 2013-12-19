cmake_minimum_required(VERSION 2.8)

if(NOT WIN32)
  add_definitions(-D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64 -D__USE_FILE_OFFSET64)
  add_definitions(-D_GNU_SOURCE)
else()
  add_definitions(-D_WIN32_WINNT=0x0600)
  add_definitions(-D_GNU_SOURCE)
endif()

if(APPLE)
  add_definitions(_DARWIN_USE_64_BIT_INODE=1)
endif()

if(${CMAKE_SYSTEM_NAME} MATCHES "Linux")
  add_definitions(-D_POSIX_C_SOURCE=200112)
endif()

add_definitions(-DHAVE_CONFIG_H)

set(SOURCE_FILES
    ../include/uv.h
    ../include/tree.h
    ../include/uv-errno.h
    fs-poll.c
    inet.c
    queue.h
    uv-common.c
    uv-common.h
    version.c
)

if(WIN32)
  set(SOURCE_FILES ${SOURCE_FILES}
      win/async.c
      win/atomicops-inl.h
      win/core.c
      win/dl.c
      win/error.c
      win/fs.c
      win/fs-event.c
      win/getaddrinfo.c
      win/handle.c
      win/handle-inl.h
      win/internal.h
      win/loop-watcher.c
      win/pipe.c
      win/poll.c
      win/process.c
      win/process-stdio.c
      win/req.c
      win/req-inl.h
      win/signal.c
      win/stream.c
      win/stream-inl.h
      win/tcp.c
      win/thread.c
      win/threadpool.c
      win/timer.c
      win/tty.c
      win/udp.c
      win/util.c
      win/winapi.c
      win/winapi.h
      win/winsock.c
      win/winsock.h
  )
else()
  set(SOURCE_FILES ${SOURCE_FILES}
      unix/async.c
      unix/atomic-ops.h
      unix/core.c
      unix/dl.c
      unix/fs.c
      unix/getaddrinfo.c
      unix/internal.h
      unix/linux-core.c
      unix/linux-inotify.c
      unix/linux-syscalls.c
      unix/linux-syscalls.h
      unix/loop.c
      unix/loop-watcher.c
      unix/pipe.c
      unix/poll.c
      unix/process.c
      unix/proctitle.c
      unix/signal.c
      unix/spinlock.h
      unix/stream.c
      unix/tcp.c
      unix/thread.c
      unix/threadpool.c
      unix/timer.c
      unix/tty.c
      unix/udp.c
  )
endif()

add_library(${TARGET_NAME} ${SOURCE_FILES})

if(WIN32)
  target_link_libraries(${TARGET_NAME} ws2_32.lib)
  target_link_libraries(${TARGET_NAME} iphlpapi.lib)
else()
  target_link_libraries(${TARGET_NAME} m)
  target_link_libraries(${TARGET_NAME} pthread)
  target_link_libraries(${TARGET_NAME} dl)
  target_link_libraries(${TARGET_NAME} rt)
endif()

install(TARGETS ${TARGET_NAME} DESTINATION ./)
