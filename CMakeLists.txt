#此文件从模板自动生成! 请勿更改!
set(CMAKE_SYSTEM_NAME Generic)
set(CMAKE_SYSTEM_VERSION 1)
cmake_minimum_required(VERSION 3.21)

# specify cross compilers and tools
set(CMAKE_C_COMPILER arm-none-eabi-gcc)
set(CMAKE_CXX_COMPILER arm-none-eabi-g++)
set(CMAKE_ASM_COMPILER  arm-none-eabi-gcc)
set(CMAKE_AR arm-none-eabi-ar)
set(CMAKE_OBJCOPY arm-none-eabi-objcopy)
set(CMAKE_OBJDUMP arm-none-eabi-objdump)
set(SIZE arm-none-eabi-size)
set(CMAKE_TRY_COMPILE_TARGET_TYPE STATIC_LIBRARY)

# project settings
project(LVGL_Clion C CXX ASM)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_C_STANDARD 11)

#Uncomment for hardware floating point
add_compile_definitions(ARM_MATH_CM4;ARM_MATH_MATRIX_CHECK;ARM_MATH_ROUNDING)
add_compile_options(-mfloat-abi=hard -mfpu=fpv4-sp-d16)
add_link_options(-mfloat-abi=hard -mfpu=fpv4-sp-d16)

#Uncomment for software floating point
#add_compile_options(-mfloat-abi=soft)

add_compile_options(-mcpu=cortex-m4 -mthumb -mthumb-interwork)
add_compile_options(-ffunction-sections -fdata-sections -fno-common -fmessage-length=0)

# uncomment to mitigate c++17 absolute addresses warnings
#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-register")

# Enable assembler files preprocessing
add_compile_options($<$<COMPILE_LANGUAGE:ASM>:-x$<SEMICOLON>assembler-with-cpp>)

if ("${CMAKE_BUILD_TYPE}" STREQUAL "Release")
    message(STATUS "Maximum optimization for speed")
    add_compile_options(-Ofast)
elseif ("${CMAKE_BUILD_TYPE}" STREQUAL "RelWithDebInfo")
    message(STATUS "Maximum optimization for speed, debug info included")
    add_compile_options(-Ofast -g)
elseif ("${CMAKE_BUILD_TYPE}" STREQUAL "MinSizeRel")
    message(STATUS "Maximum optimization for size")
    add_compile_options(-Os)
else ()
    message(STATUS "Minimal optimization, debug info included")
    add_compile_options(-Og -g)
endif ()

include_directories(Core/Inc Drivers/STM32F4xx_HAL_Driver/Inc Drivers/STM32F4xx_HAL_Driver/Inc/Legacy Drivers/CMSIS/Device/ST/STM32F4xx/Include Drivers/CMSIS/Include
        GUI/lvgl
        GUI/lvgl/src
        GUI/lvgl/src/core
        GUI/lvgl/src/draw
        GUI/lvgl/src/extra
        GUI/lvgl/src/font
        GUI/lvgl/src/hal
        GUI/lvgl/src/misc
        GUI/lvgl/src/widgets
        )

add_definitions(-DUSE_HAL_DRIVER -DSTM32F407xx)

file(GLOB_RECURSE SOURCES "startup/*.*" "Drivers/*.*" "Core/*.*"
        "GUI/lvgl/*.*"
        "GUI/lvgl/src/*.*"
        "GUI/lvgl/src/core/*.*"
        "GUI/lvgl/src/draw/*.*"
        "GUI/lvgl/src/font/*.*"
        "GUI/lvgl/src/hal/*.*"
        "GUI/lvgl/src/misc/*.*"
        "GUI/lvgl/src/widgets/*.*"
        "GUI/lvgl/src/extra/*.*"
        "GUI/lvgl/src/extra/layouts/*.*"
        "GUI/lvgl/src/extra/layouts/flex/*.*"
        "GUI/lvgl/src/extra/layouts/grid/*.*"
        "GUI/lvgl/src/extra/others/*.*"
        "GUI/lvgl/src/extra/others/fragment/*.*"
        "GUI/lvgl/src/extra/others/gridnav/*.*"
        "GUI/lvgl/src/extra/others/imgfont/*.*"
        "GUI/lvgl/src/extra/others/monkey/*.*"
        "GUI/lvgl/src/extra/others/snapshot/*.*"
        "GUI/lvgl/src/extra/themes/*.*"
        "GUI/lvgl/src/extra/themes/basic/*.*"
        "GUI/lvgl/src/extra/themes/default/*.*"
        "GUI/lvgl/src/extra/themes/mono/*.*"
        "GUI/lvgl/src/extra/widgets/animimg/*.*"
        "GUI/lvgl/src/extra/widgets/calendar/*.*"
        "GUI/lvgl/src/extra/widgets/chart/*.*"
        "GUI/lvgl/src/extra/widgets/colorwheel/*.*"
        "GUI/lvgl/src/extra/widgets/imgbtn/*.*"
        "GUI/lvgl/src/extra/widgets/keyboard/*.*"
        "GUI/lvgl/src/extra/widgets/led/*.*"
        "GUI/lvgl/src/extra/widgets/list/*.*"
        "GUI/lvgl/src/extra/widgets/menu/*.*"
        "GUI/lvgl/src/extra/widgets/meter/*.*"
        "GUI/lvgl/src/extra/widgets/msgbox/*.*"
        "GUI/lvgl/src/extra/widgets/span/*.*"
        "GUI/lvgl/src/extra/widgets/spinbox/*.*"
        "GUI/lvgl/src/extra/widgets/spinner/*.*"
        "GUI/lvgl/src/extra/widgets/tabview/*.*"
        "GUI/lvgl/src/extra/widgets/tileview/*.*"
        "GUI/lvgl/src/extra/widgets/win/*.*"
        )

set(LINKER_SCRIPT ${CMAKE_SOURCE_DIR}/STM32F407VETx_FLASH.ld)

add_link_options(-Wl,-gc-sections,--print-memory-usage,-Map=${PROJECT_BINARY_DIR}/${PROJECT_NAME}.map)
add_link_options(-mcpu=cortex-m4 -mthumb -mthumb-interwork)
add_link_options(-T ${LINKER_SCRIPT})

add_executable(${PROJECT_NAME}.elf ${SOURCES} ${LINKER_SCRIPT})

set(HEX_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.hex)
set(BIN_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.bin)

add_custom_command(TARGET ${PROJECT_NAME}.elf POST_BUILD
        COMMAND ${CMAKE_OBJCOPY} -Oihex $<TARGET_FILE:${PROJECT_NAME}.elf> ${HEX_FILE}
        COMMAND ${CMAKE_OBJCOPY} -Obinary $<TARGET_FILE:${PROJECT_NAME}.elf> ${BIN_FILE}
        COMMENT "Building ${HEX_FILE}
Building ${BIN_FILE}")
