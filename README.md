# CMAKE

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

```cmake
if(MSVC)
  target_compile_options(${TARGET_NAME} PRIVATE /W4 /WX)
else()
  target_compile_options(${TARGET_NAME} PRIVATE -Wall -Wextra -pedantic -Werror)
endif()

# OR

target_compile_options(${TARGET_NAME} PRIVATE
  $<$<CXX_COMPILER_ID:MSVC>:/W4 /WX>
  $<$<NOT:$<CXX_COMPILER_ID:MSVC>>:-Wall -Wextra -pedantic -Werror>
)
```

## References
 - Installation (from cmake good) [link](https://www.youtube.com/watch?v=_yFPO1ofyF0&list=PLK6MXr8gasrGmIiSuVQXpfFuE1uPT615s)
 - CMake features (from CMake for dummies) [link1](https://cmake.org/cmake/help/v3.1/prop_gbl/CMAKE_CXX_KNOWN_FEATURES.html#prop_gbl:CMAKE_CXX_KNOWN_FEATURES) [link2](https://www.youtube.com/watch?v=7W4Q-XLnMaA)  
- Adding Warnings in CMake [link1](https://cmake.org/cmake/help/latest/command/add_compile_options.html) [link2](https://stackoverflow.com/questions/2368811/how-to-set-warning-level-in-cmake/3818084)