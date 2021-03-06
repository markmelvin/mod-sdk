
CXX ?= g++

CXXFLAGS += -Wall -Wextra -Wshadow -std=gnu++0x -fPIC -O3 -Wno-deprecated-declarations

ifneq ($(shell uname -s),Darwin)
LDFLAGS += -Wl,-O1 -Wl,--no-undefined -Wl,--strip-all
endif

ifeq ($(shell pkg-config --atleast-version=0.22.0 lilv-0 && echo true), true)
CXXFLAGS += -DHAVE_NEW_LILV
endif

all: build

build: libmod_utils.so

clean:
	rm -f *.so *.o

libmod_utils.so: utils_lilv.cpp.o
	$(CXX) $^ $(LDFLAGS) -shared $(shell pkg-config --libs lilv-0) -o $@

utils_lilv.cpp.o: utils_lilv.cpp utils.h
	$(CXX) $< $(CXXFLAGS) $(shell pkg-config --cflags lilv-0) -c -o $@

test.cpp.o: test.cpp utils.h
	$(CXX) $< $(CXXFLAGS) -c -o $@

test: utils_lilv.cpp.o test.cpp.o
	$(CXX) $^ $(LDFLAGS) $(shell pkg-config --libs lilv-0) -o $@

test-run: test
	valgrind --leak-check=full --show-leak-kinds=all --partial-loads-ok=yes ./test
