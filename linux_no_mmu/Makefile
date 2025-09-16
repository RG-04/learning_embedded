# Toolchain and paths
BUILDROOT := ../utils/buildroot-2025.08
CROSS_COMPILE := $(BUILDROOT)/output/host/bin/riscv64-buildroot-linux-uclibc-
CC := $(CROSS_COMPILE)gcc

# Compiler/Linker flags
CFLAGS := -Wall -fPIC -static
LDFLAGS := -Wl,-elf2flt=-r

# Sources and target
SRC := init.c
TARGET := init

all: $(TARGET)

$(TARGET): $(SRC)
	$(CC) $(CFLAGS) $(LDFLAGS) -o $(TARGET) $(SRC)

clean:
	rm -f $(TARGET) $(TARGET).gdb
