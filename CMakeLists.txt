cmake_minimum_required(VERSION 3.1)
project(libffi)

enable_language(ASM_MASM)

add_library(ffi STATIC
# WIN32:
#	src/x86/ffi.c src/x86/sysv.S src/x86/win32.S
# WIN64:
        src/x86/ffi.c ${CMAKE_BINARY_DIR}/win64.asm
    src/types.c
    src/prep_cif.c
    src/types.c
    src/raw_api.c
    src/java_raw_api.c
    src/closures.c
)

target_compile_definitions(ffi PUBLIC
    -DX86_WIN64
    -DFFI_BUILDING
)

set(VERSION 3.2.1)
set(HAVE_LONG_DOUBLE 0)
set(TARGET X86_WIN64)
set(HAVE_LONG_DOUBLE_VARIANT 0)
set(FFI_EXEC_TRAMPOLINE_TABLE 0)
configure_file(include/ffi.h.in ${CMAKE_BINARY_DIR}/ffi.h @ONLY)
configure_file(include/fficonfig.h.win32.in ${CMAKE_BINARY_DIR}/fficonfig.h)

# TODO: copy config.h from glib to CMAKE_BINARY_DIR

target_include_directories(ffi PRIVATE
    ${CMAKE_BINARY_DIR}
    include
    src/x86
)

add_custom_command(OUTPUT ${CMAKE_BINARY_DIR}/win64.asm
    COMMAND ${CMAKE_C_COMPILER} -nologo -EP -I ${CMAKE_BINARY_DIR} -I include -I src/x86 ${PROJECT_SOURCE_DIR}/src/x86/win64.S > ${CMAKE_BINARY_DIR}/win64.asm
    WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}
)
