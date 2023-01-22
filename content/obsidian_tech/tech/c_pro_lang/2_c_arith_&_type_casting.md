valid arithimetic operators in c consists of.

```c
// +  (addition)
// -  (subtration)
// *  (multiplication)
// /  (division)
// %  (modulus)
// ++ (increment)
// -- (decrement)
```


### Data type casting 

When performing an arithimetic operation, the result gets influenced by the data type selected of the calculation. 

```c
int x = 5;
int y = 2;
int z ;

z = 5 / 2
// in this case z would be equals to 2.
// this is because int dont consider the decimal portion.

// to get the accurate result in this scenario.

float z ;

z = x / (float)y;

// this would yeild the accurate result, 2.5

```

### Argumented Assignment Operator

```c
x = x + 1;

// this is equivalent

x += 1

// this holds true for all arithimetic operators.
```


### Bold header
