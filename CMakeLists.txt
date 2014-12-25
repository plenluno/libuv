# Copyright (c) 2013-2014 Plenluno All rights reserved.

cmake_minimum_required(VERSION 2.8)

project(uv)

# variables --------------------------------------------------------------------

## uv-include
set(uv-include
    include
    src
)

## uv-src
set(uv-src
    src/fs-poll.c
    src/inet.c
    src/threadpool.c
    src/uv-common.c
    src/version.c
)

if(UNIX)
    set(uv-src
        ${uv-src}
        src/unix/async.c
        src/unix/core.c
        src/unix/dl.c
        src/unix/fs.c
        src/unix/getaddrinfo.c
        src/unix/getnameinfo.c
        src/unix/loop.c
        src/unix/loop-watcher.c
        src/unix/pipe.c
        src/unix/poll.c
        src/unix/process.c
        src/unix/signal.c
        src/unix/stream.c
        src/unix/tcp.c
        src/unix/thread.c
        src/unix/timer.c
        src/unix/tty.c
        src/unix/udp.c
    )
else(UNIX)
    set(uv-src
        ${uv-src}
        src/win/async.c
        src/win/core.c
        src/win/dl.c
        src/win/error.c
        src/win/fs.c
        src/win/fs-event.c
        src/win/getaddrinfo.c
        src/win/getnameinfo.c
        src/win/handle.c
        src/win/loop-watcher.c
        src/win/pipe.c
        src/win/thread.c
        src/win/poll.c
        src/win/process.c
        src/win/process-stdio.c
        src/win/req.c
        src/win/signal.c
        src/win/stream.c
        src/win/tcp.c
        src/win/tty.c
        src/win/timer.c
        src/win/udp.c
        src/win/util.c
        src/win/winapi.c
        src/win/winsock.c
    )
endif(UNIX)

if(APPLE)
    set(uv-src
        ${uv-src}
        src/unix/darwin.c
        src/unix/fsevents.c
        src/unix/kqueue.c
        src/unix/proctitle.c
        src/unix/darwin-proctitle.c
    )
endif(APPLE)

if(CMAKE_SYSTEM MATCHES "Linux")
    set(uv-src
        ${uv-src}
        src/unix/linux-core.c
        src/unix/linux-inotify.c
        src/unix/linux-syscalls.c
        src/unix/proctitle.c
    )
endif(CMAKE_SYSTEM MATCHES "Linux")

## uv-deps
if(UNIX)
    set(uv-deps
        m
        pthread
    )
else(UNIX)
    set(uv-deps
        advapi32
        iphlpapi
        psapi
        shell32
        ws2_32
    )
endif(UNIX)

if(CMAKE_SYSTEM MATCHES "Linux")
    set(uv-deps
        ${uv-deps}
        dl
        rt
    )
endif(CMAKE_SYSTEM MATCHES "Linux")

## uv-cflags
if(UNIX)
    set(uv-cflags
        --std=gnu89
        -g
        -pedantic
        -D_FILE_OFFSET_BITS=64
        -D_GNU_SOURCE
        -D_LARGEFILE_SOURCE
        -Wall
        -Wextra
        -Wno-unused-parameter
    )

    if(NOT APPLE)
        set(uv-cflags
            ${uv-cflags}
            -Wstrict-aliasing
        )
    endif(NOT APPLE)
else(UNIX)
    set(uv-cflags
        /D_GNU_SOURCE
        /D_WIN32_WINNT=0x0600
    )
endif(UNIX)

if(APPLE)
    set(uv-cflags
        ${uv-cflags}
        -D_DARWIN_UNLIMITED_SELECT=1
        -D_DARWIN_USE_64_BIT_INODE=1
        -Wno-gnu-folding-constant
        -Wno-dollar-in-identifier-extension
    )
endif(APPLE)

if(CMAKE_SYSTEM MATCHES "Linux")
    set(uv-cflags
        ${uv-cflags}
        -D_POSIX_C_SOURCE=200112
    )
endif(CMAKE_SYSTEM MATCHES "Linux")

string(REPLACE ";" " " uv-cflags "${uv-cflags}")

# build ------------------------------------------------------------------------

include_directories(
    ${uv-include}
)

add_library(uv STATIC
    ${uv-src}
)

target_link_libraries(uv
    ${uv-deps}
)

set_target_properties(uv PROPERTIES
    COMPILE_FLAGS "${uv-cflags}"
)
