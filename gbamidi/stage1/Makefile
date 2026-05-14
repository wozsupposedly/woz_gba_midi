PREFIX ?= /Users/woz/Library/Arduino15/packages/Seeeduino/tools/arm-none-eabi-gcc/9-2019q4/bin/arm-none-eabi-
AS := $(PREFIX)gcc
OBJCOPY := $(PREFIX)objcopy

all: loader.gba

loader.elf: loader.s
	$(AS) -Wall -nostdlib -Ttext=0x02000000 $< -o $@

loader.gba: loader.elf
	$(OBJCOPY) -O binary $< $@

clean:
	rm -f loader.elf loader.gba

.PHONY: all clean
