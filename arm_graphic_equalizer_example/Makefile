#################################################################################
# Copyright (C) 2020 Maxim Integrated Products, Inc., All Rights Reserved.
#
#   Licensed under the Apache License, Version 2.0 (the "License");
#   you may not use this file except in compliance with the License.
#   You may obtain a copy of the License at
#
#       http://www.apache.org/licenses/LICENSE-2.0
#
#   Unless required by applicable law or agreed to in writing, software
#   distributed under the License is distributed on an "AS IS" BASIS,
#   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#   See the License for the specific language governing permissions and
#   limitations under the License.
#
#################################################################################
# CONFIG

# This is the name of the build output file
ifeq "$(PROJECT)" ""
PROJECT=arm_graphic_equalizer_example
endif

# Specify the target processor
ifeq "$(TARGET)" ""
TARGET=MAX32665
endif
# Valid values :
# 	MAX32665 (targets MAX32665 - max32668)
#	MAX32660
#	MAX32650
#	MAX32625
#	MAX32620
#	MAX32600
#	MAX32520
# ** Don't forget to change your debug config to match!  ** (See the readme for help)


#FORCE_FP_HW_ACCEL = 1
# Uncomment the line above to force the use of hardware-accelerated floating-point instructions.  This forces the -mfloat-abi=hard compiler flag.
# By default, this is disabled and the compiler is allowed to generate floating-point instructions but it uses the soft-float abi.  This allows integer
# and floating-point code to co-mingle, but sacrifices some performance when they do.  See https://embeddedartistry.com/blog/2017/10/11/demystifying-arm-floating-point-compiler-options/
# for a great explanation.  ** Currently, this option only has an affect on the MAX32665 target. **

#################################################################################

# Create Target name variables
TARGET_UC:=$(shell echo $(TARGET) | tr a-z A-Z)
TARGET_LC:=$(shell echo $(TARGET) | tr A-Z a-z)

# Select 'GCC' or 'IAR' compiler
COMPILER=GCC

# Uncomment for verbose build output
VERBOSE = 1

# Specify the board used
ifeq "$(BOARD)" ""
BOARD=EvKit_V1
endif

# This is the path to the CMSIS root directory
ifeq "$(MAXIM_PATH)" ""
LIBS_DIR=../../../Libraries
else
LIBS_DIR=/$(subst \,/,$(subst :,,$(MAXIM_PATH))/Firmware/$(TARGET_UC)/Libraries)
endif
CMSIS_ROOT=$(LIBS_DIR)/CMSIS

# Source files for this test (add path to VPATH below)
SRCS  += arm_graphic_equalizer_example_q31.c
SRCS  += arm_graphic_equalizer_data.c
SRCS  += math_helper.c

# Where to find source files for this test
VPATH=.

# Where to find header files for this test
IPATH = .

# Enable assertion checking for development
PROJ_CFLAGS+=-DMXC_ASSERT_ENABLE 

# Add ARM CM4 flag for arm_math.h
PROJ_CFLAGS+=-DARM_MATH_CM4

# Enable printfs in the example project
PROJ_CFLAGS+=-DSEMIHOSTING

# Tell core_cm4.h that our CPU has an FPU by defining __FPU_PRESENT
PROJ_CFLAGS+=-D__FPU_PRESENT

# Provide linker flags to link against ARM's math lib
PROJ_LDFLAGS += -L$(CMSIS_ROOT)/Lib/GCC
# Some of the makefiles for our micros are currently forcing -mfloat-abi=softfp, so for these we need to link against arm_cortex_M4l_math instead of arm_cortexM4lf_math.
# These micros are losing some performance by not using the hardware accelerated floating point math, but are more easily compatible against other libraries.
ifeq "$(TARGET)" "MAX32665"
ifneq "$(FORCE_FP_HW_ACCEL)" ""
PROJ_LIBS = arm_cortexM4lf_math
MFLOAT_FLAGS = hard
export MFLOAT_FLAGS
else
PROJ_LIBS = arm_cortexM4l_math
endif

else ifeq "$(TARGET)" "MAX32660"
PROJ_LIBS = arm_cortexM4lf_math

else
PROJ_LIBS = arm_cortexM4l_math
endif

# Specify the target revision to override default
# "A2" in ASCII
# TARGET_REV=0x4132

# Use this variables to specify and alternate tool path
#TOOL_DIR=/opt/gcc-arm-none-eabi-4_8-2013q4/bin

# Use these variables to add project specific tool options
#PROJ_CFLAGS+=--specs=nano.specs
#PROJ_LDFLAGS+=--specs=nano.specs

# Point this variable to a startup file to override the default file
#STARTUPFILE=start.S

MXC_OPTIMIZE_CFLAGS=-O1

# Point this variable to a linker file to override the default file
# LINKERFILE=$(CMSIS_ROOT)/Device/Maxim/$(TARGET_UC)/Source/GCC/$(TARGET_LC).ld

################################################################################
# Include external library makefiles here

# Include the BSP
BOARD_DIR=$(LIBS_DIR)/Boards/$(BOARD)
include $(BOARD_DIR)/board.mk

# Include the peripheral driver
PERIPH_DRIVER_DIR=$(LIBS_DIR)/$(TARGET_UC)PeriphDriver
include $(PERIPH_DRIVER_DIR)/periphdriver.mk

################################################################################
# Include the rules for building for this target. All other makefiles should be
# included before this one.
include $(CMSIS_ROOT)/Device/Maxim/$(TARGET_UC)/Source/$(COMPILER)/$(TARGET_LC).mk

# The rule to clean out all the build products.
distclean: clean
	$(MAKE) -C ${PERIPH_DRIVER_DIR} clean
