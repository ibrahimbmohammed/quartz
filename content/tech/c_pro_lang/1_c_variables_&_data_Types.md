---
title: 🪴  C first note.
enableToc: false
---

```c
int x; //declaration

x = 123; // intialization

int y = 231; // declaration + initialization

```

Convention varible declaration, similar to most programming language.

## C Data Types

Data types are simply types of data being stored in a C programme, data types are used when defining a variable or function, in general term a data type is what tells a computer how to interpret a value.

#### C's data types falls under 5 category.

![C Data Types | 400](https://www.naukri.com/learning/articles/wp-content/uploads/sites/11/2021/10/C-Data-Types-1024x536.png)

![[Screen Shot 2022-12-11 at 2.24.39 AM.png | 400]]

### Primary Data Types

> **Intergers\***: (int) are positive and negetive whole numbers without decimals in them eg. 2, 340, or 900 .

> **Char**:(char) refers to all the ASCII characters set in single quotation eg. 'A' , 'b', 'd'.

> **Floating Point**:(float) refers to real number value or decimal values, eg. 3.147 .

> **Double**:(double): used when range exceeds both that of int and float, more accurate.

#### Data type Modifiers

Data modifiers are keyword prefixed to the beggining of a c data type, these modifiers are capable of changing amout of memory to be allocated to the variable.

- long
- short
- signed
- unsigned
  also help make the memory required by the primary data types more precise.

| **Data Type** | **Format Specifier** | **Minimal Range**                                      | **Size in bit** |
| ------------- | -------------------- | ------------------------------------------------------ | --------------- |
| char          | %c                   | -127 to 127                                            | 8               |
| int           | %d %i                | -32,767 to 32,767                                      | 16 or 32        |
| float         | %f                   | 1E-37 to 1E+37 along with six digits of the precisions | 32              |
| double        | %lf                  | 1E-37 to 1E+37 along with six digits of the precisions | 64              |

[Link to a more elaburate table](https://www.naukri.com/learning/articles/data-types-in-c-programming-with-examples/)

### Understanding Data Type In C In Terms Of Memory

A data type is reserving a chunk of memory to store and represent a value. A single byte consists of 8 bits of memory. Consider the below representation of byte, here each bit is represented by an underscore (\_):

**byte:** \_ \_ \_ \_ \_ \_ \_ \_ < – 8 bits

Since we have 8 positions where we can input either a 0 or 1. So we can have a combination of 2^8 or 256 distinct values which can be represented from the 8 bits and that is the overall range of a byte.

**byte:** 0 0 0 0 0 0 0 0  <- Represents  “0”

**byte:** 0 0 0 0 0 0 0 1  <- Represents  “1”

.. so on ..

**byte:** 1 1 1 1 1 1 1 0  <- Represents  “254”

**byte:** 1 1 1 1 1 1 1 1  <- Represents  “255”

Similarly, we have,

**int:**  \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_   < – 16 bits

**long:** \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_  < – 32 bits

> [!warning]
> A segmentation fault occurs when your program tries to access an area of memory that is not allowed. In other words, this error is thrown when the program tries to access memory that is beyond the allocated space for the specific data type.

### Derived Data types

Derived data types are derived for fundamental data types (primary data types), A derived data type wont create an new data type, but would and new/various functionality to the existing ones.

Types of Devired Data Types

- arrays
- pointers
- structures
- Unions

#### Arrays

an array is a collection of multiple values of similar data types and stored in a contiguos memory location.

#### Pointers

A pointer data type is used to store the address of another data type, Pointer allow users to perform dynamic memory allocation. also help pass variables by reference.

as you know any variable is assigned to a memory location and that memory location can be accessed by the ampersand ( & ) operator.

```c
#include <stdio.h>

int main () {

   int  var1;
   char var2[10];

   printf("Address of var1 variable: %x\n", &var1  );
   printf("Address of var2 variable: %x\n", &var2  );

   return 0;
}
```

> simiply put a **_pointer_** is a variable whose value is the address of another variable.

```c
type *var-name;

int    *ip;    /* pointer to an integer */
double *dp;    /* pointer to a double */
float  *fp;    /* pointer to a float */
char   *ch     /* pointer to a character */

// how to use a pointer

#include <stdio.h>

int main () {

   int  var = 20;   /* actual variable declaration */
   int  *ip;        /* pointer variable declaration */

   ip = &var;  /* store address of var in pointer variable*/

   printf("Address of var variable: %x\n", &var  );

   /* address stored in pointer variable */
   printf("Address stored in ip variable: %x\n", ip );

   /* access the value using the pointer */
   printf("Value of *ip variable: %d\n", *ip );

   return 0;
}
```

#### Structure

A structure is a data type that can be used to store variables of similar or different types, the size of the struct is the total size of the variables within it.

#### Union

A union consist of something added

https://www.geeksforgeeks.org/data-types-in-c/

> [!INFO] > **Const** key word is used when you dont want an initaited variable to be reassigned again.
>
> ```c
> const float PI = 3.147
> ```
