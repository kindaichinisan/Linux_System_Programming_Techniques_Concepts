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
    - wsl -d `<wsl_os_name>`: to launch it
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
        - replace the `#include <hdr.h>` statement by content of hdr.h
        - if hdr.h contains `#include <b_hdr.h>`, replace `#include <b_hdr.h>` by its contents and so on.
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
    - u have been using this when u use `#include <iostream>` and use cout without understanding how it was implemented

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

# Section 6: Four Stages of Compilation Process

## 25 4 Stages of Compilation
- Preprocessing
    - text substitution
    - execute preprocessing directive
- Compilation
    - generate asm code
- Assembler
    - generate machine code/ object file from compiled code
    - generate library?? or static library only??
- Linking
    - generate final exe (/shared library??)

## 26 Stage 1 of 4 - Preprocessing Stage
- role of header file is over
    - all `#include<abc.h>` are replaced by contents of abc.h recursively
    - all `#define` are applied and removed
- gcc -E test.c -o test1.c

## 27 Stage 2 of 4 - Compilation Stage
- gcc -S test1.c -o test.s

## 28 Stage 3 of 4 - Assembler Stage
- gcc -c test.s -o test.o
- objdump -D test.o > temp
    - left is virtual address, machine code, right is asm code
- test.o: elf64 file format
    elf: executable and linking format (for Linux)

## 29 Stage 4 of 4 - Linking Stage
- without linking, test.o would not know how to invoke external functions such as foo()
- linking is providing an access to external functions and global variables
- gcc test.o -o test.exe
- objdump -D test.exe > temp
- exe is also elf format.
- different sections
    - data
    - bss
    - text

# Section 7: Build Project using Makefiles

## 30 Introducing Makefile
- Makefile is a program building tool which runs on Unix, Linux and their flavours.
- It aids in simplifying building the software program that may depend on other libraries.
    - for big projects that have eg. 200 source files, 20 header files (Raw material)
    - want to create dishes (output) from Raw material:
        - 10 static libraries
        - 5 shared libraries
        - 3 executables 
- Functions of Makefile:
    - Compiling
    - Linking
    - Creating required libraries (static and dynamic)
    - Creating required executables
    - Installing libraries and executables
    - Updating dependencies
- https://github.com/csepracticals/LibraryDesigning/tree/master/MakefileAssignment

## 31 Makefile Analogy - Dependency Tree
- Makefie works on concept of dependency tree.
    - L0 is top level
    - L1 is used to make L0
    - L2 is used to make L1 and so on.
    - L1 depends on L2.
    - L N element cannot be prepared unless all its immediate descendant elements at L N+1 are available
    - Preparation takes place from bottom to up
    - L0 is the root element (target we want to prepare)
    - If element at Level N is changed, all ancestors from Level N-1 up to root of the tree needs to be updated

## 32 Makefile Assignment - part1
- https://github.com/csepracticals/LibraryDesigning/tree/master/MakefileAssignment
- libcal.a depends on:
    - common_math.o
        - common_math.c
        - common_math.h
    - complex_math.o
        - ...
    - trig_math.o
        - ...

# 33 Makefile Assignment - part2
- In Makefile, we write rules:
    - What we want to prepare
    - What are raw materials we need to prepare the final dish
    - Steps to prepare
- Example: common_math_o:common_math/common_math.c gcc -c -I common_math common_math/common_math.c -o common_math/common_math.o
    - common_math_o
    - common_math/common_math.c
    - gcc -c -I common_math common_math/common_math.c -o common_math/common_math.o
    - -I `<path>` is used to specify the location of header files
- Example: libcalc.a: trig_math/trig_math.o common_math/common_math.o complex_math/complex_math.o ar rs mylibmath.a common_math/common_math.o complex_math/complex_math.o trig_math/trig_math.o
    - libcalc.a
    - trig_math/trig_math.o common_math/common_math.o complex_math/complex_math.o
    - ar rs mylibmath.a common_math/common_math.o complex_math/complex_math.o trig_math/trig_math.o
