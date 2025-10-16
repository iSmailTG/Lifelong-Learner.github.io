---
title: "Learning CPP As Pythonista: Day-1 "
author: "Ismail TG"
date: "09/19/2025"
format:
  html:
    code-fold: true
    code-line-numbers: true
    code-tools: true
    highlight-style: github
    theme: cosmo
categories: [Kernel Engineering, Cuda, Pytorch, CPP]
image: "PythonVsCPP.png"
---
## Motivation:
- In this series of lessons, I will track my learning through the universe of C++ as someone who mainly program in Python.
- The motivation behind learning C++ in 2025 can be resumed into one word: **Cuda**.
- CUDA kernels are still C++ first, Python second. Learning the bare-metal steps so I can stop guessing and start writing fast GPU code. One small page a day until the compiler feels normal.
- This idea of learning C++ for CUDA make it less intimidating, since C++ isn't the goal here  rather just a tool for something else.
- But still I need to learn some basics of C++ in order to navigate its world that already after one studying session looks very strange and outlandish for a Python programmer.

## C++ World:
- C++ is a **Compiled language**, which means the code goes through a transformation process before it can run.
- In C++, we write code then a special program called **Compiler** translates our *Human-readable* code into machine code (1s & 0s) that computer can executes.
- This process of compiling works in **4 stages**:
  * Stage1: Pre-processing before the compiler even looks at our code, it's a text manipulation step where the preprocessor check code that starts with `#` and  copy-past, find-replace it automatically, so the next step include only pure C++ code.
  * Stage2: The compiler reads the C++ code and converts it to **Assembly Language**, which is a human readable instructions that are very close to machine code.
  * Stage3: An **Assembler** converts assembly into actual `1s` & `0s` that the CPU understands.
  * Stage4: A **Linker** connects everything together (code, libraries, other file..) and creates the final executable file that can run.


```cpp
#include <iostream>
int main()
{
    // print my name in three lines
    std::cout << "Hi, My" << std::endl;
    std::cout << "name is" << std::endl;
    std::cout << "Ismail." << std::endl;
    // in one line with three string
    std::cout << "Hi, my " << "name is " << "Ismail." <<std::endl;
    // print the whole expression in one string
    std::cout << "Hi, my name is Ismail.";
    
    return 0;
}
```


```cpp
main()
```

    Hi, My
    name is
    Ismail.
    Hi, my name is Ismail.
    Hi, my name is Ismail.




    0



### Importing in CPP:

* In order to use libraries and tools in C++ we use the keyword **`#include`**, it's equivalent to `import` in Python.
* Library we used here is `iostream` which allows us to print in the screen and many more things.

### Main() function:

* **All C++ programs** has one `main()` function, it's the starting point of every program, The `main` function is called automatically when a C++ program begins.

#### Printing:

* By using **`cout`** we can print text on the screen.
* More than one piece of data can be printed at a time.
* **`endl`** is used after **`cout`** to end the line, so the next **`cout`** could starts printing next line.
* We can print multiple pieces of data if we **seperate** them with **`<<`**.

#### Statements:
* The lines of code inside `main` function are called `statements`.
* All statements in C++ must end with semi-colon **;**.
* **The compiler** reads our code and turns it into instructions that a computer can understand (machine instructions).
* Then the compiler will generate an executable program which is just a file filled with machine instructions.

    - The compiler uses the semi-colons to know where each statement ends. If you do not include a semi-colon at the end of every statement then you will get a `syntax error`. A program with syntax errors will not compile and run.
#### String:
* The thing printed to the screen is called string, which refers to a groupr of characters inside `""`.
#### Comments:
* As in python comments are non executable code that we put beside the code in order to explain for us or others ideas or concepts in the code.
* In C++ the comment starts with //.
* There are two types of comments:  
       - single line comments and multi-line comments. All code in between a /* and  a */ are called multi-line comments. They will also be ignored by the compiler. When you have a lot to say in a comment you will use these

### Variables:
* We will write a program that will find the distance between two points on a graph. It will need variables to hold the points (an X and a Y value for each point) and a variable to hold the length of the line that connects  the points.


* Since C++ is a static typing language, we need to **declare** the type of the variable while creating it, and this variable will only accept the type is assigned to.
* Here we declare a variable `var` with type `int` which refers to integer or whole number.



```cpp
int var = 5;
std::cout << "variable of type integer: " << var << std::endl;

```

    variable of type integer: 5


