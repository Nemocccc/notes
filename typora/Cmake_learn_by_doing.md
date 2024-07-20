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