- Example: exe: main.o libcalc.a gcc main.o -o exe -L . libcalc.a -lm
    - exe
    - main.o libcalc.a
        - no need state the standard math lib here
    - gcc main.o -o exe -L . libcalc.a -lm
        - need to state the standard math lib here (-lm)
- order in the Makefile does not matter.
- TARGET: final output we want to make (L0 element)
    - TARGET is not a keyword, just a subvariable.
- clean: to delete everything that were made
- use Tab at beginning of recipe lines and not space in Makefile
    - GNU Make requires a literal tab at beginning of recipe lines because of a historical design decision in Make's syntax
    ```makefile
    all:
        g++ main.cpp -o main
    ```

## 34 Final Makefile
- Makefile do not proceed from top to bottom.
- Makefile follow DFS
- first line indicate the L0 element. TARGET is not a keyword.
- Make file is made of line of rules
- Rule order
    - Line 1 (exe)
        - Line 2
            - Line 12
                - Line 13
            - Line 4
                - Line 10
                    - Line 11
                - Line 6
                    - Line 7
                - Line 8
                    - Line 9
                - Line 5
            - Line 3
- Makefile always need a clean rule to delete everything that was made by the Makefile. Use make clean.
- use make to execute the Makefile (line 1).
- use make `<rule>` to execute particular rule of Makefile.
- 1 directory can only have 1 Makefile.

# Section 8: Programmable Libraries - Generics

## 35 Introduction
- Programmable libraries:
    - teach the library to perform application-specific tasks
    - while library preserves the application-agnostic (generic) property
        - library should be written in generic manner instead of for a specific program
    - make our library programmable - teach it at runtime how to perform application-specific operations
    - need to know about function pointers/ callbacks
    dll uses void* so that it can work for all data type.
- https://github.com/csepracticals/LibraryDesigning/tree/master/ProgrammableLib

## 36 Revisiting DLL
- application.c

## 37 Problem Statement
- implementation of match_student_rec_by_key and match_employee_rec_by_key is logically the same.
- difference is the way u mach record with key
    - code duplication

## 38 Solution - Responsibility Delegation
- delegation = offload
- offload responsibility of searching a particular record by key to library.
- write search logic in only one place in library code.
- application need to teach library how to match application record with given key because library is agnostic to application data.
- https://github.com/csepracticals/LibraryDesigning/tree/master/ProgrammableLib/search_callbacks
- search by key callbacks
    - step1: callback implementation in application.c
        - function signature must be generic (not application specific). use void*
        - static int search_student_db_by_key(void *data, void *key);
        - static int search_employee_db_by_key(void *data, void *key)
    - step2: define callback function pointer in library dll.h. Match step1 function signature
        - int (*key_match)(void*, void*);
    - step3: search callbacks registration in library
        - add new function in library (register_key_match_callback)
        - void register_key_match_callback(dll_t *dll, int (*key_match)(void *, void *));
    - step4: add generic search function in library
        - void* dll_search_by_key(dll_t* dll, void* key)
    - step5: register callback function (step1) with library
        - register_key_match_callback(student_db, search_student_db_by_key);
        - register_key_match_callback(employee_db, search_employee_db_by_key);

## 39 Using Programmable DLL library - Code walk
- offload search function from application to library.

## 40 Search Callback Summary
- we taught the DLL library by registering key match callback to how to search the application data held by DLL depending on the key.
- application developer need to write key match callback
- library is still application-agnostic.

## 41 Comparison Callback
- Goal: Application should be able to insert the app data in DLL in sorted order.
- follow exact same step as searching a record.
    -step1: callback implementation in application.c
        - static int student_comparison_fn(void *stud1, void *stud2)
    - step2: define callback function pointer in library dll.h. Match step1 function signature
        - int (*comparison_fn)(void *, void *);
    - step3: search callbacks registration in library
        - void register_comparison_callback(dll_t *dll, int (*comparison_cb)(void *, void *));
    - step4: add generic insert function in library
        - int dll_priority_insert_data (dll_t *dll, void *data)
        - insertion sort.
    - step5: register callback function (step1) with library
        - register_comparison_callback(student_db, student_comparison_fn);
        - register_comparison_callback(employee_db, student_comparison_fn);

