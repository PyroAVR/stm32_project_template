project('stm32_template', 'c')

# dependencies
dependencies = []

# Project include path.
local_headers = ['include']

# List of C sources to compile. Relative to project root.
c_sources = [
    'src/button.c',
    'src/led.c',
    'src/main.c'
]

# List of ASM sources to compile.  Relative to project root.
asm_sources = [
]

### DON'T EDIT BELOW THIS LINE ###
add_project_arguments('-D' + get_option('mcu'), language: 'c')

objcopy = find_program('objcopy')


# These are additional sources that are needed for normal system init, provided
# by STMicro in the BSP.
c_sources = c_sources + ['core/src/system_stm32l4xx.c']
asm_sources = asm_sources + ['core/src/startup_stm32l476xx.s']

# this is a hack - these must be copied in from the BSP.
_includes = local_headers + [
    'core/include/bsp/Drivers/STM32L4xx_HAL_Driver/Inc',
    'core/include/bsp/Drivers/CMSIS/Core/Include',
    'core/include/bsp/Drivers/CMSIS/Include',
    'core/include/bsp/Drivers/CMSIS/Device/ST/STM32L4xx/Include'
]
_incl_dirs = include_directories(_includes)

asm_static_object = static_library(
    'asm_static_object',
    asm_sources,
    include_directories: _incl_dirs,
    c_args: get_option('c_args') + ['-x', 'assembler-with-cpp'],
    install: false,
)

main = executable(
    'main.elf',
    c_sources,
    include_directories: _incl_dirs,
    link_with: asm_static_object,
    dependencies: dependencies,
    install: true,
    install_dir: false,
)

# copy everything - custom linker script takes care of what goes where
# and non-flashing sections.
main_bin = custom_target(
    'main.bin',
    command: [
        objcopy,
        '-O', 'binary',
        '@INPUT@', '@OUTPUT@'
    ],
    input: main,
    output: 'main.bin',
    build_by_default: true
)
