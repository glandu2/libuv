cmake_minimum_required(VERSION 2.8)
project(uv-test)

set(TEST_TARGET_NAME "run-tests")
set(BENCH_TARGET_NAME "run-benchmarks")

include(../DetectOS.cmake)

set(SOURCE_FILES_COMMON
  blackhole-server.c
  echo-server.c
  runner.c
  runner.h
  task.h
)

if(DETECTED_OS_WIN)
  set(SOURCE_FILES_RUNNER
    runner-win.c
    runner-win.h
    ../src/win/snprintf.c
  )
else()
  set(SOURCE_FILES_RUNNER
    runner-unix.c
    runner-unix.h
  )
endif()


# Unit tests
set(SOURCE_FILES_TEST
  run-tests.c
  test-list.h
  test-active.c
  test-async-null-cb.c
  test-async.c
  test-barrier.c
  test-callback-order.c
  test-callback-stack.c
  test-close-fd.c
  test-close-order.c
  test-condvar.c
  test-connection-fail.c
  test-cwd-and-chdir.c
  test-default-loop-close.c
  test-delayed-accept.c
  test-dlerror.c
  test-embed.c
  test-emfile.c
  test-error.c
  test-fail-always.c
  test-fs-event.c
  test-fs-poll.c
  test-fs.c
  test-get-currentexe.c
  test-get-loadavg.c
  test-get-memory.c
  test-getaddrinfo.c
  test-getnameinfo.c
  test-getsockname.c
  test-handle-fileno.c
  test-homedir.c
  test-hrtime.c
  test-idle.c
  test-ip4-addr.c
  test-ip6-addr.c
  test-ipc-send-recv.c
  test-ipc.c
  test-loop-alive.c
  test-loop-close.c
  test-loop-configure.c
  test-loop-handles.c
  test-loop-stop.c
  test-loop-time.c
  test-multiple-listen.c
  test-mutexes.c
  test-osx-select.c
  test-pass-always.c
  test-ping-pong.c
  test-pipe-bind-error.c
  test-pipe-close-stdout-read-stdin.c
  test-pipe-connect-error.c
  test-pipe-connect-multiple.c
  test-pipe-connect-prepare.c
  test-pipe-getsockname.c
  test-pipe-pending-instances.c
  test-pipe-sendmsg.c
  test-pipe-server-close.c
  test-pipe-set-non-blocking.c
  test-platform-output.c
  test-poll-close-doesnt-corrupt-stack.c
  test-poll-close.c
  test-poll-closesocket.c
  test-poll.c
  test-process-title.c
  test-queue-foreach-delete.c
  test-ref.c
  test-run-nowait.c
  test-run-once.c
  test-semaphore.c
  test-shutdown-close.c
  test-shutdown-eof.c
  test-shutdown-twice.c
  test-signal-multiple-loops.c
  test-signal.c
  test-socket-buffer-size.c
  test-spawn.c
  test-stdio-over-pipes.c
  test-tcp-bind-error.c
  test-tcp-bind6-error.c
  test-tcp-close-accept.c
  test-tcp-close-while-connecting.c
  test-tcp-close.c
  test-tcp-connect-error-after-write.c
  test-tcp-connect-error.c
  test-tcp-connect-timeout.c
  test-tcp-connect6-error.c
  test-tcp-create-socket-early.c
  test-tcp-flags.c
  test-tcp-oob.c
  test-tcp-open.c
  test-tcp-read-stop.c
  test-tcp-shutdown-after-write.c
  test-tcp-try-write.c
  test-tcp-unexpected-read.c
  test-tcp-write-after-connect.c
  test-tcp-write-fail.c
  test-tcp-write-queue-order.c
  test-tcp-write-to-half-open-connection.c
  test-tcp-writealot.c
  test-thread-equal.c
  test-thread.c
  test-threadpool-cancel.c
  test-threadpool.c
  test-timer-again.c
  test-timer-from-check.c
  test-timer.c
  test-tty.c
  test-udp-bind.c
  test-udp-create-socket-early.c
  test-udp-dgram-too-big.c
  test-udp-ipv6.c
  test-udp-multicast-interface.c
  test-udp-multicast-interface6.c
  test-udp-multicast-join.c
  test-udp-multicast-join6.c
  test-udp-multicast-ttl.c
  test-udp-open.c
  test-udp-options.c
  test-udp-send-and-recv.c
  test-udp-send-immediate.c
  test-udp-send-unreachable.c
  test-udp-try-send.c
  test-walk-handles.c
  test-watcher-cross-stop.c
)
add_executable(${TEST_TARGET_NAME} ${SOURCE_FILES_TEST} ${SOURCE_FILES_COMMON} ${SOURCE_FILES_RUNNER})
target_link_libraries(${TEST_TARGET_NAME} uv)
if(NOT DETECTED_OS_WIN32)
  target_compile_definitions(${TEST_TARGET_NAME} PRIVATE _GNU_SOURCE)
endif()

# make test-fs.c compile
if(DETECTED_OS_SOLARIS)
  target_compile_definitions(${TEST_TARGET_NAME} PRIVATE __EXTENSIONS__ _XOPEN_SOURCE=500)
elseif(DETECTED_OS_AIX)
  target_compile_definitions(${TEST_TARGET_NAME} PRIVATE _ALL_SOURCE _XOPEN_SOURCE=500)
endif()


# Benchmark tests
set(SOURCE_FILES_BENCH
  dns-server.c
  run-benchmarks.c
  benchmark-list.h
  benchmark-async-pummel.c
  benchmark-async.c
  benchmark-fs-stat.c
  benchmark-getaddrinfo.c
  benchmark-loop-count.c
  benchmark-million-async.c
  benchmark-million-timers.c
  benchmark-multi-accept.c
  benchmark-ping-pongs.c
  benchmark-pound.c
  benchmark-pump.c
  benchmark-sizes.c
  benchmark-spawn.c
  benchmark-tcp-write-batch.c
  benchmark-thread.c
  benchmark-udp-pummel.c
)
add_executable(${BENCH_TARGET_NAME} ${SOURCE_FILES_BENCH} ${SOURCE_FILES_COMMON} ${SOURCE_FILES_RUNNER})
target_link_libraries(${BENCH_TARGET_NAME} uv)
if(NOT DETECTED_OS_WIN32)
  target_compile_definitions(${TEST_TARGET_NAME} PRIVATE _GNU_SOURCE)
endif()
