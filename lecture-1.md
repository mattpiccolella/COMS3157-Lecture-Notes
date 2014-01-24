#COMS3157 Advanced Programming: Lecture 1
Today, we will go over basic UNIX shell commands and working on the CLIC system to ensure that we will be able to effectively complete our laboratory assignments. This will serve as an introduction to programming in a UNIX environment. We will also start with an introduction to C.

Notes written by [Matt Piccolella](http://www.mattpiccolella.com).

------

##CLIC
We will be working on the Ubuntu Linux machines in the CLIC laboratory for all of our assignments. Once you have made an account, to access your machine:

```
$ ssh mjp2220@clic.columbia.edu
```

Although we can work remotely, try to go to the CLIC lab at least once. That is where recitation is being held.

CLIC is a cluster of machines, the SSH command will redirect you to one of the machines labeled CLIC. If you want to know the full name of the host, type:

```
$ hostname -f 
```

## Unix Commands

Print full contents of all directories:

```
$ ls -al
or
$ ll
```

This is an example of an alias. Instead of typing one thing, we can type another thing in its place. Normally used to shorten the type sequence. Use the `alias` command to view all defined aliases:

```
$ alias
alias ll="ls -alF"
```

Learn more, if necessary, though I believe I should know most of the ones required.

Possibly look up these commands, different flags for them:

```
locate, history, date, diff
```

-----
## Basic C Programming: Compiling and Linking

Write a basic C program, compile it:

```
$ gcc main.c
$ ls
a.out main.c
```

To execute the executable (`a.out`), simply type:

```
$ ./a.out
```

"Hello, World!" in C:

```C
#include <stdio.h>

int main() {
	printf("Hello, World!");
	return 0;
}
```

Line 1 is a pre-processor directive. It tells the compiler to put the content from the specified file into the file at that place. Similar to an import, but more of a textual replace. The angular brackets mean they are from the system, not user-defined.

Each file must have a main, should return an integer. 0 represents a successful return, non-zero represents some kind of a failure.

We can add other functions in the file:

```C
#include <stdio.h>

int myadd(int x, int y) {
	return x + y;
}

int main() {
	printf("The result is %d\n", myadd(1,5));
	return 0;
}
```

Best to include your functions first, before `main()`, where it is called. If we don't, we get a warning from `gcc`, because of an implicit function declaration. To see all warnings, use:

```
$ gcc -Wall
```

However, to avoid this warning, we can declare a function before we add its specific definition. By adding the function declaration/prototype, 

```C
#include <stdio.h>

int myadd(int x, int y);

int main() {
	printf("The result is %d\n", myadd(2,4));
	return 0;
}

int myadd(int x, int y) {
	return x + y;
}
```

When we run our `gcc` command, there are two steps:

1.) Compile the C file into an object file.

2.) Link the file to an executable.

If we only only want to compile, we can run:

```
$ gcc -c main.c
$ ls
main.c main.o
```

As we see, we don't get `a.out`, but instead `main.o`, which represents the machine code.

Once we have this object file, we can link the object file to an executable, either under the name `a.out` or another name of our choosing:

```
$ gcc main.o
$ gcc -o main main.o
```

What if we break up our functions into different files?

```
$ cat main.c
#include <stdio.h>

int myadd(int x, int y);

int main(int argc, chart **argv) {
	printf("The number is %d\n", myadd(1, 2));
	return 0;
}
$ cat myadd.c
int myadd(int x, int y) {
	return x + y;
}
$ gcc -c main.c
$ gcc -o main main.o
```

We will get an error if we do this! We declared a function in `main.c` but we didn't define it. We thus need to specify the `myadd.o` file in our final linking into the executable:

```
$ gcc -c myadd.c
$ gcc -o main main.o myadd.o
```

This links together to produce the main executable, which can then be run.

Why do we need to do the linking separately? It allows us to make changes to the functions that we are calling without having to recompile the `main()`. We don't need the `main.c` code, all we really need is the `main.o` code. This way, if there is a "secret algorithm" a developer doesn't want us to know, all we can see is the object file. 

We can also add headers, which are separate declarations of functions, so we do not need to hardcode the signature. We do this using quotes:

```
$ cat myadd.h
int myadd(int x, int y);
$ cat main.c
#include <stdio.h>

#include "myadd.h"

int main() {
	printf("The number is %d\n", myadd(1,6));
	return 0;
}
```

It is good practice to include your header file in the function definition (`myadd.c`):

`myadd.c`:

```C
#include "myadd.h"

int myadd(int x, int y) {
	return x+y;
}
```

If our function is already defined, though, we do not want to redefine it. We can take a measure to prevent this:

`myadd.h`:
```
#ifndef __MYADD_H__
#define __MYADD_H__

int myadd(int x, int y);

#endif
```

The pound line pre-processors allow us to avoid errors of including the same header file more than once. This is called include guard.

To compile and link files takes a lot of commands. We want to use `make` to make it easier for us to run things more easily. Create a file called `Makefile`:

```
main.o: main.c myadd.h
		gcc -c main.c
myadd.o: myadd.c myadd.h
		gcc -c myadd.c
```

We then need to run our command:

```
$ make main.o
```

The command seeks to make the specified target. Our `Makefile` tries to find if it has a target we are talking about. If it does, it produces the dependencies that are needed to create it.

We will then have our `main.o` file. If we run `make` again, we will get the fact that the file is up to date:

```
$ make main.o
make: 'main.o' is up to date.
```