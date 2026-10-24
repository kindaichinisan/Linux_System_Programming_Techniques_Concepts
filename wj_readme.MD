# Linux System Programming Techniques & Concepts

# Section 1: Introduction
## 1 Introduction
- glic: GNU C standard library
- difference between library and application. Library is reusable code used by application.

## 3 Setting up Linux Development Environment
- Installing WSL2 on windows: https://www.youtube.com/watch?v=Jo_b26px0h8
    - Dont follow exactly as the step is obsolete (2024). Use Chatgpt to find out how.
    - wsl --install: enable required Windows features, installs the WSL components, sets WSL 2 as default, and installs Ubuntu
    - set username and password
    - see wsl details by wsl --list --verbose. Ensure version is 2 => meaning WSL2.
    - wsl -d <wsl_os_name>: to launch it
    - Enable Virtualization: Ensure Intel Virtualization Technology is enabled in BIOS setting
    - Enable WSL
    - Enable Virtual Machine Platform
- Installing Linux Tools for development: https://www.youtube.com/watch?v=DYvMf8W2Q7M
    - sudo apt-get update

    | Feature | apt | apt-get |
    |---|---|---|
    | User interface | More user-friendly, progress bars | More stable, script-oriented output |
    | Intended use | Interactive terminal use | Scripts and automation |
    | Available since | Ubuntu 14.04 era | Debian/ Ubuntu legacy package manager interface

    - install the following app:
        - make
        - g++/ gcc compiler
        - ssh
        - vim
        - git
        - python3
        - libncurses-dev
            - development library for building terminal-based applications in Linux
            - ncurses stands for new curses.
            - Let a program control terminal screen, position the cursor, handle keyboard input, and display text interfaces.
        - gdb (GNU debugger)
        - screen: Linux teminal multiplexer. Let u run a command in a terminal session, detach from that session and return to it later without stopping the command
            - touch .screenrc: create file in home directory.
            ```bash
            cat > ~/.screenrc <<'EOF'
            autodetach on
            startup_message off
            hardstatus alwayslastline
            shelltitle 'bash'
            hardstatus string '%{gk}[%{wk}%?%-Lw%?%{=b kR}(%{W}%n*%f %t%?(%u)%?%{=b kR})%{= w}%?%+Lw%?%? %{g}][%{d}%l%{g}][ %{= w}%Y/%m/%d %0C:%s%a%{g} ]%{W}'
            EOF
            ```
            - .screenrc file controls naming and organization of sub-screens
            - screen to open a new screen
            - Ctrl + A, then
                - c: create a new window
                - n: next window
                - p: prev window
                - ": show window list
                - 0: switch to window 0
                - w: show window numbers and names
                - Shift + a: change window name
    - remb to cd ~ after loading the distribution else it will use the original path b4 loading the distribution (/mnt/c/Users/kinda == C:\Users\kinda)
- How to run Linux code on Windows with WSL 2 & VS code: https://www.youtube.com/watch?v=bRW5r7TK6KM
    - WSL2 allows u to use Linux kernel directly on Windows
    - virtualize Linux kernel using HyperV
    - type \\wsl$ on Window Explorer to open the network of all dist folders
    - Ubuntu command:
        - lsb_release -a: List the distribution ID, description, release version and codename
        - ls -a: list all files and directories, including hidden ones
        - ls -la: detailed listing
    - install WSL extension in vscode in windows. From Microsoft.
        - click bottom left corner >< icon
        - connect to wsl using distro > select the dist: VScode starts the dist for u.
        - run ubuntu using Vscode on Windows.
    - need to install node.js on Ubuntu even if Windows has node.js.
        - sudo apt update
        - sudo apt install -y nodejs npm
        - sudo npm install -g yarn
        - node -v
    - git clone https://github.com/stoqey/ib
    - cd ib
    - code . to open another VScode window
    - to view NPM SCRIPTS windows on vscode, View > Open view... > NPM Scripts
        - run clean on NPM SCRIPTS (must have yarn installed)
    - no need reinstall docker on ubuntu, reuse windows docker.
        - Open Docker desktop on Windows
        - Setting > General > Tick Use the WSL 2 based engine
        - Resources > WSL integration > tick the dist: let window share docker with wsl distribution
        - type docker in ubuntu to see u can now use docker.

