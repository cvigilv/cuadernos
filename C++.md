---
title:    C++
author:   Carlos Vigil Vásquez
toc:      true
geometry: margin=1in
lang:     en
---

\newpage

# Guide to Scientific Computing in C++
## A First C++ Program
Let's begin with the common _"Hello World"_ program, written in C++.

```{.c++, .numberLines}
#include <iostream>

int main(int argc, char* argv[])
{
	/* This is a comment and will be ignores by the compiles.
	Comments are useful to explain in English what the program does */
	
	// Print "Hello World" to the screen
	std::cout << "Hello World\n";
	
	return 0;
}
```

Let's go line by line explaining what we see:

* `#include <iostream>` corresponds to the inclusion of a header file that contains functions we can use in our program.
_iostream_ pertains to **input and output streaming** and is requiered if you want to input data from the keyboard or output data to the console.
* `main(int argc, char* argv[])` corresponds to the main function that runs when we run the program.
Inside the parenthesis we find the user-specified arguments that the code will execute with.
* `/* ... /` this is a multiline comment and `//` is a comment in the line.
* `std:cout` is a contraction of **console output**, that is, printing to the screen / terminal.
* `int` befor the `main` function means that the output of the function is an integer.
`return 0` indicates to the computer that the program has reached the end without encountering any problems.

White spaces are ignored completely when running the program, therefore indenting is not necessary at all.
It's good practice to have indenting in your code to improve readability, but if necesarry you can skip this.

## Compiling a C++ Program
To compile from the command line we will use GNU `gcc` compiler.
Let's compile our _"Hello World"_ program using the following command:

```{#CompileWithOutputFlag}
	$ g++ HelloWorld.cpp -o HelloWorld
	$ ./HelloWorld
	Hello World
```

The `-o` flag indicates the name of the executable file compiled from `HelloWorld.cpp`. 
As we see in line 3, the output of our program is the message _Hello World_ as we wrote in our C++ code.
If we were to run the same command above, but this time without the `-o` flag and it's executable name, then the executable file is produced using a default name:

```{#CompileWithoutOutputFlag}
	$ g++ HelloWorld.cpp
	$ ls
	a.out
	HelloWorld.cpp
	$ ./a.out
	Hello World
```

Know that we know that when compiling with `g++` we can add flags, here is a table with some common flags used:

| Flag      | Contraction    | Use case                                                                                                         |
| :-------: | :------------: | :-------                                                                                                         |
| `-Wall`   | Warning all    | This will warn us of anything unexpected that is not actually an error, but will still create an executable file |
| `-Werror` | Warning errors | This will halt the compiling of our program if an error is encountered.                                          |
| `-O`      | Optimize       | Optimization of the program at compile.                                                                          |
| `-g`      |                | Produces a non-optimized version of the code with the debugging information.                                     |
| `-lm`     | Math library   | Link to a library of mathematical routines.                                                                      |
Table: Commonly used flags for GNU `g++`

## Variables
This correspond to entities that can store values, objects, other entities, etc.
In C++ variables must be decllared to be an appropiate type before they are used.

