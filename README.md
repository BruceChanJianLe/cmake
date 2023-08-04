# CMAKE

## Table of Contents
1. [Installation on Linux](#Installation-on-Linux)
1. [Details for CMakeLists.txt](#Details-for-CMakeLists-txt)
1. [Finding packages](#Finding-packages)
1. [Adding Warnings in CMake](#Adding-Warnings-in-CMake)
1. [Adding Compile Definitions](#Adding-Compile-Definitions)
1. [Display All Available CMake Variables](#Display-All-Available-CMake-Variables)
1. [Adding Compile Flag](#Adding-Compile-Flag)
1. [Make_Install](#Make-Install)

## Installation on Linux

For Linux users, the default Linux are quite old on their repo. Please follow these instructions below to install the latest _cmake_ on your Linux.

**Step 1: Obtain the latest cmake sh file**

Go to `https://cmake.org/` [downlaod_link](https://cmake.org/download/) and download the Linux version shell script. The `*.sh` file. After that you may rename it as `cmake.sh`.

**Step 2: Installation**

Note that Linux tells us that packages that are not managed by the system should be installed in the `/usr/local` directory.

Therefore,

`sudo bash cmake.sh --prefix=/usr/local --exclude-subdir`

Note that `--exclude-subdir` command will put `bin`, `man`, `doc` and `share` in the `/usr/local` as we want.

**Others**

If you search for cmake on the Ubuntu repo `apt-cache search cmake` you will find this:

```
cmake/xenial-updates,now 3.5.1-1ubuntu3 amd64 [installed]
  cross-platform, open-source make system
```
Version 3.5.1 is quite old.

If cmake has conflict on your system, causing Ubuntu not to know which cmake to use. Please follow the instructions below to setup your cmake properly.

```bash
# Do this only after installation
# Remove any cmake set before
sudo update-alternatives --remove-all cmake

# Set the current cmake as the default cmake
sudo update-alternatives --install /usr/bin/cmake cmake /usr/local/bin/cmake 80
# If you faced with this error
# update-alternatives: using /usr/local/bin/cmake to provide /usr/bin/cmake (cmake) in auto mode
# update-alternatives: warning: not replacing /usr/bin/cmake with a link
# reinstall cmake
# sudo apt-get --reinstall install cmake

# Choose you cmake, if you have already remove all just now you will only have one cmake
sudo update-alternatives --config cmake

# Check your cmake version
cmake --version
```

## Details for CMakeLists.txt
```cmake
# Set cmake minimum require
cmake_minimum_required(VERSION 3.17)

# To obtain the name of the main directory as the project name
get_filename_component(PROJECT_ID ${CMAKE_CURRENT_SOURCE_DIR} NAME)

# If the name has spaces replace them with "_"
string(REPLACE " " "_" PROJECT_ID ${PROJECT_ID})

# Set the project as project id
project(${PROJECT_ID})

# Include ncurses library
find_package(Curses REQUIRED)
include_directories(${CURSES_INCLUDE_DIR})

# Make an executable (from main.cpp)
add_executable(${PROJECT_NAME} main.cpp)

# Link the found library
target_link_libraries(${PROJECT_NAME} ${CURSES_LIBRARIES})
```

### Finding packages

Run `cmake --help-module-list` to list all the packages that it can find.

### Adding Warnings in CMake

Note to add the `target_compile_options` after the target is build. e.g. after `add_executable`.   

**Method 1:**  
```cmake
if(MSVC)
  target_compile_options(${TARGET_NAME} PRIVATE /W4 /WX)
else()
  target_compile_options(${TARGET_NAME} PRIVATE -Wall -Wextra -pedantic -Werror)
endif()
```

**Method 2:**  
```cmake
target_compile_options(${TARGET_NAME} PRIVATE
  $<$<CXX_COMPILER_ID:MSVC>:/W4 /WX>
  $<$<NOT:$<CXX_COMPILER_ID:MSVC>>:-Wall -Wextra -pedantic -Werror>
)
```

**Method 3:**
```cmake
target_compile_options(line_node
  PRIVATE
    $<$<CXX_COMPILER_ID:Gnu>:
    # g++ warning flags
    -Wall
    -Wextra
    -Wpedantic
    -Werror
    >

    $<$<CXX_COMPILER_ID:Clang>:
    # clang warning flags
    -Wall
    -Wextra
    -Wpedantic
    -Werror
    >

    $<$<CXX_COMPILER_ID:MSVC>:
      # MSVC warning flags
      /W4
      /WX
    >
)
```

### Adding Compile Definitions

```cmake
# Taken from https://github.com/BruceChanJianLe/ros-class-node/blob/master/CMakeLists.txt
# node_class_debug executable
add_executable(node_class_debug
               src/main.cpp
               src/node_class.cpp
               )

target_compile_definitions(node_class_debug PRIVATE DEBUG_)

target_link_libraries(node_class_debug ${catkin_LIBRARIES} ${CURSES_LIBRARIES})
```

### Display All Available CMake Variables

```cmake
    message(STATUS "print_all_variables------------------------------------------{")
    get_cmake_property(_variableNames VARIABLES)
    foreach (_variableName ${_variableNames})
        message(STATUS "${_variableName}=${${_variableName}}")
    endforeach()
    message(STATUS "print_all_variables------------------------------------------}")
```


### Adding Compile Flag

Add compile flag for single source file.
```cmake
add_executable(MyProgram myprogram.cpp)

# Add the -std=c++11 flag as an example
set_source_files_properties( myprogram.cpp PROPERTIES COMPILE_FLAGS "-std=c++11" )
target_link_libraries(MyProgram MyLibraries)
```

Add compile flag for multiple source files.
```cmake
add_executable(MyProgram myprogram.cpp)
# Add the -std=c++11 flag as an example
target_link_libraries(MyProgram MyLibraries)
set_target_properties( MyProgram PROPERTIES COMPILE_FLAGS "-std=c++11" )
```

Update: To remove a single property, you can first get all the properties and manually remove the offending flag from the list. [link](https://cmake.org/cmake/help/v2.8.12/cmake.html#command%3aget_source_file_property)
```cmake
get_source_file_property( MYPROPS myprogram.cpp COMPILE_FLAGS )
STRING( REPLACE "/MP1" "" MYPROPS ${MYPROPS} )
set_source_files_properties( myprogram.cpp COMPILE_FLAGS ${MYPROPS})
```

## References
 - Installation (from cmake good) [link](https://www.youtube.com/watch?v=_yFPO1ofyF0&list=PLK6MXr8gasrGmIiSuVQXpfFuE1uPT615s)
 - CMake features (from CMake for dummies) [link1](https://cmake.org/cmake/help/v3.1/prop_gbl/CMAKE_CXX_KNOWN_FEATURES.html#prop_gbl:CMAKE_CXX_KNOWN_FEATURES) [link2](https://www.youtube.com/watch?v=7W4Q-XLnMaA)  
 - Adding Warnings in CMake [link1](https://cmake.org/cmake/help/latest/command/add_compile_options.html) [link2](https://stackoverflow.com/questions/2368811/how-to-set-warning-level-in-cmake/3818084) [Method3link1](https://github.com/ricab/scope_guard/issues/4) [Method3link2](https://gitlab.kitware.com/cmake/cmake/-/issues/19084) 
 - All Available CMake Variables [link1](https://stackoverflow.com/questions/9298278/cmake-print-out-all-accessible-variables-in-a-script/9328525#9328525) [link2](https://stackoverflow.com/questions/31343813/displaying-cmake-variables)
 - Adding compile flag [link_stackoverflow](https://stackoverflow.com/questions/24238937/how-to-change-a-compiler-flag-for-just-one-executable-in-cmake)
 - Better CMake [link](https://www.youtube.com/playlist?list=PL8i3OhJb4FNV10aIZ8oF0AA46HgA2ed8g)
 - Talk on CMake [link](https://www.youtube.com/watch?v=bsXLMQ6WgIk&ab_channel=CppNow)

### Make Install

As we all know that libraries that are not managed by apt should go to `/usr/local` therefore let's do it with `make install`

```bash
# Preferrably using the DESTDIR method
make DESTDIR="/usr/local" install
# Alternatively, you can export DESTDIR, however, you will need to remember to clean it later.
# Else it will cause undesirable behaviour
```

Note: You can also use the configure file (as suggested in the ref link) if there is one, but not all has one.
Ref: https://stackoverflow.com/questions/3239343/make-install-but-not-to-default-directories