## 4 Begin - Doubly Linked List as a Library
- library: https://github.com/csepracticals/LibraryDesigning
    - dll: doubly linked list. dll.c, dll.h
- https://github.com/csepracticals/DevelopLibrary
    - not go through. Seem to be singly linked list.

## 5 Quick Compilation Steps
- https://github.com/csepracticals/LibraryDesigning/tree/master/ApplnIntegration
    - application.c
    - add_data_to_dll(dll_t *dll, void *app_data)
        - void *app_data: can be any data type (person_)
- gcc: C compiler
- gcc -g ...: GDB Flags
- gcc -g -c ...: compile a source file
- gcc -g -c dll.c -o dll.o: compile to an output object file. Compile dll.c to object file dll.o
- gcc -g application.o dll.o -o main: link application.o and dll.o to form program named main. Note that it does not have -c.
- ./main: run the program.
- Should alway be able to compile single source file independently.
- Only source files (.c/.cpp) are compilation units. Header files are not compilation units.

## 6 Summary
- object files are not human-readable file. They contains the source code in machine instruction form.
- to link the object files to create executable, one of the object file must contain the main function.

# Section 2: Understanding Header Files
## 7 What are Header Files and their Purpose?
- Header files are non-compilation units. Compiler do not compile header files.
- If header files has error, there will be compilation error.
- header file inclusion (#include):
    - recursive text substitution method
        - replace the #include <hdr.h> statement by content of hdr.h
        - if hdr.h contains #include <b_hdr.h>, replace #include <b_hdr.h> by its contents and so on.
- header file inclusion helps to achieve 2 basic principles of c/C++ language
    - define structure definitions first b4 use (define-and-use Thumb Rule)
    - declare function prototype first b4 use (declare-and-use Thumb Rule)

## 8 Relationship between Source and Header Files
- header files contains anything which needs to be exposed to other source files:
    - structure definition
    - constants and enumerations
    - macros
    - function declaration (no function definition)
    ```cpp
    // function declaration
    int foo();
    ```
    ```cpp
    // function definition
    int foo(){
        ...
    }
    ```
    - static inline functions
- source files contains anything which need not be exposed to other source files:
    - actual implementation of logic as functions whose declarations are present in hdr files
- header files conveys "what is there" and not "how it is done"
    - analogy: eating a tasty meal without knowing how it was cooked.
    - u have been using this when u use #include <iostream> and use cout without understanding how it was implemented

## 9 Text substitution
- happen in source files first b4 compilation of source files
- do not happen in header file
- pre-processing directive (#...)
    - #include: replace with file contents
    - #define for macro: replace occurence with macro and remove #define eventually.
- pre-processing because it happens b4 compilation starts.

## 10 Text substitution Example
- final compilation unit does not have #include and #define statements. In fact, final compilation unit does not have all pre-processor directives as they are removed

## 11 Text Substituion Demonstration
- gcc -E app.c -o app.i: perform text substitution on app.c and output in app.i
    - ignore all the # statement in app.i.

# Section 3: Preprocessing Directives

## 12 Problem of Duplicate Inclusion of Header Files
- most of time, we end up including same header file into 1 source file multiple times.
- This results in duplicate text substitution.
- Duplicate declaration -> Compilation error.

## 13 What are Pre-processing Directives
- all #... (not all stated below)
    - #define
    - #include
    - #undef
    - #ifdef
    - #ifndef
    - #if
    - #else
    - #elif
    - #endif
- Can be written in both sources and header files
- Processing of directives happen from top to bottom of source files.

## 14 Solution to Duplicate Inclusion of Hdr files using Preprocessing
- sandwich the content of header files between #ifndef, #define, #endif
```cpp
#ifndef __A__
#define __A__
...
#endif
```

# Section 4: COrrect way of Using Structures and Functions

## 15 Structure Definitions and Use
- always definition first and then usage for structure. define-and-use thumb rule
    - occ_t occ; //use
    - typedef struct occ_{} occ_t; //define
- if not, will have compilation error.
- put independent structure first b4 dependent structure.
- compilation of source file happens from top to bottom.

## 16 Function Declaration and use
- When compiler compiles the source files, it must first see the declaration of a function and then its usage (function call). declare-and-use thumb rule.
- compiler dont consider the function definition while compiling the source files to create object files
- function definition must be present in exactly one and only one source file.
- function definition is only considered in linking phase.

## 17 Recursive Dependency
- Pointer usage vs complete usage for a structure
```cpp
struct emp_t{
    designation_t des; //complete usage
    occ_t* occ; //pointer usage
}
```
- a compiler must know the complete size of the structure at compile time.
- 32b machine, all pointer variable is 4 Bytes
- 64b machine, all pointer variable is 8 Bytes
- compilation fails if 2 structs depend on each other. Chicken and egg problem. Recursive dependency
    - emp_t depends on occ_t
    - occ_t depends on emp_t
- error: field 'occ' has incomplete type.

## 18 Solution to Recursive Dependency
- forward declaration: tells the compiler that struct occ_t will be defined in future, pls tolerate if it is used as pointer.
```cpp
struct occ_t; //forward declaration
struct emp_t{
    struct occ_t* occ; //know it is 4 (32-b machine) or 8 Bytes (64-b machine)
};

struct occ_t{
    struct emp_t boss;
};
```
- break the recursive dependency by using pointer usage.
- rule of thumb:
    - avoid recursive dependency
    - if cannot avoid recursive dependency, use forward declaration + pointer usage

# 5 Quick Creation of Static and Dynamic Libraries

## 20 Resuming with Doubly Linked List Library
- library is a collection of related object files. object files are compiled version of source file.
- https://github.com/csepracticals/LibraryDesigning/tree/master/Morefunctions
- dll_util.c and dll.c implements the functions declared in dll.h
    - doing these for the sake that our library code spawns across 2 source files.

## 21 Static and Dynamic Libraries - Quick Creation
- Linux c/C++ libraries can be:
    - static libraries (.a)
        - gcc -c dll.c -o dll.o: compile dll
        - gcc -c dll_util.c -o dll_util.o: compile dll_util
        - ar rs libdll.a dll.o dll_util.o: create static library
            - ar: archive
            - r: replace or add. Insert object files into archive, replacing existing members with same names
            - s: symbol index. create or update symbol table index so the linker can find symbols efficiently
    - dynamic libraries (.so)
        - .so: shared object
        - PIC: Position Independent Code
            - to understand this, need to understand asm code
        - gcc -c -fPIC dll.c -o dll.o: compile dll
        - gcc -c -fPIC dll_util.c -o dll_util.o: compile dll_util
        - gcc dll.o dll_util.o -shared -o  libdll.so: create dynamic library

## 22 Linking with Static Library
- gcc -c application.c -o application.o: compile application
- gcc application.o -o main -L . -ldll: link to create exe
- ./main: run exe
- creation of object file is called compilation
- creation of exe file is called linking

## 23 Linking with Dynamic Library
- gcc -c application.c -o application.o: compile application
- place libdll.so in default location in /usr/lib. If /usr/lib is not available, use /usr/local/lib
- run sudo ldconfig to tell OS that we have updated the shared library files, so OS will note and update its database to include the libdll.so
- gcc application.o -o main -ldll: link to create exe
    - no need to state the -L path
- ./main: run exe