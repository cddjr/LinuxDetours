cmake_minimum_required(VERSION 3.10)

project(linuxDetours LANGUAGES CXX)

set(sources
    LinuxDetours/barrier.cpp
    LinuxDetours/detours.cpp
    LinuxDetours/disasm.cpp
    LinuxDetours/plthook_elf.cpp
)
if(BUILD_ARM)
    list(APPEND sources
        LinuxDetours/trampoline_arm.cpp
    )
else()
    list(APPEND sources
        LinuxDetours/trampoline_x86.cpp
    )
endif()

add_library(linuxDetours STATIC ${sources})

set_target_properties(linuxDetours
    PROPERTIES
    CXX_STANDARD 11
    CXX_VISIBILITY_PRESET hidden
    VISIBILITY_INLINES_HIDDEN ON
    POSITION_INDEPENDENT_CODE ON
)

if(BUILD_ARM)
    target_compile_definitions(linuxDetours
        PUBLIC
        _ARM64_
        _UNIX64
    )
else()
    target_compile_definitions(linuxDetours
        PUBLIC
        _AMD64_
        _UNIX64
    )
endif()

target_compile_options(linuxDetours
    PRIVATE
#    -g -O0 -fno-omit-frame-pointer
)

target_include_directories(linuxDetours
    PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/LinuxDetours>
)

target_link_libraries(linuxDetours
    PRIVATE
    pthread
    dl
)