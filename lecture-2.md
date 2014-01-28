#COMS3157 Advanced Programming : Lecture 2

Today, we will finish up the creation of Makefiles and move on to basics of the C language.

Notes written by [Matt Piccolella](http://www.mattpiccolella.com).

-----

## Makefile

In your `myadd.h` file, provide the function prototype. We then define that function in `myadd.c`. To make the object file of a certain file, we declare the steps necessary to get to the object file in our Makefile. We then run:

```
$ make main.o
```

The compiler then looks for the steps necessary to make the file, creates it, then prints the steps that were necessary to get to that point. If you simply run:

```
$ make
```

It will make the first out-of-date object file that is present in our MakeFile, which is seen as follows:

```C
main: main.o myadd.o
		gcc -g main.o main.c -o main

main.o: main.c myadd.h
		gcc -c -Wall -g main.c

myadd.o: myadd.c myadd.h
		gcc -c -Wall -g myadd.c
```

If we type `make`, it tries to make `main`. It will then see that `myadd.o` is not there, first try to make `myadd.o`, then continue to make `main`. It recursively does this until it has all the pieces it needs to create `main`.

The benefit of specifying a Makefile is a reduction in runtime. Only necessary to recompile things that are necessary. Also, important to specify a `clean` that will remove all things that were created:

```C
clean:
	rm -f *.o main
```

Then simply run:

```
$ make clean
```

We could also specify it is a 'phony':

```C
.PHONY: clean
clean:
	rm -f *.o main
```

We can also declare variables in our Makefile to reduce the number of keystrokes we have to use:

```C
CC = gcc
CFLAGS = -g -Wall

main.o: main.c myadd.h
	$(CC) $(CFLAGS) -c main.c
```

When we use variable names `CC` and `CFLAGS`, the Makefile knows that it should do these things automatically. We can define other variable names, but these are the ones that Makefile knows about.

We can use 'all' as well:

```C
.PHONY: all
all: clean main
```
--------
## C Language Basics
Integer data types in C:

```
char: 1 byte -> 0 ~ 255 (char) -128 ~ 127 (unsigned char)
short: 2 byte -> -2^15 ~ 2^15 - 1 
int: 4 byte -> -2^31 ~ 2^31 - 1
long: 4/8 byte
long long: 8 byte
```

We can use precise types to ensure the number of bits:

```
int16_t, uint32_t
```

We mostly do this for low-level programming, where the number of bits is incredibly important.

Because there are 16 possibilities in 4 bits, it would be nice to have 16 different digits. We do! It's called hexadecimal number:

```
0000 0	1000 8
0001 2	1001 9
0010 2	1010 10/A
0011 3	1011 11/B
0100 4	1100 12/C
0101 5	1101 13/D
0110 6	1110 14/E
0111 7	1111 15/F
```

### Two's Complement

If we want to represent negative numbers, we would use two's complement. We use the leftmost bit as an indicator of negative or positive, then find the value by flipping all the other bits and then add one. For example:

Binary | usigned decimal | two's complement decimal
------ | --------------- | ----------------------- 
000    | 0               | 0
001    | 1               | 1
010    | 2               | 2
011    | 3               | 3
100    | 4               | -4
101    | 5               | -3
110    | 6               | -2
111    | 7               | -1

Generally, to negate in two's complement, we flip the bits and add one.

Two hexadecimal digits is one byte. If we want to write a hexadecimal digit, we prepend:

```C
0x7F2D152F
```

Memorize bit patterns like:

```C
0x7F // 127, m
0xFF // 255 or -1
0xFFFFFFFF // -1 for signed int
```

We can use different flags:

```
printf("%u", -1); // Prints some large number, converts it to unsigned
```

Important to remember that `char` is a number, converting using the ASCII key standard. 

In C, everything is a number. A string is not what it appears to us in C. 

### Escape Sequences
There are certain special characters in ASCII that we want to use that also have numeric values:

```C
'\n' // 10
'\r' // 13
```

We can represent octal numbers using a backslash:

```C
'\0' == 0
```

### Floating Point Numbers

There are also floating point numbers:

```
float, double
```

People mostly just use double now. These floating point numbers store using a type of scientific notation, use a certain number of bits to store the precision part, the exponent part, etc.

### Expressions and Statements
Expressions have a value. For example:

```
5, x, x+2, myadd(1,2)
```

Each of these things have a value. Statements, however, do something. For example,

```C
if (x == 3) { 
	goto label1;
}
```

These don't have values, but they do something.

Sometimes you can have an expression and a statement:


```C
x = 7;
// All of these are equivalent and are statements
x = x+1;
x+=1;
// Oddball, does the same thing, but value is 7, not 8, like the others.
++x;
```