* A variable is place where we store data in the memory that can be read or written to in a program.
* We think of variables as boxes that hold data, and each box is distinct in the memory. So every variable lives in a location in memory, and we can retrieve it by name without carrying about it's location in memory.



```cpp
void func()
{
    // declare variables
    int var0;
    int var1;

    int var2;
    int var3;

    // assign values to each variable
    var0 = 1;
    var1 = 1;
    var2 = 2;
    var3 = 2;

    // print the variables value with a message on the screen
    std::cout <<"variable 1: " <<var0<< std::endl;
    std::cout <<"variable 2: " <<var1<< std::endl;
    std::cout <<"variable 3: " <<var2<< std::endl;
    std::cout <<"variable 4: " <<var3<< std::endl;
}
func();
```

    variable 1: 1
    variable 2: 1
    variable 3: 2
    variable 4: 2


* Here I declared 4 variables of type int, at this point the program requires a box in the memory for each variable.
* Then when I assign each variable to a value, the program will put those values in their correspondent box.
* Now we have what represents the points in a graph: `var0` and `var1` for point 1 & `var2` and `var3` for point 2, now we need variable that will hold the value of the distance. The type of that variable must be float number, since it can be decimal.


```cpp
void func2()
{
    int var0;
    int var1;
    int var2;
    int var3;
    var0 = 1;
    var1 = 1;
    var2 = 2;
    var3 = 2;

    // declare a float type variable

    float lengthOfline;
}
```

* the variable `lengthOfline` doesn't have value yet, so need to calculate the distance:
  $d = \sqrt{(x_2 - x_1)^2 + (y_2 - y_1)^2}$
* So we need power and square root. we could multiply the number by itself to obtain the power but the square root need to be hard coded, but luckily we can just import it from **math** module in C++, which we need to import in order to use the built in function **sqrt**.


```cpp
#include <cmath>
void func3()
{ 
    //declare the variables to hold points 
    int x1; 
    int y1; 
 
    int x2; 
    int y2; 
 
    //fill the point variables with data 
    x1 = 2; 
    y1 = 2; 
 
    x2 = 2; 
    y2 = 4; 
 
    //declare a variable to hold the length of a line between two points 
    float lengthOfLine; 
 
    //use the distance formula to find the distance 
    lengthOfLine = sqrt(((x2 - x1) * (x2 - x1)) + ((y2 - y1) * (y2 - y1))); 
 
    //print the length of the line 
    std::cout<<"The length of the line is: "<<lengthOfLine<<std::endl; 
  
}
func3();
```

    The length of the line is: 2


### Data types:
* In C++ data types are defined once and cannot be changed. If a variable is declared with a specific data type, it can only hold values that suits that data type.
* We already used **`int`** which is a whole number, **`float`** a decimal. There are many other types:


```cpp
#include <string>
using namespace std;

```


```cpp
void basicdtypes()
{
    int num = 5;
    float dec = 5.22;
    char oneChar = 'K';
    string name = "Ismail";

    cout <<"this is a whole number: " << num << endl;
    cout <<"this is a decimal number: " <<dec<< endl;
    cout << "this is single character: " <<oneChar<< endl;
    cout << "This is a string of charaters: " <<name<< endl;
}
basicdtypes();
```

    this is a whole number: 5
    this is a decimal number: 5.22
    this is single character: K
    This is a string of charaters: Ismail


* `char` is a single character between single quotes.
* `string` is couple of characters between double quotes, and it's not primitive type, it must be imported `#include <string>`

### Numbers types:
* Till now we saw 2 numerical data types: `int` and `float`.
* A data type defines a few things:
    - the operations that can be performed on variables of that type
    - the values that can be stored inside a variable of that type (there is usually a range of acceptable values)
    - the amount of space a variable takes up in memory
* The operations performed on `int` are: **` - + / * `**, the division is the only operation that requires some explanation.
* Here is a division between to integers: 


```cpp
void divint()
{
    int a = 10;
    int b = 5;
    int res = a / b;
    cout <<"result a / b => " <<res<< endl;
}    
divint();
```

    result a / b => 2


* What if we want to divide 2 integers that will causes not a whole number:


```cpp
void dvd()
{
    int num = 12;
    int num1 = 2;
    int res = num / num1;
    cout <<"result as num = 12: " <<res<< endl;
    num = 13;
    res = num / num1;
    cout <<"result as num = 13: " <<res<< endl;
}
dvd();
```

    result as num = 12: 6
    result as num = 13: 6


