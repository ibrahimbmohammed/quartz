Static memory allocation 

this is memory allocated during compile time, the memory allocated is fixed and cant be increased or decreaded during run time.

```c
int arr[5] = {1,2,3,4,5};
```

  > [!warning]
 > with the example above user can not increase or decrease size of the arrat at run time.
 > 
 > Thus if the value at runtime being used is less than the array size ,then we have a waste of memory.
 >  if it happens to be more, then an error would occur.
 > 
 
 
 Dynamic Memory Allocation

The process of allocating memory at the time of execution is called dynamic memory allocation

Heap is the segment of memory where dynamic memory allocation takes place,

heap memory is allocated and dealloated randomly, 
stack however in a defined order

#### Built in function for dynamic memory allocation

malloc()
calloc()
realloc()
free( )


```c
#include<stdio.h>

int total;

int square(int x)
{
  return x * x;
}

int SquareOfSum(int x, int y)
{
 int z =Square(x+y);
 return z;
}

int main()
{
int a = 4. b = 8; 
total = SquareOfSum(a, b);
printf("output = %d", total);

}

```

![[Pasted image 20221225174549.png]]

[[c_memory_layout]]

```c
#include <stdio.h>
#include <stdlib.h>

int main 
{
int a;
int *p;
p = (int*) malloc(sizeof(int));
*p = 10;

/// beware of not deallocating
p = (int*) malloc(sizeof(int));
*p = 20;
// this goes to the heap and assigns to a new block of memory a new value, ignoring the previous value.

!note

free(p);

// free up the memory before reassigning.

}
```


```c

```