## 42 Comparison Callback Demo
- simple code walkthrough + demo run

## 43 Summary
- program general purpose library using callback
- application developer need to specify key_match and comparison function and register with library.
- library need to implement search logic and insertion algo

# Section 9 Iterative Macros

## 44 Getting Started
- iterate through data structure (Linked list, trees, etc.).
- traditional approach
    - start current node at head
    - while node is not at end, process current node and go to next node
- macro approach
    ```cpp
    ITERATE_LIST_BEGIN(listptr, current){
        /*process current node*/
    } ITERATE_LIST_END(listptr, current);
    ```
    - readable, no error-prone, handy and easy
    - work well with complex data structure like tree or graph.

## 45 For loop Semantics
```
for(initialization; condition; next_iteration){
    body;
}
```
- initialization -> condition -> body -> next_iteration
- loop: condition -> body -> next_iteration

## 46 Iterative Macros for LinkedList
- write iterative macros for linked list
- delete-safe loop due to ```_node_ptr=node_ptr->right;```
- write delete-safe iterative macro
```cpp
#define ITERATE_LIST_BEGIN(list_ptr, node_ptr)                              \
{                                                                           \
    dll_node_t *_node_ptr = NULL;                                           \
    node_ptr = list_ptr->head;                                              \
    for(; node_ptr!= NULL; node_ptr = _node_ptr){                           \
        if(!node_ptr) break;                                                \
        _node_ptr = node_ptr->right;

#define ITERATE_LIST_END  }}
```

## 47 Iterative Macros for Binary Trees
- iterate over BST in inorder sequence
- if node of tree has pointer to its paranet, no need to write recursive function
- recursive logic performs poorly compared to iterative logic.

## 48 Tree Iterators Prerequisites
- write 2 pre-requisite functions:
    - tree_node_t *get_left_most (tree_node_t *node);
    - tree_node_t *get_next_inorder_succ (tree_node_t *node);
- iterative macro is just wrapper for for loop

## 49 Tree Iterators Implementation
- traverse in inorder sequence print sorted #

## 50 Summary
- iterative macros makes it easy and handy to iterate over data structure. Esp so for complex data structure.
- ensure parenthesis are balanced for iterative macros.

# Section 10: Glue Based Data structures

## 51 Introduction
- Glue way of organizing data structures
- Benefit of using Glue libraries over Traditional libraries
    - b4 this is all Traditional libraries
- Linux kernel code uses GLUEd version of standard data structures such as trees, linked list, etc.
- Glue libraries used in industry too.
- Name change: dll -> glthreads
    - gl: glued
    - linked list known as threads as they are linear data structure, similar to thread.
- Method learnt can be applied to other Data Structure
- https://github.com/csepracticals/DevelopLibrary/tree/master/glthreads

## 52 Glthreads Definition
- Glthreads: Glued doubly linked list
- no void* data in node of glthread_node_ vs dll_node_
    - how do glthreads hold application data?
- additional unsigned int offset in gldll_ vs dll_

## 53 Difference - Traditional Vs Glue Based DLL
- glthreads and glnode for Glued Doubly Linked List and its node
- application data has some sticky gum using which it can stick itself to any linked list.
    - sticky gum: glnode
    - linked list: glthread
- glue: stick an object over another object

## 54 Structure Field Offset
- write a c macro which computes the offset of a given field in a given c structure
    - offset: offset from start of object.
    ```cpp
    #define offsetof(struct_name, field_name) \
    unsigned int)&((struct_name *)0)->field_name
    ```
- glthread_node_t at the end of struct emp_t.
    - glthread_node_t acts as a glue
- use offsetof(struct_name, glnode) to get the starting address of object.


## 55 GLNode Insertion
- to add an object to glthread (DLL), glnode has to be a member of structure.
- glnode is used to stick the object to the glthread.

## 56 API to insert a glthread node in a glthread list
- implement void glthread_add(glthread_t *lst, glthread_node_t *glnode)
- implement static void _glthread_add_next(glthread_node_t *curr_node, glthread_node_t *new_node)
    - _ for private function

