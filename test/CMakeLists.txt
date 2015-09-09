cmake_minimum_required(VERSION 2.8)
project(uv-test)

include(../CMakeLists.txt)

set(TEST_TARGET_NAME "uv_test")
set(BENCH_TARGET_NAME "uv_bench")

if(DETECTED_OS_MAC)
  add_definitions(-D_DARWIN_USE_64_BIT_INODE=1)
endif()

if(DETECTED_OS_LINUX)
  add_definitions(-D_POSIX_C_SOURCE=200112)
endif()


set(SOURCE_FILES
	blackhole-server.c
	echo-server.c
	run-tests.c
	runner.c
	runner.h
	test-get-loadavg.c
	task.h
	test-active.c
	test-async.c
	test-async-null-cb.c
	test-callback-stack.c
	test-callback-order.c
	test-close-fd.c
	test-close-order.c
	test-connection-fail.c
	test-cwd-and-chdir.c
	test-default-loop-close.c
	test-delayed-accept.c
	test-error.c
	test-embed.c
	test-emfile.c
	test-fail-always.c
	test-fs.c
	test-fs-event.c
	test-get-currentexe.c
	test-get-memory.c
	test-getaddrinfo.c
	test-getnameinfo.c
	test-getsockname.c
	test-handle-fileno.c
	test-homedir.c
	test-hrtime.c
	test-idle.c
	test-ip6-addr.c
	test-ipc.c
	test-ipc-send-recv.c
	test-list.h
	test-loop-handles.c
	test-loop-alive.c
	test-loop-close.c
	test-loop-stop.c
	test-loop-time.c
	test-loop-configure.c
	test-walk-handles.c
	test-watcher-cross-stop.c
	test-multiple-listen.c
	test-osx-select.c
	test-pass-always.c
	test-ping-pong.c
	test-pipe-bind-error.c
	test-pipe-connect-error.c
	test-pipe-connect-prepare.c
	test-pipe-getsockname.c
	test-pipe-sendmsg.c
	test-pipe-server-close.c
	test-pipe-close-stdout-read-stdin.c
	test-pipe-set-non-blocking.c
	test-platform-output.c
	test-poll.c
	test-poll-close.c
	test-poll-close-doesnt-corrupt-stack.c
	test-poll-closesocket.c
	test-process-title.c
	test-ref.c
	test-run-nowait.c
	test-run-once.c
	test-semaphore.c
	test-shutdown-close.c
	test-shutdown-eof.c
	test-shutdown-twice.c
	test-signal.c
	test-signal-multiple-loops.c
	test-socket-buffer-size.c
	test-spawn.c
	test-fs-poll.c
	test-stdio-over-pipes.c
	test-tcp-bind-error.c
	test-tcp-bind6-error.c
	test-tcp-close.c
	test-tcp-close-accept.c
	test-tcp-close-while-connecting.c
	test-tcp-connect-error-after-write.c
	test-tcp-shutdown-after-write.c
	test-tcp-flags.c
	test-tcp-connect-error.c
	test-tcp-connect-timeout.c
	test-tcp-connect6-error.c
	test-tcp-open.c
	test-tcp-write-to-half-open-connection.c
	test-tcp-write-after-connect.c
	test-tcp-writealot.c
	test-tcp-write-fail.c
	test-tcp-try-write.c
	test-tcp-unexpected-read.c
	test-tcp-oob.c
	test-tcp-read-stop.c
	test-tcp-write-queue-order.c
	test-threadpool.c
	test-threadpool-cancel.c
	test-thread-equal.c
	test-mutexes.c
	test-thread.c
	test-barrier.c
	test-condvar.c
	test-timer-again.c
	test-timer-from-check.c
	test-timer.c
	test-tty.c
	test-udp-bind.c
	test-udp-dgram-too-big.c
	test-udp-ipv6.c
	test-udp-open.c
	test-udp-options.c
	test-udp-send-and-recv.c
	test-udp-send-immediate.c
	test-udp-send-unreachable.c
	test-udp-multicast-join.c
	test-udp-multicast-join6.c
	test-dlerror.c
	test-udp-multicast-ttl.c
	test-ip4-addr.c
	test-ip6-addr.c
	test-udp-multicast-interface.c
	test-udp-multicast-interface6.c
	test-udp-try-send.c
)

if(DETECTED_OS_WIN)
  set(SOURCE_FILES ${SOURCE_FILES}
	runner-win.c
	runner-win.h
  )
  add_definitions(-D_WIN32_WINNT=0x0600)
else()
  set(SOURCE_FILES ${SOURCE_FILES}
	runner-unix.c
	runner-unix.h
  )
endif()

if(DETECTED_OS_SOLARIS)
  add_definitions(-D__EXTENSIONS__ -D_XOPEN_SOURCE=500)
endif()

if(DETECTED_OS_AIX)
  add_definitions(-D_ALL_SOURCE -D_XOPEN_SOURCE=500)
endif()

add_executable(${TEST_TARGET_NAME} ${SOURCE_FILES})
target_link_libraries(${TEST_TARGET_NAME} uv)
if(DETECTED_OS_WIN)
  target_link_libraries(${BENCH_TARGET_NAME} ws2_32)
endif()

# Benchmark tests
set(SOURCE_FILES_BENCH
    benchmark-async.c
    benchmark-async-pummel.c
    benchmark-fs-stat.c
    benchmark-getaddrinfo.c
    benchmark-list.h
    benchmark-loop-count.c
    benchmark-million-async.c
    benchmark-million-timers.c
    benchmark-multi-accept.c
    benchmark-ping-pongs.c
    benchmark-pound.c
    benchmark-pump.c
    benchmark-sizes.c
    benchmark-spawn.c
    benchmark-thread.c
    benchmark-tcp-write-batch.c
    benchmark-udp-pummel.c
    dns-server.c
    echo-server.c
    blackhole-server.c
    run-benchmarks.c
    runner.c
    runner.h
    task.h
)

if(DETECTED_OS_WIN)
  set(SOURCE_FILES_BENCH ${SOURCE_FILES_BENCH}
	runner-win.c
	runner-win.h
  )
else()
  set(SOURCE_FILES_BENCH ${SOURCE_FILES_BENCH}
	runner-unix.c
	runner-unix.h
  )
endif()

add_executable(${BENCH_TARGET_NAME} ${SOURCE_FILES_BENCH})
target_link_libraries(${BENCH_TARGET_NAME} uv)
if(DETECTED_OS_WIN)
  target_link_libraries(${BENCH_TARGET_NAME} ws2_32)
endif()
