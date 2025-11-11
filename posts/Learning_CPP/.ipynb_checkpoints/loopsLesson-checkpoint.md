---
title: "Learning CPP As Pythonista: Day-3 "
author: "Ismail TG"
date: "10/29/2025"
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
# Loops in C++:
Today I learned how to repeat actions in C++ using `while` and `for` loops. I practiced printing values multiple times, validating input, summing ranges of numbers, using nested loops for a multiplication table, and even modifying characters inside a string. I also learned how break and continue can control the flow inside a loop.

## Repeating Output with a while Loop

**Concept**: Counter-controlled loop

**What it does**: Asks the user how many times to print their name, then uses a while loop to repeat the output.

**Key learning**: Loop structure, incrementing counters.


```cpp
#include <iostream>
#include <string>
#include <cmath>
using namespace std;

int main()
{
  int numberOfIterations;
  string name;

  cout << "Enter number of times your name will be printed: " << endl;
  cin >> numberOfIterations;
  cout << "Enter your name: " << endl;
  cin >> name;

  int count = 1;
  while( count <= numberOfIterations)
  {
    cout << name << endl;
    count++;
  }    
  return 0;
}  
```

## Validating User Input in a Loop:

**Concept**: Boolean condition control (`bool` + `while`)

**What it does**: Keeps asking the user a `Yes/No` question until the correct answer is given.

**Key learning**: Using a **flag** (`doneYet`) to control loop exit, `if/else` inside a loop.


```cpp
#include <iostream>
#include <string>
#include <cmath>
using namespace std;

int main()
{
  bool doneYet = false;
  char answer;

  while(!doneYet)
  {
    cout << "Are you Ismail Taghouchti?:(Y or N)  " << endl;
    cin >> answer;
    
    if(answer == 'Y')
    {
      doneYet = true;
      cout << "access granted .." << endl;
    }
    else 
    {
      cout << "Only Ismail Taghouchti could access." << endl;
    }
    
  }    
  return 0;
}  
```

## Summing a Range of Numbers (Using while):

**Concepts**: Input validation + running total

**What it does**: Ensures the ending value is larger than the starting value, then calculates the sum step by step.

**Key learning**: Nested logic using validation loop followed by a counting accumulation loop.


```cpp
#include <iostream>
#include <string>
#include <cmath>
using namespace std;

int main()
{
  int startingV;
  int endingV;

  cout << "Enter in a starting value: ";
  cin >> startingV;

  cout << "Enter in an ending value:  ";
  cin >> endingV;


  while( endingV <= startingV)
  {
    cout << "Please enter starting value larger then ending value. Try again." << endl;
    cout << "Enter in a starting value: ";
    cin  >> startingV;
    cout << "Enter in a ending value: " << endl;
    cin >> endingV;
  }
   
  int count = startingV + 1;
  int totalSum = startingV;
  while(count <= endingV)
  {
      cout << totalSum << " + " << count << " = ";
      totalSum = totalSum + count;
      cout << totalSum << endl;
      count ++;
  }
    
  cout << "The Sum of the numbers from " << startingV << " to " << endingV << " is ";
  cout << totalSum << endl;
     
  return 0;
}
```

## Multiplication Table (Nested Loops):

**Concept**: Nested `while` loops

**What it does**: Prints a full `9×9` multiplication table by looping rows and columns.

**Key learning**: Inner loop runs completely for each iteration of the outer loop.


```cpp
#include <iostream>
#include <string>
#include <cmath>
using namespace std;


int main()
{
  int left;
  int right;
  int product;
  

  left = 1;
  while(left < 10)
  {
    right = 1;
    while(right < 10)
    {
      product = left * right;
      cout << left << "X" << right << "=" << product << " ";
      right++;
    }
    left++;
    cout << endl;
  }

  return 0;
}
```

## Summing a Range of Numbers (Using for)

**Concept**: Comparison between `for` and `while` for counting

**What it does**: Same task as Program 3, but uses a for loop for cleaner counting logic.

**Key learning**: When iteration count is known → for loop is simpler and more readable.


```cpp
#include <iostream>
#include <string>
using namespace std;

int main()
{
  int startingV;
  int endingV;
  int sum;
  
  cout << "Enter in starting value: " << endl;
  cin >> startingV;

  cout << "Enter in endingV: " << endl;
  cin >> endingV;

  while(endingV < startingV)
  {
    cout << "The ending value must be greater than or equal to starting value " << startingV << "Please enter again: "<< endl;
    cin >> startingV;

    cout << "Enter in ending value: " << endl;
    cin  >> endingV;

  }    
  sum = startingV;
  for(int count = startingV + 1; count <= endingV; count++)
  {
    cout << sum << " + " << count << " = ";
    sum = sum + count;
    cout << sum << endl;
  
  }
  cout << "the summation of numbers from " << startingV << " to " << endingV << " is: " << sum << endl;
  return 0;
}
```

## Capitalizing Letters After Spaces:

**Concept**: Looping through strings using indexes

**What it does**: Detects spaces and capitalizes the next character.

**Key learning**: Boolean flag (`isCap`) + character processing using `toupper()`.


```cpp
#include <iostream>
#include <string>
using namespace std;

int main()
{
  string sentence = "Today was a bit sunny.";
  cout << sentence << endl;
  cout << "After Capitalization: " << endl;
  bool isCap = false;
  for(int i=0; i < sentence.length(); i++)
  {
    if(isCap)
    {
      char capL = toupper(sentence[i]);
      cout << capL;
      isCap = false;
    }
    else 
    {
      cout << sentence[i];
    }
    if(sentence[i] == ' ')
    {
      isCap = true;
    }
      
  }
  cout << endl;
  return 0;
}
```

## Using break and continue:

**Concept**: Loop flow control

**What it does**: Loops from 0 to 49 but:

`continue` skips printing even numbers

`break` stops when `i == 21`

**Key learning**: Adjusting loop behavior without changing loop condition.


```cpp
#include <iostream>
#include <string>
using namespace std;


int main()
{
  for(int i = 0; i < 50; i++)
  {
    if(i == 21)
    {
      break;
    }  

    if(i % 2 == 0)
    {
      continue;
    }  
    cout << i << endl;
  }  
  return 0;  
}
```

# Summarization:
Loops make it possible to automate repetitive tasks and control how many times something runs. This session helped me understand both `while` and `for` loops, and when it makes sense to use each one.

Next time, I will continue building on this foundation by learning **`arrays`**.


```cpp

```