* At first, the result was 6 which is the obvious answer, but when we predicted `6.5` we still get `6`?
* The reason is that integer division returns the quotient which is always an integer. This is true of all integer divides using the / operator

* There is another integer division operator called the **modulus operator** that returns the remainder. The mod operator is the **`%`** symbol.


```cpp
void divmod()
{
     
    int num1 = 12; 
    int num2 = 2; 
    int result; 
 
    result = num1 / num2; 
    cout << "result with / operator: " << result << endl;     
 
    result = num1 % num2; 
    cout << " result with % operator: " << result << endl;
    
}

divmod();
```

    result with / operator: 6
     result with % operator: 0


* A data type also specifies an acceptable range of values.
* An int variable cannot hold an infinite sized number, there is a limit.

* There is a built in constant called **`INT_MAX`** that holds the largest value that can be stored in any int variable (there is an **`INT_MIN`** too).


```cpp
#include <climits>
```


```cpp
int largeNumber;
largeNumber = INT_MAX;
cout<<"largest number can be stored: " <<largeNumber<< endl;
```

    largest number can be stored: 2147483647


* Adding one number to that value will causes an out of range error
* The arithmetic operators for floats are the same as ints except there is no mod operator. float division results in numbers with fractional parts.
* In case we want to declare a float data type but the value is a whole number we need to explicitly tell the compiler it's a float number by adding `.0`:


```cpp
#include <typeinfo>
void wholeFloat()
{
    int num1 = 15;
    float num2 = 15.0;
    cout << "the type of num1 = 15 :  " << typeid(num1).name() << endl;
    cout << "the type of num2 = 15.0 :  " << typeid(num2).name() << endl;
    
    
}

wholeFloat();
```

    the type of num1 = 15 :  i
    the type of num2 = 15.0 :  f


* We get `i` for integer for `15` and `f` float for `15.0`.

### Characters and strings
* **`string`** is very strong type which allows us to make many different things and manipulates data in many ways.
* While **`char`* is a bit hard to work with, since it's limited and not as versatile as `string`.


```cpp
void chars()
{
    
    char first = 'c';
    char second = 'h';
    char third = 'a';
    char fourth = 'r';

    string word = "String";
    
    cout << first << second << third << fourth << endl;
    cout << word << endl;

}
```


```cpp
chars();
```

    char
    String


#### String Operations:
* String tyoe has many operation that we could use to manipulate data, like **length()** which as the name suggests tells us the length of string:


```cpp
string name = "Ismail";
cout << "Number of characters in Ismail is: " << name.length() << endl;
```

    Number of characters in Ismail is: 6


* The square brackets is another operator that will return an individual character in the string:


```cpp
string word = "word";
char letter  = word[0];
cout << "this is a string: " << word << endl;
// the index is 0 based
cout << "this is the first character of that word: " << letter << endl;
```

    this is a string: word
    this is the first character of that word: w


* The **+** operator concatenates strings together.


```cpp
string sentence = "word1";
sentence = sentence + " word2" + " word3" + " ...";
cout << sentence << endl;
```

    word1 word2 word3 ...


* **substr** is a string function that will create a new string from the innards of another.
* This function takes a starting position and the number of character to use after that position and copies the characters into a new string.


* **`find`** will return the position where the first occurrence of a word is found (and -1 if it is not found).



```cpp
string sentence = "where is Aldo ?";
int findAldo = sentence.find("Aldo");
cout << "Aldo can be found at this position: " << findAldo << endl;
```

    Aldo can be found at this position: 9


* **`erase`** will remove characters from a string. It takes the starting position of where I want to start removing characters and he total number of characters to get rid of.



```cpp
void func()
{
    string sentence = "In this sentence sentence, it must be no repeated word!";
    int repeatedWord = sentence.find("sentence");
    sentence.erase(repeatedWord, 9);
    cout << sentence << endl;
}
```


```cpp
func();
```

    In this sentence, it must be no repeated word!


* **`insert`** adds a new string after the position specified in the string.



```cpp
string sentence = ", my name is Ismail.";
string greeting = "Hi";
sentence = sentence.insert(0, greeting);
cout << sentence << endl;
```

    Hi, my name is Ismail.



```cpp

```