## 57 GLThread Iteration
- use offsetof(struct_name, glnode) to get the starting address of object.
- iterate to print emp_details();
- Should know how to write iterative macros for Glued data structure

## 58 GLNode Removal
- void glthread_remove(glthread_t *lst, glthread_node_t *glnode)
- similar to remove middle node from traditional DLL.

## 59 Code Walk - Demo
- main.c code walk through

## 60 Traditional List Problems
- Problem of traditional DLL
    - Insertion: need as many malloc as #linked list to add object to the linked lists
    - Deletion: need to remove reference in all the linked list and un-reference and finally free the object.
        - Time complexity: k * O(n)

## 61 GLThread Benefits
- Glthreads benefits:
    - Insertion: need 0 malloc to add object to the linked lists. glnode is declared in the struct already for each linked list. no explicit malloc needed
    - Deletion: use 1 linked list to find reference, then remove reference in all the linked list and finally free the object.
        - Time complexity: O(n) + (k-1) * O(1)
- Use GLThread if u have multiple data structures storing same object.

# Section 11 Opaque Pointers

## 62 Introduction
- widely used in industry
- opaque pointers are a way to isolate one code from one another, while ensuring seamless integration between them
- OOP terminology: Opaque class = class with all its instance variable private.
- c is a procedural language and does not have notion of class. How to define private?

## 63 Typical Library Design
- what is the problem? Not allowed to declare private for struct.

## 64 Problem Statement
- Problem:
    - developer could reinvent the wheel and write own node insertion code.
    - developer could have access to all node's members and exploit them.
    - developer could introduce a bug.
- Solution:
    - restrict node's member in the file so that developer could not write new code which requires access to node's internal members.
    - developer only can make use of public API defined by the library creator.
    - but how to achieve this? Use opaque pointer.

## 65 Solution Strategy
- to make structure (ll_node_t) opaque to external world (application.c), define structure definition in library source files rather than header file.
- Not expose structure definition to outside world, so outside world would never know its internal members details.
    - typedef struct ll_node_ ll_node_t;
    - Cannot use sizeof() in outside world => need to provide function ll_node_t* malloc_new_node();

## 66 Conclusion
- opaque pointers ensure that developer are mer user of library
- library owning team can improve the library functionality independent of applications using library public API as long as publc API interface are not changed.
- opaque library is a black box to external world. application owner can use it through exposed public API but must not bother about its internal design and implementation.

# Section 12: Bit Programming

## 67 Introduction to Bit Programming
- Most programming language provide developers ability to manipulate memory at the finest granularity of 1 Byte (char). Example: C/ C++/ Java
    - Cannot manipulate memory less than 1 Byte using primitive/ inbuilt data types.
- some problem require memory manipulation at bit level
- Bool is 4B on most compilers.
- Pre-requisite: boolean algebra
- MSByte: lower address, LSByte: higher address
- Memory is Byte-addresable: every byte has an address
- bit does not have address.
- Bitwise operator:
    - &
    - |
    - ~: negate
    - ^: xor
    - <<
    - >>

## 68 XOR Operator
- segregate the mixture of 2 things
- R=A xor B
    - R xor A = B
    - R xor B = A
- interview question: how to implement a doubly linked list in singly linked list node structure
    - store inside the singly linked list node structure the address of previous and next node.
    - In main program, store the prev node address.
    - for first node (either head or tail), no prev node address.
    - use xor. prev xor (prev xor next) = next
    - limitation: cannot start traversal in middle of linked list. Must start from head of tail.

