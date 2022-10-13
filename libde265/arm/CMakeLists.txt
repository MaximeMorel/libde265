# This file has been modified by Adobe.
# All modifications are Copyright 2022 Adobe.

set(arm_sources
  arm.cc arm.h
)

set(arm_neon_sources
  asm.S cpudetect.S hevcdsp_qpel_neon.S neon.S
)

add_library(arm OBJECT ${arm_sources})

add_library(arm_neon OBJECT ${arm_neon_sources})

set(neon_flags "")

if(NOT MSVC)
  set(neon_flags "${neon_flags} -mfpu=neon")
endif()

set(ARM_OBJECTS $<TARGET_OBJECTS:arm> $<TARGET_OBJECTS:arm_neon> PARENT_SCOPE)

set_target_properties(arm_neon PROPERTIES COMPILE_FLAGS "${neon_flags}")
set_target_properties(arm_neon PROPERTIES LINKER_LANGUAGE ASM)
