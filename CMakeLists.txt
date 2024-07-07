cmake_minimum_required(VERSION 3.27)
project(weactblecore-ch592f-blinky C CXX ASM)

set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)
set(LINKER_SCRIPT ${CMAKE_CURRENT_LIST_DIR}/core/ch592f.ld)

include(toolchain/riscv-gcc-toolchain.cmake)

include_directories(
  app
  board
  core/FlashROM/include
  core/RVMSIS/include
  core/StdPeriphDriver/include
)

file(GLOB_RECURSE SOURCES 
  "app/*.*"
  "board/*.*"
  "core/RVMSIS/src*.*"
  "core/Startup/*.*"
  "core/StdPeriphDriver/src/*.*"
)

add_executable(${PROJECT_NAME}.elf ${SOURCES} ${LINKER_SCRIPT})

target_link_directories(${PROJECT_NAME}.elf PRIVATE core/FlashROM/lib)
target_link_libraries(${PROJECT_NAME}.elf -l:libISP592.a)

set(HEX_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.hex)
set(BIN_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.bin)

add_custom_command(TARGET ${PROJECT_NAME}.elf POST_BUILD
        COMMAND ${CMAKE_OBJCOPY} -Oihex $<TARGET_FILE:${PROJECT_NAME}.elf> ${HEX_FILE}
        COMMAND ${CMAKE_OBJCOPY} -Obinary $<TARGET_FILE:${PROJECT_NAME}.elf> ${BIN_FILE}
        COMMENT "Building ${HEX_FILE}
Building ${BIN_FILE}")
