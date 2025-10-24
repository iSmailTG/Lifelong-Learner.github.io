---
title: "Learning CPP As Pythonista: Day-2 "
author: "Ismail TG"
date: "10/24/2025"
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
## Even || Odd
- `%` gives the remainder of division, perfect for checking parity.
- A boolean variable like `iseven` stores true/false logic.
- `if/else` chooses which message to display.



```cpp
#include <iostream>
#include <string>
#include <cmath>
using namespace std;


int main()
{
  int number;
  cout << "Please enter a Number: " << endl;
  cin >> number;

  bool iseven = number % 2 == 0;
  if(iseven)
  {
    cout << number << " is an even number" << endl;
  }
  else
  {
    cout << number << " is an odd number" << endl;
  }

  return 0;
}
```

## Weekly Payout:
- Simple payroll logic with overtime calculation.
- Hours above 40 are paid at *1.5x* the regular rate.
- Read multiple inputs from the user and compute the result.



```cpp
#include <iostream>
#include <string>
#include <cmath>
using namespace std;

int main()
{
  float hourPerWeek;
  float payPerHour;
  string name;
  float weekPayout;

  cout << "How many Hours did you work last week: " << endl;
  cin >> hourPerWeek;
  cout << "How much you earn per hour: " << endl;
  cin >> payPerHour;
  cout << "What's your name: " << endl;
  cin >> name;

  if(hourPerWeek <= 40.0)
  {
    weekPayout = hourPerWeek * payPerHour;
    cout << name << " earned " << weekPayout << "$$." << endl;
  }
  else:
  {
    weekPayout = ((payPerHour * 40.0) + ((hourPerWeek - 40) * (payPerHour * 1.5)));
    cout << name << "  earned " << weekPayout << "$$." << endl;
  }
  return 0;
}

```

## Water Temperature:
- Nested `if` statements help handle several rules inside one category.
- Logical OR `||` checks if the scale is either C or F.
- Different boiling/freezing points for Celsius vs Fahrenheit.
- Provide feedback for invalid input.



```cpp
#include <iostream>
#include <string>
#include <cmath>
using namespace std;

int main()
{
  float temp;
  char tempScale;
  cout << "Enter in the temperature: " << endl;
  cin >> temp;
  cout << "Enter temperature scale ( C or F ): " << endl;
  cin >> tempScale;

  if( tempScale == 'C' || tempScale == 'F')
  {
    if(tempScale == 'F')
    {
      if(temp <= 32.0)
      {
        cout << "Water will turn to ice at "<<temp<< " F degrees." << endl;
        
      }
      else if(temp >= 212.0)
      {
        cout << "Water will turn to steam at " <<temp<< " F degrees." << endl;

      }
      else
      {
        cout << "Water will stay liquid at " << temp<< " F degrees." << endl;
      }
    
    }
    else
    {
      if(temp <= 0.0)
      {
        cout << "Water will turn to ice at " << temp << " C degrees." << endl;
      }
      else if(temp >= 100.0)
      {
        cout << "Water will turn to steam at " << temp << "C degrees." << endl;
      }
      else
      {
        cout << "water will stay liquid at " << temp << "C degrees." << endl;
      }
    }
  }
  else 
  {
    cout << "You entered an invalid temperature scale. Please run the program again." <<endl;
  }
  return 0;
}
```

## Number of the Day (If / else if):
- Chain of `else if` to match a specific number with its weekday.
- Demonstrates grouping conditions like weekend vs weekday.
- A good example where many comparisons might get messy.



```cpp

#include <iostream>
#include <string>
#include <cmath>
using namespace std;

// slightly different version of switch.cpp
// using || and && and adding weekend 


int main()
{
  int dayNum;
  cout << "Enter a number day (1 Sunday, 2 Monday, 3 Tuesday .. etc" << endl;
  cin >> dayNum;
  cout << "You selected ";
  
  if(dayNum == 1)
  {
    cout << "Sunday. ";

  }
  else if(dayNum == 2)
  {
    cout << "Monday. ";
  }
  else if(dayNum == 3)
  {
    cout << "Tuesday. ";
  }
  else if(dayNum == 4)
  {
    cout << "Wednesday. ";
  }
  else if(dayNum == 5)
  {
    cout << "Thursday. ";
  }
  else if(dayNum == 6)
  {
    cout << "Friday. ";
  }
  else if(dayNum == 6)
  {
    cout << "Saturday. ";
  }
  else
    cout << "Please enter a valid day. ";


  if(dayNum == 1 || dayNum == 7)
  {
    cout << "It's a weekend.";
  }
  else if(dayNum >=2 && dayNum <= 6)
  {
    cout << "It's a weekday." << endl;
  }
  return 0;
}

```

## Number of the Day (switch):
- `switch` is a cleaner choice for fixed numeric options.
- Each `case` handles one specific value.
- `break` prevents “falling through” to the next case.
- `default` is like the `else` of switch statements.



```cpp
#include <iostream>
#include <string>
#include <cmath>
using namespace std;
// same as what I did in switch.cpp but now I will use swith instead of if statements.

int main()
{
  int dayNumber;
  cout << "Enter a number day ( 1 for Sunday, 2 Monday, 3 Tuesday ...etc)" << endl;
  cin >> dayNumber;
  

  switch(dayNumber)
  {
    case 1:
      cout << "Sunday. " << endl;
      break;
    case 2:
      cout << "Monday. " << endl;
      break;
    case 3:
      cout << "Tuesday. " << endl;
      break;
    case 4:
      cout << "Wednesday. " << endl;
      break;
    case 5:
      cout << "Thursday. " << endl;
      break;
    case 6:
      cout << "Friday. " << endl;
      break;
    case 7:
      cout << "Saturday. " << endl;
      break;
    default:
      cout << "an invalid day. " << endl;
  }
  return 0;

}
```

## Day 2 Summarization :

Here is what I achieved:

* I learned the **`if` statement**, the first building block of logical decision-making.
* I used **comparison operators** like `==`, `<=`, and `>=` to evaluate conditions.
* I combined conditions using logical operators such as `||` and `&&`.
* I controlled program behavior with **`if` / `else` / `else if`** chains for multiple outcomes.
* I explored **`switch`** as a cleaner option for specific numeric cases.
* I wrote several mini programs with real-world logic:

  * Even or odd number checker
  * Weekly pay calculator with overtime
  * Water state detector (ice, liquid, steam)
  * Day-of-the-week selector (two versions)
