cmake_minimum_required(VERSION 3.4.3)

project(enet LANGUAGES C)

# The "configure" step.
include(CheckFunctionExists)
include(CheckStructHasMember)
include(CheckTypeSize)
check_function_exists("fcntl" HAS_FCNTL)
check_function_exists("poll" HAS_POLL)
check_function_exists("getaddrinfo" HAS_GETADDRINFO)
check_function_exists("getnameinfo" HAS_GETNAMEINFO)
check_function_exists("gethostbyname_r" HAS_GETHOSTBYNAME_R)
check_function_exists("gethostbyaddr_r" HAS_GETHOSTBYADDR_R)
check_function_exists("inet_pton" HAS_INET_PTON)
check_function_exists("inet_ntop" HAS_INET_NTOP)
check_struct_has_member("struct msghdr" "msg_flags" "sys/types.h;sys/socket.h" HAS_MSGHDR_FLAGS)
set(CMAKE_EXTRA_INCLUDE_FILES "sys/types.h" "sys/socket.h")
check_type_size("socklen_t" HAS_SOCKLEN_T BUILTIN_TYPES_ONLY)
unset(CMAKE_EXTRA_INCLUDE_FILES)

set(ENET_INCLUDE_DIR ${PROJECT_SOURCE_DIR}/include)
set(ENET_SRC_FILES
    callbacks.c
    compress.c
    host.c
    list.c
    packet.c
    peer.c
    protocol.c
    address.c
    unix.c
    win32.c
    interop_helpers.c
)

add_library(enet ${ENET_SRC_FILES})
target_include_directories(enet PUBLIC ${ENET_INCLUDE_DIR})

if (BUILD_SHARED_LIBS)
    target_compile_definitions(enet PRIVATE ENET_DLL=1)
endif()

if (WIN32)
    if (BUILD_SHARED_LIBS)
        set_property(TARGET enet PROPERTY WINDOWS_EXPORT_ALL_SYMBOLS ON)
    endif()

    target_compile_definitions(enet PRIVATE _CRT_SECURE_NO_WARNINGS)
    target_link_libraries(enet winmm ws2_32)
endif()

if(MSVC)
	target_compile_options(enet PRIVATE -W3)
else()	
    target_compile_options(enet PRIVATE -Wno-error)
endif()

if(HAS_FCNTL)
    target_compile_definitions(enet PRIVATE HAS_FCNTL=1)
endif()
if(HAS_POLL)
    target_compile_definitions(enet PRIVATE HAS_POLL=1)
endif()
if(HAS_GETNAMEINFO)
    target_compile_definitions(enet PRIVATE HAS_GETNAMEINFO=1)
endif()
if(HAS_GETADDRINFO)
    target_compile_definitions(enet PRIVATE HAS_GETADDRINFO=1)
endif()
if(HAS_GETHOSTBYNAME_R)
    target_compile_definitions(enet PRIVATE HAS_GETHOSTBYNAME_R=1)
endif()
if(HAS_GETHOSTBYADDR_R)
    target_compile_definitions(enet PRIVATE HAS_GETHOSTBYADDR_R=1)
endif()
if(HAS_INET_PTON)
    target_compile_definitions(enet PRIVATE HAS_INET_PTON=1)
endif()
if(HAS_INET_NTOP)
    target_compile_definitions(enet PRIVATE HAS_INET_NTOP=1)
endif()
if(HAS_MSGHDR_FLAGS)
    target_compile_definitions(enet PRIVATE HAS_MSGHDR_FLAGS=1)
endif()
if(HAS_SOCKLEN_T)
    target_compile_definitions(enet PRIVATE HAS_SOCKLEN_T=1)
endif()