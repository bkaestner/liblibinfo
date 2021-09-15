# Lib²Info
Enable self-describing libraries with a single command.

## What is Lib²Info?
Lib²Info (or `liblibinfo`) is a small addition to [CMake] to turn your shared libraries into stand-alone executables that still can be dynamically linked. It's small and can be copied into an existing project.

## Dependencies
In order to use Lib²Info, you need to use CMake. Also, only GCC is currently supported. While it might work with Clang, that hasn't been tested yet.

# Usage
Add `liblibinfo.cmake` and `liblibinfo.c.in` to your project, `include(...)` the `.cmake` file and then use the `add_libinfo(TARGET)` function on a *shared* library:

```cmake
cmake_minimum_required(VERSION 3.10)
project(libgen
  VERSION 1.0.0
  LANGUAGES C)

include("liblibinfo.cmake")

add_library(foo SHARED foo.c)
add_libinfo(foo)
```

[CMake]: https://cmake.org/
[ELF]: https://en.wikipedia.org/wiki/Executable_and_Linkable_Format "Executable and Linkable Format"

# How does it work?
Shared libraries on Linux systems are [ELF][ELF] files. With an appropriate entry in the `.interp` section and a proper entry point it's possible to run them. The `add_libinfo` call prepares an additional source file with a banner, entry point implementation and interpreter section. It also adjust the linker flags to configure the entry point.