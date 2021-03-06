# Makefile :)
OS_UNAME =  $(shell uname -s)

ifeq ($(OS_UNAME), Linux)
OS=linux
LIBRARYEXT=.so
else
OS=windows
LIBRARYEXT=.dll
endif

ifeq ($(OS_UNAME), FreeBSD)
OS=freebsd
LIBRARYEXT=.so
endif

SRCDIR=src

NAME=helloworldhook

DEVSTAGE = beta

CC?=gcc
CXX?=g++

ifeq ($(CLANG), 1)
CC=clang
CXX=clang++
endif

OPT_CFLAGS = -O2 -flto -funroll-loops -fno-omit-frame-pointer -fno-stack-protector -fPIC -Wall
#-fsanitize=undefined -fsanitize=address

BASE_CFLAGS = -D__USE_GNU -std=gnu11 -DVERSION=\"$(VERSION)\" -D_VKBOT_MODULE -I../../src/

ARCH=$(shell uname -m)

COMMIT := $(firstword $(shell git rev-parse --short=6 HEAD) unknown)

ifeq ($(ARCH), i686)
ARCH_CFLAGS +=-msse3 -march=i686 -mtune=generic
else ifeq ($(ARCH), x86_64)
ARCH_CFLAGS +=-msse3 -mtune=generic
ifeq ($(TARGET_ARCH), i686)
ARCH = i686
ARCH_CFLAGS +=-m32 -msse3 -march=i686 -mtune=generic
endif
else ifeq ($(ARCH), aarch64)
ARCH_CFLAGS +=
else ifeq (, $(findstring arm,$(ARCH_UNAME)))
ARCH_CFLAGS +=
endif

ifeq ($(DEBUG),1)
BUILD_TYPE = debug
BUILD_TYPE_CFLAGS = -g -DDEBUG
else
BUILD_TYPE = release
BUILD_TYPE_CFLAGS = -DNDEBUG
endif

VERSION := $(COMMIT)-$(DEVSTAGE)-$(BUILD_TYPE)


OBJDIR=$(BUILD_TYPE).$(OS).$(ARCH)

CFLAGS = $(BUILD_TYPE_CFLAGS) $(BASE_CFLAGS) $(OPT_CFLAGS) $(ARCH_CFLAGS)

INCLUDE=-I. -I$(SRCDIR) -I../../cjson/

LDFLAGS=-L. -shared -lm -lcjson -L../../cjson/

DO_CC=$(CC) $(CFLAGS) $(INCLUDE) -o $@ -c $<

DO_CXX=$(CXX) $(CFLAGS) $(INCLUDE) -o $@ -c $<

$(OBJDIR)/%.o: $(SRCDIR)/%.c
	$(DO_CC)

SRC = $(wildcard src/*.c)

OBJ := $(SRC:$(SRCDIR)/%.c=$(OBJDIR)/%.o)

$(NAME)_$(ARCH)$(LIBRARYEXT) : neat depend $(OBJ)
	$(CC) $(CFLAGS) $(OBJ) $(LDFLAGS) -o $(OBJDIR)/$@

neat:
	@mkdir -p $(OBJDIR)

$(OBJDIR): neat

clean: depend
	-rm -f $(OBJ)
	-rm -f $(OBJDIR)/$(NAME)_$(ARCH)$(LIBRARYEXT)
	-rm -f $(OBJDIR)/Rules.depend

depend: $(OBJDIR)/Rules.depend

$(OBJDIR)/Rules.depend: $(SRCFILES) $(OBJDIR)
	$(CC) -MM $(INCLUDE) $(SRC) $(CFLAGS) | sed "s;\(^[^         ]*\):\(.*\);$(OBJDIR)/\1:\2;" > $@

include $(OBJDIR)/Rules.depend
