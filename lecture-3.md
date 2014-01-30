#COMS3157 Advanced Programming: Lecture 3

Today, we will go over the differences between expressions and statements, including loops and control flow. We will also go over variable types.

Notes written by [Matt Piccolella](http://www.mattpiccolella.com).

-----

## Expressions and Statements

```C
z = 5;
```

'z' is an 'l' value, and 5 is an 'r' value. These are things that can appear on the left and right side of a statement. All 'l' values can be 'r' values, but not all 'r' values can be 'l' values. An example of this would be a constant.

There are no boolean values in C, only integers. A true expression evaluates to 1, but a false expression evaluates to 0. For example:

```C
5 == 2 + 3 // 1
5 == 2 + 4 // 0
```

Any non-zero integer value is read as true, as well:

```C
if (5) // evaluates to 1, is executed
```

'^' is an exclusive or:

```
0 ^ 0 -> 0
0 ^ 1 -> 1
1 ^ 0 -> 1
1 ^ 1 -> 0
```

A bit operator is a very good way to take a remainder. For example:

```C
x & 0xff
```

is equivalent to saying:
```C
x % 256
```

Negation can be used to flip all the bits:

```C
x & ~0xf
// 0000 0000 … 1111 -> 1111 1111 … 000
```

```
x = 1
x << 1 // Multiply by two
x << 3 // Multiply by eight
x >> 1 // Divide by two
```

One thing to worry about is sign extend in C, to make sure that shifting a negative number doesn't cause the shift to malfunction.

More basics about the language…

## Variables

```C
foo() {
	int x;
	x = 0;
	{
		int x;
		x = 1;
		printf("%d", x);
	}
	printf("%d", x);
}
```

This block defines a function scope. Within a new set of curly braces, we have another scope. The 'x' inside the block is independent of x above the block. The first `printf()` will print 1, and the second `printf()` will print 0.

The 'x' inside of the block is called a local, stack, or automatic variable. They come into existence automatically when we define them, and they are deleted automatically when the program leaves its scope.

Another kind of variable is one passed as a parameter, which is also a local variable, defined within the scope of the function.

You can declare a variable to be global by using the `extern` keyword. 

What if we have a variable that we want to be access from within functions in the same file, but from from files on other ways? We can use static:

```C
static int fs = 0;
```

This means there is only class access for this variable. It will not be made available to other files.

There is a third kind of static variables. It can be put inside a function:

```C
int foo(int a) {
	static int func_s = 0;	
}
```

This behaves the same way as the other static function, but can only be accessed in the function.




