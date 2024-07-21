### cmake learn by doing

##### source-link: [CMake 入门实战 | HaHack](https://www.hahack.com/codes/cmake)



#### introduction

cmake is a system independent automatic construct tool.

use cmake generating Makefile in linux:

1. write `CMakeLists.txt`
2. `cmake PATH` or `ccmake PATH`, the differences is that ccmake provide an interactive UI.
3. use `make PATH` to compile.



#### single source file

```cmake
cmake_minimum_required (VERSION 3.10)

project(hello VERSION 1.0)

add_executable(hello main.cpp)
```

than:

```bash
make

./hello
```



#### mutiple source files

file tree:

```
./t1
	|
	+--- main.cpp
	|
	+--- math.cpp
	|
	+--- math.h
```

then change `CMakeLists.txt`:

```cmake
cmake_minimum_required (VERSION 3.10)

project(hello VERSION 1.0)

add_executable(hello main.cpp math.cpp)
```

if too much source files, use

```cmake
aux_source_directory(<dir> <variable>)# clollect address of all source files and save the infomation to variable, without filter, which means these files could include suffixless files like makefile and CMakeLists.txt itself.
```

CMakeLists.txt:

```cmake
cmake_minimum_required (VERSION 3.10)

project(hello VERSION 1.0)
aux_source_directory(. DIR_SRCS)

add_executable(hello main.cpp math.cpp)
```

to directly collect files with appointed suffix use file(GLOB) or file(GLOB_RECURSE):
```cmake
file(GLOB <variable> <globbing-expression>) #search files in current dir that matches globbing-expression.
file(GLOB_RECURSE <variable> <globbing-expression>) #also search child-dir resursively.
```



#### multiple directories and multiple source files

```
./t1
	|
	+--- main.cpp
	|
	+-- /math
        |
        +--- math.cpp
        |
        +--- math.h
```

in this case, we need to write CMakeLists.txt for both root dir and math dir:

root dir:
```cmake
cmake_minimum_required (VERSION 3.10)

project(hello VERSION 1.0)

aux_source_directory(. DIR_SRCS)

add_executable(hello main.cpp math.cpp)

add_subdirectory(math) #add sublibraries so that CMakeLists.txt in sublibrarities can also be processed

target_link_libraries(hello MathFunctions)#tell cmake that main should have to link a library named MathFunctions
```

sub dir:

```cmake
aux_source_directory(. DIR_MATH_SRCS)

# compile the source files in the src directory into a static link library
add_library(MathFunctions ${DIR_MATH_SRCS})
```



####  customize compilation options

`configure-file`:

```
configure_file(
    "${SOURCE_FILE}"  # source files address
    "${DESTINATION_FILE}"  # target files address
    [COPYONLY]  # selectable, if selected, cmake simply copy files, without variable substitution
)
```

`option`:

```
option(<OPTION_NAME> "<help description>" <VALUE>)
```



root CMakeList.txt:

```cmake
cmake_minimum_required (VERSION 3.10)

project(hello VERSION 1.0)

# add a configure header, to process cmake settings toward source code, so that we can 
configure_file(
    "${PROJECT_SOURCE_DIR}/config.h.in" #${PROJECT_SOURCE_DIR} is an auto variable regarding source address.
    "${PROJECT_BINARY_DIR}/config.h"
)

#define weather to use our self-define mathfunction library
option(
    USE_MYMATH
    "Use provided math implementtation" ON
)

# weather to add mathfunctions library
if (USE_MYMATH)
    include_directories("${PROJECT_SOURCE_DIR}/math")
    add_subdirectory(math)
    set(EXTRA_LIBS ${EXTRA_LIBS} MathFunctions)
endif(USE_MYMATH)

aux_source_directory(. DIR_SRCS)

add_executable(hello main.cpp)

target_link_libraries(hello ${EXTRA_LIBS})#tell cmake that main should have to link a library named MathFunctions
```

in `main.cpp`:

```cpp
// main.cpp

#include<iostream>
#include"config.h" //auto-generate by cmake from config.h.in, if not include, `#ifdef` always false

#ifdef USE_MYMATH
    #include "math/math.h"
#else 
    #include <math.h>
#endif

int main(){
    std::cout << "hello world" << std::endl;
    
#ifdef USE_MYMATH
    math();
#else 
    std::cout << "im standard math lib" << std::endl;
#endif

    return 0;
}
```

in `config.h.in`:

```
#cmakedefine USE_MYMATH
```

then on bash:

`cmake .` or `cmake -USE_MYMATH OFF` default value is ON.



#### install and test

install() in cmake use to appoint files, libraries and executable files that should be copied to the system installation directory during the build process. 

add the following to math/CMakeLists.txt:

```cmake
#specify math library's installing address
install (TARGETS math DESTINATION bin)
install (FILES math.h DESTINATION include)
```

add the following to the ending of root CMakeLists.txt:

```cmake
install (TARGETS hello DESTINATION bin)
install (FILES "${PROJECT_BINARY_DIR}/config.h" DESTINATION include)
```

then hello and libmath.o that generated will be copied to /usr/local/bin, and math.h and config.h will be copied to /usr/local/include. /usr/local is the default installing root address,we can modify variable CMAKE_INSTALL_PREFIX to appoint the target address.

---

cmake provide a tool named CTest ,  if we want our project has a test function, what we need to do is to  add `add_test` in our `CMakeList.txt`.

```cmake
add_test(NAME <test_name>
          COMMAND <command> [<arg1> <arg2> ...]
          [CONFIGURATIONS <config1> [<config2>...]]
          [WORKING_DIRECTORY <dir>]
          [COMPILE_DEFINITIONS <def1> [<def2>...]])
```

in CMakeLists.txt:

```cmake
enable_testing()

# test weather testing program can run successfully
add_test (test_run hello) #if hello need parameter, use add_test (test_run hello <pm1> ...)

#test weather help-info can prompt normally
add_test (test_usage hello)
set_tests_properties (test_usage
	PROPERTIES PASS_REGULAR_EXPRESSION "Usage: .* base exponent") #`PASS_REGULAR_EXPRESSION`: check if the output contians the string "Usage: .* base exponent"
	
#add_test (test_case_1 hello pm1 pm2)
#set_tests_properties (test_case_1
#	PROPERTIES PASS_REGULAR_EXPRESSION "xxx")
#
#add_test
#......
```

auto-test: **write macro**

:star:example:star:

```cmake
macro (do_test arg1 arg2 result)#numbers of args depend on your function.
  add_test (test_${arg1}_${arg2} Demo ${arg1} ${arg2})
  set_tests_properties (test_${arg1}_${arg2}
    PROPERTIES PASS_REGULAR_EXPRESSION ${result})
endmacro (do_test)

#do_test（arg1 arg2 "part of target")
```



#### support gdb

active `-g` option in debug mode.

```cmake
set(CMAKE_BUILD_TYPE "Debug")
set(CMAKE_CXX_FLAGS_DEBUG "$ENV{CXXFLAGS} -00 -Wall -g -ggdb")
set(CMAKE_CXXX_FLAGS_RELEASE "$ENV{CXXFLAGS} -03 -Wall")
```

