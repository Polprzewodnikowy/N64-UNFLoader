APP=UNFLoader
OS_NAME := $(shell uname -s)

CODEFILES = main.cpp helper.cpp term.cpp debug.cpp gdbstub.cpp
CODEOBJECTS =	$(CODEFILES:.cpp=.o)
LIBFILES = Include/lodepng.cpp
LIBOBJECTS =	$(LIBFILES:.cpp=.o)

CARTLIBNAME 	= flashcart
CARTLIBFILES	= device.cpp \
            	  device_64drive.cpp \
                  device_everdrive.cpp \
                  device_sc64.cpp
CARTLIBOBJECTS 	= $(CARTLIBFILES:.cpp=.o)
CARTLIBNAME_STATIC	= $(CARTLIBNAME).a
CARTLIBNAME_SHARED	= $(CARTLIBNAME).so

CXX = g++

DEPENDENCIESLIB := -lftd2xx
ifeq ($(OS_NAME),Darwin)
	DEPENDENCIES := -lncurses $(DEPENDENCIESLIB) -lpthread
else
	DEPENDENCIES := -lncursesw $(DEPENDENCIESLIB) -lpthread -lrt 
endif

LINKER_OPTIONS := -Wl,-rpath /usr/local/lib
CFLAGS = -D LINUX -D_XOPEN_SOURCE_EXTENDED -Wall -Wno-unknown-pragmas -O3 -std=c++11

default: $(APP)

$(APP): static $(CODEOBJECTS) $(LIBOBJECTS) 
	@echo "Linking $@"
	@$(CXX) $(CFLAGS) -o $(APP) $(CODEOBJECTS) $(CARTLIBOBJECTS) $(LIBOBJECTS) $(CARTLIBNAME_STATIC) $(DEPENDENCIES) $(LINKER_OPTIONS) -L/usr/local/lib

%.o: %.cpp
	@echo "Compiling $<"
	@$(CXX) -c $(CFLAGS) -o $@ $<

static: $(CARTLIBOBJECTS) 
	@echo "Creating static library $(CARTLIBNAME_STATIC)"
	ar rcs $(CARTLIBNAME_STATIC) $^

shared: $(CARTLIBOBJECTS)
	@echo "Creating shared library $(CARTLIBNAME_SHARED)"
	@$(CXX) $(CFLAGS) $(CARTLIBFILES) -o $(CARTLIBNAME_SHARED) $(DEPENDENCIESLIB) $(LINKER_OPTIONS) -L/usr/local/lib -fPIC -shared

clean:
	@echo "Cleaning built artifacts.."
	@rm -f $(APP) $(CODEOBJECTS) $(CARTLIBOBJECTS) $(LIBOBJECTS) $(CARTLIBNAME_STATIC) $(CARTLIBNAME_SHARED)

install: $(APP)
	@echo "Installing $(APP) to /usr/local/bin"
	@mkdir -p /usr/local/bin
	@cp $(APP) /usr/local/bin/$(APP)

uninstall: $(APP)
	@echo "Removing $(APP) from /usr/local/bin"
	@rm -f /usr/local/bin/$(APP)