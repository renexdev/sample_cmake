# sample_cmake
Taken from Medium post https://medium.com/heuristics/c-application-development-part-3-cmakelists-txt-from-scratch-7678253e5e24

Sample project to understand building process of a c++ project with make and cmake. Based on tutorial repo. 

# Building with make command tools

### Structure of a make file
A **Makefile** will have the following structe:
```
target_name_1:
	g++ a.cpp b.cpp c.cpp -o output
target_name_2:
	gcc a.cpp b.cpp -o output
```
To execute a particular target:
> make target_name

A rather complex example of make ( where one target can call the other )
> target: dependency1 dependency 2  
command_depending_on_the_dependencies
```
CC=g++

CFLAG=-c -Wall

all: hello

hello: main.o function1.o function2.o
	$(CC) main.o function1.o function2.o -o output

main.o: main.cpp
	$(CC) $(CFLAG) main.cpp

function1.o: function1.cpp
	$(CC) $(CFLAG) function1.cpp

function2.o: function2.cpp
	$(CC) $(CFLAG) function2.cpp

clean:
	rm -rf *.o output
```

**Note:** Only files that have been changed after last compilation will be recompiled.

**Note:** Writing **Makefile** is a tedious process, hence **CMake** is a utility to automate that task.

## Manual compilation of the project

Create a building folder e.g. bin-manual

Then, we will need to convert the *.o* file(s) into *.a* file, we use the following command:
```
g++ -c $REPO_DIR/libs/Randomize/src/randomize_util.cpp -I ./include

g++ -c $REPO_DIR/libs/Logger/src/logger_util.cpp -I ./include

```

Once this has been done, we use the **ar** tool to generate the archieve in the $REPO_DIR/libs/Randomize and $REPO_DIR/libs/Logger:
```
ar rsv librandomize.a randomize_util.o

ar rsv liblogger.a logger_util.o

```

Then g++ compiler can be manually invoked to compile the project and to generate the binary type the following in the $REPO_DIR/bin-manual:
```
g++ ../src/game_engine.cpp ../src/game_interface.cpp ../src/main.cpp -I ../src -I ../include -I ../libs/Logger/include -I ../libs/Randomize/include -L ../libs/Logger -l logger -L ../libs/Randomize -l randomize
```
Here **-I** flag is used to tell the compiler which directories contain the header files.

**-L** flag are used to tell the compiler which directories contain the static library files (.a). 

**-l** flag is used to specify the lib's name to the complier (built with ar command). The compiler will look for *liblogger.a* or *loggerlib.a* & *librandomize.a* or *randomizelib.a* .

You will find the executale a.out in $REPO_DIR/bin-manual

# Building with cmake (https://cmake.org/)

## What is CMake?
It is a build tool that basically takes in the name of the **source file(s)** and **various parameters** mentioned in a file called **CMakeLists.txt** and spits out a **make** file that in turn could be used to create the final executable binary.

### Initial file structure
* CMakeLists.txt (only file required by cmake)
* main.cpp (source file)

### Build process
The usual trend is to create a build directory so as to hold the output of cmake in a seperate directory. Then we point cmake to the location where CMakeLists.txt resides. Then CMake shall spit out several files out of which **make** is the one we are concerned with. Once cmake has generated the files, there is no need to run cmake again in case any changes are made to CMakeLists.txt; make will know about these changes automatically.
1. mkdir bin
2. cd bin
3. cmake .. //or ccmake .. for a ui version
4. make ( or **make VERBOSE=1** to see under the hood )
  
> *This shall generate the binary executable in the build directory that can be run to verify that the make file indeed compiles the project*  

### Final file structure
* CMakeLists.txt (only file required by cmake)
* main.cpp (source file)
* build/
	* CMakeFiles/
	* cmake_install.cmake
	* CMakeCache.txt
	* Makefile
	* output