## 69 Using Bits as Flags
- pure power of 2 has only 1 bit set
- use uint8_t, uint16_t, uint32_t to track different # of bits
```cpp
#define SET_FIRST_BIT (1<<0)
#define SET_SECOND_BIT (1<<1)
uint8_t alex_f;
alex_f |= SET_FIRST_BIT //set
alex_f &= ~SET_SECOND_BIT //unset
```
```cpp
//n is uint8_t, uint16_t, uint32_t
//BIT_F is power of 2: 1, 2, 4, etc.
#define SET_BIT(n,BIT_F) (n |= BIT_F)
#define UNSET_BIT(n,BIT_F) (n &= ~BIT_F)
#define IS_BIT_SET(n,BIT_F) (n & BIT_F)
#define TOGGLE_BIT(n, BIT_F) (n ^ BIT_F)
#define COMPLEMENT16(n) (n^0xFFFF) // (~n)
```

## 70 Using Enums as Bits
- Sometime, it is good to define enum as power of 2
    - Advantage: we can use enum like BITS
    - Disadvantage: memory inefficient

## 71 Bit Pattern Matching
- input binary string with base bit pattern (1xx1, x=Dont care)
- real world use case: block destination IP address of 100.100.x.x
    - m-trie: data structure based on bit pattern matching, used to implement ACLs in firewall

## 72 Bit Pattern Matching Steps
- Base bit pattern:
    - Create mask using the following rules:
        - replace all bit to be matched by 1
        - replace all X by 0
        - eg 0000 10xx 10xx xxx0 => 1111 1100 1100 0001
    - Create template using the following rules:
        - replace all X by 0
        - eg 0000 10xx 10xx xxx0 => 0000 1000 1000 0000
```cpp
if(input & mask == template){
    match
}
else{
    no match
}
```

## 73 BitMaps
- bitmap: array of bit
- Memory is always allocated in units of Bytes, not bits
- To save memory, create wrapper API which work on Byte-addressable memory but able to perform operation at bit level.
- boolean is normally 4 Bytes in most compilers.
- malloc(4): allocate 4 Bytes
```cpp
typedef struct bitmap_{
    char *bit_arr;
    int arr_size;
} bitmap_t;
int n=45; //45 bits
int N_bytes=n/8+((n%8)?1:0); //N_bytes=6
//put ((n%8)?1:0) as ?: has lower precedence than +.
bitmap_t *bitmap=(bitmap_t *)calloc(1,sizeof(bitmap_t));
bitmap->bit_arr=(char*)calloc(N_bytes,sizeof(char));
bitmap->arr_size=n;
//allocating twice is okay.
//allocating bitmap is for pointer of bit_arr and arr_size
//allocating bitmap->bit_arr is for the bitmap.
//both memory are for different things. No repeat.
```
## 74 BitMap APIs and Implementation
- efficient code for bitmap_set_all() and clear_all()
    ```cpp
    uint8_t* v=(uint8_t*)(bitmap->bit_arr);
    v=0; //one operation
    ```
- to set 156 bits
    ```cpp
    //set 64b, left 92b
    //set another 64b, lef 28b
    //set 16b, left 12b
    //set 8b, left 4b
    //set individual bit.
    //need 8 operations
    ```
- to create a bitmap api with following functions
    ```cpp
    bitmap_t* bitmap_create(int n_bits);
    void bitmap_print(bitmap_t *bitmap);
    void bitmap_set_bit(bitmap_t* bitmap, int index);
    void bitmap_unset_bit(bitmap_t* bitmap, int index);
    bool bitmap_is_bit_set(bitmap_t* bitmap, int index);
    void bitmap_clear(bitmap_t* bitmap);
    void bitmap_set_all(bitmap_t* bitmap);
    void bitmap_free(bitmap_t* bitmap);
    ```

## Section 13: Machine Endianness

## 75 Machine Endianness
- Endianness refers to sequential order in which bytes of data are stored in computer memory. Rearrange the bytes but not the bit.
- Big-endian
    - MS Byte at lower address (network byte order)
- Little-endian
    - MS Byte at higher address (host byte order)

## 76 Program to find Machine Endianness
- Interview question: write a c program to determine if machine is big endian or little endian
```cpp
/* return 0 - Big endian, 1 for Little endian*/
int machine_endianness_type(){
    unsigned short a = 1;
    char first_byte=*((char*)&a); //lower address
    if(first_byte==0)
        return 0; //big endianness
    else
        return 1; //little endianness
}
```