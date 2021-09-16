cmake_minimum_required(VERSION 3.5)

project(liblibinfo-example
  VERSION 1.0.0
  LANGUAGES C)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -Wextra -Werror")

enable_testing()

# Include the liblibinfo file
include("../liblibinfo.cmake")

# Use your usual add_library call
add_library(foo SHARED foo.c)

# Just call add_libinfo on the target:
add_libinfo(foo)
# We can run the library!
add_test(NAME run-foo-library COMMAND "$<TARGET_FILE:foo>")

# If we don't use add_libinfo, we end up with a regular shared library:
add_library(foo_base SHARED foo.c)
add_test(NAME "foo-without-libinfo" COMMAND sh -c "! $<TARGET_FILE:foo_base>")

# You can also use add_libinfo on libraries that use lib²infoed libraries:
add_library(bar SHARED bar.c)
target_link_libraries(bar PUBLIC foo) # a keyword MUST be used
add_libinfo(bar)
add_test(NAME run-bar-library COMMAND "$<TARGET_FILE:bar>")

# You can also use add_libinfo only on the outer library
add_library(bar-exe SHARED bar.c)
target_link_libraries(bar-exe PRIVATE foo_base)
add_libinfo(bar-exe)
add_test(NAME run-other-bar-library COMMAND "$<TARGET_FILE:bar-exe>")

# All other combinations don't yield an executable.
add_library(bar_base SHARED bar.c)
target_link_libraries(bar_base foo)
add_test(NAME bar-without-libinfo COMMAND sh -c "! $<TARGET_FILE:bar_base>")

add_library(bar_base_base SHARED bar.c)
target_link_libraries(bar_base_base foo_base)
add_test(NAME bar-and-foo-without-libinfo COMMAND sh -c "! $<TARGET_FILE:bar_base_base>")

# You can link against foo or bar as usual.
add_executable(example main.c)
target_link_libraries(example foo)
add_test(NAME run-main COMMAND example)

add_executable(example-bar main.c)
target_link_libraries(example-bar bar)
add_test(NAME run-main-bar COMMAND example)
