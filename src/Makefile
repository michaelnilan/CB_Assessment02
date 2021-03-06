#******************************************************************************
# Copyright (C) 2017 by Alex Fosdick - University of Colorado
#
# Redistribution, modification or use of this software in source or binary
# forms is permitted as long as the files maintain this copyright. Users are 
# permitted to modify this and use it to learn about the field of embedded
# software. Alex Fosdick and the University of Colorado are not liable for any
# misuse of this material. 
#
#*****************************************************************************

#------------------------------------------------------------------------------
# A Makefile to support cross platform support over HOST machine and ARM 
#
# Use: make [TARGET] [PLATFORM-OVERRIDES]
#
# Build Targets:
#      <FILE>.o - Builds <FILE>.o object file
#      <FILE>.i - Builds the preprocesses file <FILE>.i
#      <FILE>.asm - Generate the assembly output of <FILE>.asm
#      
#      compile-all : Compiles all files
#      build       : Compile all object files and link to final executable and output size
#      clean       : Removes all (rm -f) generated and dummy files
# Platform Overrides:
#      CPU      - ARM Cortex Architecture (cortex-m4)
#      ARCH     - ARM Architecture (thumb)
#      SPECS    - Linker file (nosys.specs) 
#
#------------------------------------------------------------------------------
include sources.mk

ifeq ($(PLATFORM),MSP432)

        # Architectures Specific Flags ARM
        LINKER_FILE = ../msp432p401r.lds
		CPU = cortex-m4
		ARCH = thumb
		SPECS = nosys.specs
		ARMFLAGS = -mcpu=$(CPU) -m$(ARCH) --specs=$(SPECS) -march=armv7e-m -mfloat-abi=hard -mfpu=fpv4-sp-d16	

        # Compiler Flags and Defines
		CC = arm-none-eabi-gcc
		LD = arm-none-eabi-ld
		LDFLAGS = -Wl,-Map=$(TARGET).map -T $(LINKER_FILE)

		

		OBJDUMP = arm-none-eabi-objdump

		#Size 
		SIZE = arm-none-eabi-size 
else
        # Compiler Flags and Defines
		CC = gcc
		LD = ld
        LDFLAGS = -Wl,-Map=$(TARGET).map

        OBJDUMP = objdump

		#Size 
		SIZE = size		
endif

TARGET = c1m2

CFLAGS = -Wall -Werror -g -O0 -std=c99 -D$(PLATFORM) $(ARMFLAGS)
CPPFLAGS = -E
MAIN = main

OBJS = $(SOURCES:.c=.o)

PREPRO = $(SOURCES:.c=.i)

ASMS = $(SOURCES:.c=.asm)

DEPS = $(SOURCES:.c=.d)
 
FILES = *.asm *.i *.o *.d

	
%.o : %.c
	$(CC) $(INCLUDES) -c $< $(CFLAGS) -o $@

%.i : %.c
	$(CC) $(INCLUDES) $(CPPFLAGS) $< $(CFLAGS) -o $@

%.asm : %.c
	$(CC) $(INCLUDES) -S $< $(CFLAGS) -o $@

$(TARGET).asm : $(TARGET).out
	$(OBJDUMP) -d $(TARGET).out >> $@

%.d : %.c
	$(CC) $(INCLUDES) -M $< $(CFLAGS) -o $@

.PHONY: compile-all
compile-all: $(OBJS)
	$(CC) $(INCLUDES) $(OBJS) -c $(CFLAGS) -o $(TARGET).o

.PHONY: build
build: $(TARGET).out  $(TARGET).asm


$(TARGET).out: $(OBJS) $(DEPS) 
	$(CC) $(CFLAGS) $(INCLUDES) $(LDFLAGS) -o $@ $(OBJS)
	$(SIZE) -Atd $(TARGET).out
	$(SIZE) $(TARGET).out
.PHONY: clean
clean:
	rm -f $(TARGET).asm $(FILES) $(TARGET).out $(TARGET).map $(PREPRO) $(ASMS) $(DEPS) $(OBJS)
