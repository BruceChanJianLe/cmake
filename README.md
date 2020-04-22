# CMAKE

## Installation on Linux

For Linux users, the default Linux are quite old on their repo. Please follow these instructions below to install the latest _cmake_ on your Linux.

If you search for cmake on the Ubuntu repo `apt-cache search cmake` you will find this:

```
cmake/xenial-updates,now 3.5.1-1ubuntu3 amd64 [installed]
  cross-platform, open-source make system
```
Version 3.5.1 is quite old.

- Step 1: Obtain the latest cmake sh file

Go to `https://cmake.org/` and download the Linux version shell script. The `*.sh` file. After that you may rename it as `cmake.sh`.

- Step 2: Installation

Note that Linux tells us that packages that are not managed by the system should be installed in the `/usr/local` directory.

Therefore,

`sudo bash cmake.sh --prefix=/usr/local --exclude-subdir`

Note that `--exclude-subdir` command will put `bin`, `man`, `doc` and `share` in the `/usr/local` as we want.

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

## References
 - Installation (from cmake good) [link](https://www.youtube.com/watch?v=_yFPO1ofyF0&list=PLK6MXr8gasrGmIiSuVQXpfFuE1uPT615s)