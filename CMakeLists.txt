# Each Makefile lines are reported in this file with "->"
# and the CMake implementation follows

# -> CC := gcc
# -> BIN := posix_demo
project(posix_demo C)
cmake_minimum_required(VERSION 3.5)

# -> BUILD_DIR := build
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/build)

# For debug, no change detected ...
set(CMAKE_VERBOSE_MAKEFILE ON)

# -> FREERTOS_DIR_REL := ./FreeRTOS-Kernel
# -> FREERTOS_DIR := $(abspath $(FREERTOS_DIR_REL))
set(FREERTOS_DIR "${CMAKE_CURRENT_SOURCE_DIR}/FreeRTOS-Kernel")

# -> PORT_DIR_REL := ./port
# -> PORT_DIR := $(abspath $(PORT_DIR_REL))
set(PORT_DIR "${CMAKE_CURRENT_SOURCE_DIR}/port")


# -> INCLUDE_DIRS := -I.
# -> INCLUDE_DIRS += -I${FREERTOS_DIR}/include
# -> INCLUDE_DIRS += -I${FREERTOS_DIR}/GCC/Posix
# -> INCLUDE_DIRS += -I${FREERTOS_DIR}/GCC/Posix/utils
include_directories(
    .
    ${FREERTOS_DIR}/include
    ${PORT_DIR}/GCC/Posix
    ${PORT_DIR}/GCC/Posix/utils
)

# -> SOURCE_FILES := $(wildcard *.c)
# -> SOURCE_FILES += $(wildcard ${FREERTOS_DIR}/*.c)
FILE(GLOB FREERTOS_SOURCES
    ${FREERTOS_DIR}/*.c
)

# Memory manager (use malloc() / free())
# -> SOURCE_FILES += ${FREERTOS_DIR}/portable/MemMang/heap_3.c
list(APPEND FREERTOS_SOURCES "${FREERTOS_DIR}/portable/MemMang/heap_3.c")

# Posix port
# -> SOURCE_FILES += ${PORT_DIR}/GCC/Posix/utils/wait_for_event.c
# -> SOURCE_FILES += ${PORT_DIR}/GCC/Posix/port.c
list(APPEND FREERTOS_SOURCES "${PORT_DIR}/GCC/Posix/utils/wait_for_event.c")
list(APPEND FREERTOS_SOURCES "${PORT_DIR}/GCC/Posix/port.c")

# -> CFLAGS := -ggdb3 -O0 -DprojCOVERAGE_TEST=0 -D_WINDOWS_
add_definitions(-DprojCOVERAGE_TEST=0)
add_definitions(-D_WINDOWS_)
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -ggdb3 -O0")

# -> LDFLAGS := -ggdb3 -O0 -pthread
# pthread included after
set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -ggdb3 -O0 -pthread")

# Debug
message("Project: ${PROJECT_NAME}")
message("Sources: ${FREERTOS_SOURCES}")

# -> End of Makefile : Compilation

# Create executable with demo code files
add_executable(${PROJECT_NAME}
    main.c
    FreeRTOSConfig.h
    console.h console.c
)

# Link FreeRTOS as a static library
add_library(freertos STATIC ${FREERTOS_SOURCES})
target_link_libraries(${PROJECT_NAME} freertos)

## Add pthread
# target_link_libraries(${PROJECT_NAME} pthread)
