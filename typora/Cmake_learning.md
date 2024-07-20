> ***sudo apt install Cmake***

### Cmake

***[CMake Tutorial — Mastering CMake](https://cmake.org/cmake/help/book/mastering-cmake/cmake/Help/guide/tutorial/index.html#introduction)***



### `CMakeLists.txt`

`cmake_minimum_required()`: Run Cmake function with a compatible version od Cmake.

> - ```cmake
>   cmake_minimum_required(VERSION <min>[...<policy_max>] [FATAL_ERROR])
>   ```

`project()`: start a project and set the name, other project level info. 

`add_executable()`: create an executable file using the specified source code files.



#### step1:  basic starting point

most simple CMakeLists.txt:

```cmake
cmake_minimum_required(VERSION 3.10)

#set the project name
project(Tutorial)  #you can also use PROJECT, cmake is case insensitive

#add the executable
add_executable(Tutorial tutorial.cxx)
```



#### adding a version number and configured header file

set version number:

```cmake
project(tutorial VERSION 1.0)
```

configure a header file to pass version number to source code:

```cmake
configure_file(TutorialConfig.h.in TutorialConfig.h)
```

the config file will be written into the binary tree. add that directory to the list of paths to search for include  files. add the following:

```cmake
target_include_directories(Tutorial PUBLIC 
							"${PROJECT_BINARY_DIR}"
							)
```

