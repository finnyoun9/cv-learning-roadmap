TC = arm-none-eabi
CC = $(TC)-gcc
AS = $(TC)-as
LD = $(TC)-gcc
OBJCOPY = $(TC)-objcopy
SIZE = $(TC)-size

TC_PATH = /Users/finn/.local/arm-gnu-toolchain-14.2.rel1-darwin-arm64-arm-none-eabi/bin
CC := $(TC_PATH)/$(CC)
AS := $(TC_PATH)/$(AS)
LD := $(TC_PATH)/$(LD)
OBJCOPY := $(TC_PATH)/$(OBJCOPY)
SIZE := $(TC_PATH)/$(SIZE)

TARGET = balance-car-stage08

CFLAGS  = -mcpu=cortex-m3 -mthumb
CFLAGS += -DSTM32F10X_MD -DUSE_STDPERIPH_DRIVER -DHSE_VALUE=8000000
CFLAGS += -Wall -Os -g
CFLAGS += -IUser -IStart -ILibrary -ISystem -IHardware

LDFLAGS  = -mcpu=cortex-m3 -mthumb
LDFLAGS += -T STM32F103C8T6.ld
LDFLAGS += -Wl,--gc-sections -Wl,-Map=$(TARGET).map
LDFLAGS += --specs=nosys.specs --specs=nano.specs
LDLIBS   = -lm

# All source files (exclude core_cm3.c — Keil-only inline asm)
C_SRCS  = $(filter-out Start/core_cm3.c, $(wildcard Start/*.c))
C_SRCS += $(wildcard Library/*.c)
C_SRCS += $(wildcard System/*.c)
C_SRCS += $(wildcard Hardware/*.c)
C_SRCS += $(wildcard User/*.c)

ASM_SRCS = startup_stm32f10x_md_gcc.S

OBJS  = $(C_SRCS:.c=.o)
OBJS += $(ASM_SRCS:.S=.o)

.PHONY: all clean flash size

all: $(TARGET).elf $(TARGET).bin $(TARGET).hex
	@$(SIZE) $(TARGET).elf

%.o: %.c
	$(CC) $(CFLAGS) -c $< -o $@

%.o: %.S
	$(CC) $(CFLAGS) -c $< -o $@

$(TARGET).elf: $(OBJS)
	$(LD) $(LDFLAGS) $(OBJS) $(LDLIBS) -o $@

$(TARGET).bin: $(TARGET).elf
	$(OBJCOPY) -O binary $< $@

$(TARGET).hex: $(TARGET).elf
	$(OBJCOPY) -O ihex $< $@

size: $(TARGET).elf
	$(SIZE) $<

flash: $(TARGET).bin
	st-flash write $< 0x08000000

clean:
	rm -f $(OBJS) $(TARGET).elf $(TARGET).bin $(TARGET).hex $(TARGET).map
