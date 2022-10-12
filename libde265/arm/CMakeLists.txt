set (arm_sources 
  arm.cc arm.h
)

set (arm_neon_sources 
  asm.S cpudetect.S hevcdsp_qpel_neon.S neon.S
)

add_library(arm OBJECT ${arm_sources})

add_library(arm_neon OBJECT ${arm_neon_sources})

set(neon_flags "")

if(NOT MSVC)
  if(CMAKE_SIZEOF_VOID_P EQUAL 8)
    set(neon_flags "${neon_flags} -mfpu=neon")
  else(CMAKE_SIZEOF_VOID_P EQUAL 8)
    set(neon_flags "${neon_flags} -mfpu=neon")
  endif(CMAKE_SIZEOF_VOID_P EQUAL 8)
endif()

set(ARM_OBJECTS $<TARGET_OBJECTS:arm> $<TARGET_OBJECTS:arm_neon> PARENT_SCOPE)

SET_TARGET_PROPERTIES(arm_neon PROPERTIES COMPILE_FLAGS "${neon_flags}")
SET_TARGET_PROPERTIES(arm_neon PROPERTIES LINKER_LANGUAGE ASM)
