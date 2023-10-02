# Magick++ CMake #

Add cmake compile/install to Magick++ project.


## Usage ##

0. Download original Magick++ source code from http://www.graphicsmagick.org/. 
  Magick++ source code is part of the whole graphicsmagick source.

1. Download this repo to a folder named Magick++

2. Overwrite (merge) this Magick++ folder to the sub folder of Magick++ in graphicsmagick source.

3. run cmake commands in the sub folder of Magick++

### Result ###

1. cmake compiling of Magick++ as a dll project.

2. cmake install into system

3. Use the this project by other cmake project
```cmake
find_package(Magick++ REQUIRED)
target_link_libraries(dexe Magick++::Magick++)
```

