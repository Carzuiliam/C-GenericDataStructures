# Generic Data Structures in C

This project shows an example of a data structure with generic types in C.

## Introduction

First of all: **there is no specific implementation for generic types in C**. Only after C++ the concept of generic type were born (using [templates or objects](https://web.eecs.utk.edu/~bvz/cs365/notes/generic-types.html)). But there are **2 (two) basic ways** to workaround with this:

 - The first way is using **empty pointers** (of `void*` type), requiring _downcast_ for the desired type before its manipulation. Thought this approach gives more power to the programmer (including to allow the use of functions as functions parameters -- an unthinkable thing on another programming languages), the _downcast_ is extremely unsafe, since the C language does not perform type checking during execution time.
 - The second way is using **shared memory structures**, built using the `union` structure combined with the `enum` structure (which contains the type index). This method is easier to depure than the pointer method, thought requires certain attention for the programmer to control the value contained by the variable.

## About This Project

The source codes presented here implements, using the `union`/`enum` approach previously described, an example of a single dynamic list.

Using the `union` type, it's possible to allocate multiple variables in a same memory block. For example, consider the following code lines from the file [Element.c](libraries/Element.c):

```C
typedef struct Element
{
    enum E_Type
    {
        E_CHAR,
        E_FLOAT,
        E_INTEGER,
        E_STRING
    }
    type;

    union E_Value
    {
        char c;
        float f;
        int i;
        char *s;
    }
    value;

    struct Element *next;
}
Element;
```

This code block defines that, inside an `Element`, there is a `union` type structure, which contains a `char`, a `float`, an `int`, and a string (`char*`), all of them sharing the same memory block. There is also a `enum` type structure, which contains the value type attached to the `Element`, and an `Element*` variable, utilized in the control of the `List` structure (defined in the [List.c](libraries/List.c) file).

Based on the previous structure, the following code block shows the creation of an `Element` struct of the `int` type:

```C
//  Creates a new element
Element *element = new_Element();

//  Attaches the values for the element
element->type = INTEGER;
element->value.i = 100;
element->next = NULL;
```

It's necessary to store the variable type because, when the command `element->value.i = _value` occurs, a value is not only attached to `i`, but also to `c`, `f`  and `*s`, since the memory block for `value` is shared. This behavior can be observed by, after the above code has been executed, all the following operations are valid from the compiler point-of-view -- even generating inconsistent data:

```C
//  Prints "value" as a char (probably the char corresponding
// to the value 100)
printf("%c", element->value.c);

//  Prints "value" as a float (probably wrong)
printf("%.2f", element->value.f);

//  Prints "value" as an int (the only correct)
printf("%i", element->value.i);

//  Prints "value" as a string (only garbage)
printf("%s", element->value.s);
```

However, if the variable type is available, it's possible to do

```C
switch (element->type)
    {
        case CHAR:
            printf("%c", element->value.c);
            break;

        case FLOAT:
            printf("%.2f", element->value.f);
            break;

        case INTEGER:
            printf("%i", element->value.i);
            break;

        case STRING:
            printf("%s", element->value.s);
            break;
    }
```

which guarantee that the data can be correctly treated.

## Additional Info

Since this is a work in progress, probably I will add more features in the future (e.g. stacks, sparse matrices and trees). Stay tuned!

## License

The available source codes here are under the Apache License, version 2.0 (see the attached `LICENSE` file for more details). Any questions can be submitted to my email: carloswilldecarvalho@outlook.com.