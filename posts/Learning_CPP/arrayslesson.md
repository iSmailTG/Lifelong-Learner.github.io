---
title: "Learning CPP As Pythonista: Day-4 "
author: "Ismail TG"
date: "11/11/2025"
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
## Introduction:
After learning how to control program flow with loops, I moved on to arrays which is a key concept that brings structure to repetitive data. Instead of creating separate variables for each value, arrays let me group them together and access each element using an index. This chapter helped me see how loops and arrays work hand in hand to process multiple pieces of data efficiently.

### Basics of Arrays:
* Learned how to **declare** and **initialize** arrays of both strings and integers.

* Practiced indexing elements to store and access data.

* Used a for loop to iterate over array elements.

* Combined two arrays (**`arr`** and **`yearOfBirth`**) using the same index to relate data.

* Reinforced using input/output (**`cin`**, **`cout`**) with arrays.

* Saw how arrays can replace multiple separate variables efficiently.


```cpp
#include <iostream>                                                                
#include <string>                                                                  
using namespace std;                                                               
                                                                                   
int main()                                                                         
{                                                                                  
  string arr[4];                                                                   
  arr[0] = "Ismail";                                                               
  arr[1] = "Sarah";                                                                
  arr[2] = "Sifao";                                                                
  arr[3] = "Yuba";                                                                 
                                                                                   
  int yearOfBirth[] = {1989, 2002, 2028, 2028};                                    
  int currentYear;                                                                 
  cout << "Enter in the current year: " << endl;                                   
  cin >> currentYear;                                                              
                                                                                   
  for(int i = 0; i < 4; i++)                                                       
  {                                                                                
    int age = currentYear - yearOfBirth[i];                                        
    cout << arr[i] << " born " << yearOfBirth[i] << " age: " << age << endl;       
  }                                                                                
  return 0;                                                                        
}                                                                                  

```

### Basics of Arrays 2:
* Introduced the idea of nested loops to compare array elements.

* Used an inner loop to calculate relationships between all pairs (differences in ages).

* Learned how to avoid comparing an element to itself using an if **`(j != i)`** condition.

* Practiced loop logic with multiple arrays in a single program.

* Understood how nested loops quickly increase the number of operations **`(n²)`**.

* Strengthened the mental model of outer vs. inner loop roles.


```cpp
#include <iostream>                                                              
#include <string>                                                                
using namespace std;                                                             
                                                                                 
                                                                                 
int main()                                                                       
{                                                                                
  string names[4];                                                               
  names[0] = "Ismail";                                                           
  names[1] = "Sarah";                                                            
  names[2] = "Sifao";                                                            
  names[3] = "Yuba";                                                             
                                                                                 
  int yearOfBirth[] = {1989, 2002, 2028, 2028};                                  
  int currenYear;                                                                
  cout << "Enter in the current year: " << endl;                                 
  cin >> currenYear;                                                             
                                                                                 
  for(int i = 0; i < 4; i++)                                                     
  {                                                                              
                                                                                 
    cout << "Difference in ages: " << names[i] << " : ";                         
    for(int j = 0; j < 4; j++)                                                   
    {                                                                            
                                                                                 
      if(j != i)                                                                 
      {                                                                          
        cout << names[j] << " " << yearOfBirth[j] - yearOfBirth[i] << " ";       
      }                                                                          
    }                                                                            
                                                                                 
    cout << endl;                                                                
  }                                                                              
                                                                                 
  return 0;                                                                      
}                                                                                

```

### Arrays with Inputs:
* Learned to fill arrays dynamically based on user input rather than predefined data.

* Used a counter (**`numberOfElements`**) to track how many entries the user provided.

* Practiced calculating sum and average using array values.

* Got introduced to variance and standard deviation, reinforcing mathematical processing with arrays.

* Understood the importance of loop limits and correct variable initialization.

* Reinforced using break statements and conditional user responses to stop input early.


```cpp
#include <iostream>                                                             
#include <string>                                                               
#include <cmath>                                                                
using namespace std;                                                            
                                                                                
int main()                                                                      
{                                                                               
  int nums[20];                                                                 
  int num;                                                                      
  int sum = 0;                                                                  
  int numberOfElements;                                                         
  char response;                                                                
  for(int i = 0; i < 20; i++)                                                   
  {                                                                             
    cout << "enter in a number: " << endl;                                      
    cin >> num;                                                                 
                                                                                
    nums[i] = num;                                                              
    numberOfElements++;                                                         
                                                                                
    if(i < 19)                                                                  
    {                                                                           
      cout << "Would you like to add another number? (Y)es Or (N)o " << endl;   
      cin >> response;                                                          
      if( response != 'Y')                                                      
      {                                                                         
        break;                                                                  
      }                                                                         
    }                                                                           
    sum = sum + nums[i];                                                        
    cout << sum << " " << nums[i] << endl;                                      
  }                                                                             
  float avg = float(sum) / float(numberOfElements);                             
  float sumSDV = 0.0;                                                           
  cout << "The average is: " << avg << endl;                                    
  float diff;                                                                   
                                                                                
  for(int i=0; i<=numberOfElements; i++)                                        
  {                                                                             
    diff = nums[i] - avg;                                                       
    sumSDV = sumSDV + (diff * diff);                                            
                                                                                
  }                                                                             
  float variance = float(diff) / float(numberOfElements);                       
  float standardDV = sqrt(variance);                                            
                                                                                
  cout << "The variance is " << variance << endl;                               
  cout << "The standard deviation is " << standardDV << endl;                   
  return 0;                                                                     
}                                                                               

```

### Coin Flip:
* Practiced creating a **`2D array`** to simulate a grid of coin flips.

* Used **`rand()`** and `**srand(time(0))`** to generate random outcomes.

* Reinforced nested loop iteration for 2D data structures (rows and columns).

* Represented outcomes (**`H`** or **`T`**) with characters instead of numbers.

* Displayed a 2D grid output neatly using nested cout loops.

* Built intuition for how 2D arrays represent tables, matrices, or grids.


```cpp
#include <iostream>                                  
#include <string>                                    
#include <ctime>                                     
using namespace std;                                 
                                                     
                                                     
int main()                                           
{                                                    
  char coinFlip[10][10];                             
  srand(time(0));                                    
                                                     
  for(int row = 0; row < 10; row++)                  
  {                                                  
    for(int col = 0; col < 10; col++)                
    {                                                
      if(rand() % 2 == 0)                            
      {                                              
        coinFlip[row][col] = 'H';                    
      }                                              
      else                                           
      {                                              
        coinFlip[row][col] = 'T';                    
      }                                              
    }                                                
  }                                                  
  for(int row = 0; row < 10; row++)                  
  {                                                  
    for(int col = 0; col < 10; col++)                
    {                                                
      cout << coinFlip[row][col] << " ";             
    }                                                
    cout << endl;                                    
  }                                                  
                                                     
  return 0;                                          
}                                                    

```

### Coin Flip 2:
* Improved the previous coin flip simulation with constants for clearer structure.

* Learned to calculate row-based statistics (percent of heads and tails).

* Practiced using floating-point division and type casting for accuracy.

* Reinforced using **`iomanip`** and **`setprecision()`** for formatted output.

* Understood how to store computed values in a separate array (**`percentageOfHeadsInARow`**).

* Built intuition for how arrays can represent probability distributions or statistics.


```cpp
#include <iostream>                                                                       
#include <string>                                                                         
#include <ctime>                                                                          
#include <iomanip>                                                                        
using namespace std;                                                                      
                                                                                          
                                                                                          
int main()                                                                                
{                                                                                         
  const int NUM_OF_ROWS = 10;                                                             
  const int NUM_OF_FLIP_PER_ROW = 15;                                                     
                                                                                          
                                                                                          
  char coinFlip[NUM_OF_ROWS][NUM_OF_FLIP_PER_ROW];                                        
                                                                                          
  srand(time(0));                                                                         
  float  sumOfRowPercentages = 0.0;                                                       
  for(int row = 0; row < NUM_OF_ROWS; row++)                                              
  {                                                                                       
    cout << fixed << setprecision(2);                                                     
                                                                                          
    float percentageOfHeadsInARow[NUM_OF_ROWS];                                           
    float sumOfRowPercentages = 0.0;                                                      
    int numOfHeadsPerRow = 0;                                                             
    for(int col = 0; col < NUM_OF_FLIP_PER_ROW; col++)                                    
    {                                                                                     
      if(rand() % 2 == 0)                                                                 
      {                                                                                   
        coinFlip[row][col] = 'H';                                                         
        numOfHeadsPerRow++;                                                               
      }                                                                                   
      else                                                                                
      {                                                                                   
        coinFlip[row][col] = 'T';                                                         
      }                                                                                   
      cout << coinFlip[row][col] << " ";                                                  
                                                                                          
    }                                                                                     
                                                                                          
    percentageOfHeadsInARow[row] = (float)numOfHeadsPerRow / (float)NUM_OF_FLIP_PER_ROW;  
    //sumOfRowPercentages += percentageOfHeadsInARow[row];                                
                                                                                          
                                                                                          
    cout << " | Heads: " << percentageOfHeadsInARow[row] * 100 << "%.";                   
    cout << " | Tails: " << ((1 - percentageOfHeadsInARow[row]) * 100) << "%.";           
    //cout << sumOfRowPercentages;                                                        
    cout << endl;                                                                         
  }                                                                                       
                                                                                          
  return 0;                                                                               
}                                                                                         

```

### 3D Array:
* Introduced **`multi-dimensional arrays (3D)`** for complex data storage.

* Modeled real-world hierarchical data: **`years → weeks → days`**.

* Practiced deep nested loops to initialize and process multi-level data.

* Reinforced using input validation with range checks for each dimension.

* Learned to calculate aggregates (e.g., total sales per week, per day, across years).

* Understood how 3D arrays extend the same principles from 1D/2D to represent structured data.


```cpp
#include <iostream>                                                                                                                                                                     
#include <string>                                                                                                                                                                       
using namespace std;                                                                                                                                                                    
                                                                                                                                                                                        
int main()                                                                                                                                                                              
{                                                                                                                                                                                       
  const int NUM_YEARS_IN_SALES = 10;                                                                                                                                                    
  const int NUM_WEEKS_IN_YEAR = 52;                                                                                                                                                     
  const int NUM_DAYS_IN_WEEKS = 7;                                                                                                                                                      
                                                                                                                                                                                        
  int numCarsSold[NUM_YEARS_IN_SALES][NUM_WEEKS_IN_YEAR][NUM_DAYS_IN_WEEKS];                                                                                                            
                                                                                                                                                                                        
  for(int year = 0; year < NUM_YEARS_IN_SALES; year++)                                                                                                                                  
  {                                                                                                                                                                                     
    for(int week = 0; week < NUM_WEEKS_IN_YEAR; week++)                                                                                                                                 
    {                                                                                                                                                                                   
      for(int day = 0; day < NUM_DAYS_IN_WEEKS; day++)                                                                                                                                  
      {                                                                                                                                                                                 
        numCarsSold[year][week][day] = 0;                                                                                                                                               
      }                                                                                                                                                                                 
    }                                                                                                                                                                                   
  }                                                                                                                                                                                     
  cout << "Enter in the car sales for the 10 years. ";                                                                                                                                  
  int enteredYear;                                                                                                                                                                      
  int enteredWeek;                                                                                                                                                                      
  int enteredDay;                                                                                                                                                                       
  int numCarsSoldInADay;                                                                                                                                                                
                                                                                                                                                                                        
  char answer = 'y';                                                                                                                                                                    
  while(answer == 'y')                                                                                                                                                                  
  {                                                                                                                                                                                     
    cout << "Enter in a year number: " << endl;                                                                                                                                         
    cin >> enteredYear;                                                                                                                                                                 
                                                                                                                                                                                        
    cout << "Enter in a week number" << endl;                                                                                                                                           
    cin >> enteredWeek;                                                                                                                                                                 
                                                                                                                                                                                        
    cout << "Enter in a day number: " << endl;                                                                                                                                          
    cin >> enteredDay;                                                                                                                                                                  
                                                                                                                                                                                        
    cout << "Enter in number of cars were sold in that particular day: " << endl;                                                                                                       
    cin >> numCarsSoldInADay;                                                                                                                                                           
                                                                                                                                                                                        
    if((enteredYear >= 0 && enteredYear < NUM_YEARS_IN_SALES) &&                                                                                                                        
       (enteredWeek >= 0 && enteredWeek < NUM_WEEKS_IN_YEAR) &&                                                                                                                         
       (enteredDay >= 0 && enteredDay < NUM_DAYS_IN_WEEKS))                                                                                                                             
    {                                                                                                                                                                                   
      numCarsSold[NUM_YEARS_IN_SALES][NUM_WEEKS_IN_YEAR][NUM_DAYS_IN_WEEKS] = numCarsSoldInADay;                                                                                        
    }                                                                                                                                                                                   
    else                                                                                                                                                                                
    {                                                                                                                                                                                   
      cout << "You entered invalid year/week/day combination" << endl;                                                                                                                  
    }                                                                                                                                                                                   
                                                                                                                                                                                        
    cout << "Would you like to enter another sales day (y or n):  " << endl;                                                                                                            
    cin >> answer;                                                                                                                                                                      
  }                                                                                                                                                                                     
  int weeklySaleYearNumber;                                                                                                                                                             
  int weeklySaleWeekNumber;                                                                                                                                                             
                                                                                                                                                                                        
                                                                                                                                                                                        
  cout << "Enter in the year number: " << endl;                                                                                                                                         
  cin >> weeklySaleYearNumber;                                                                                                                                                          
                                                                                                                                                                                        
  cout << "Enter in the week number: " << endl;                                                                                                                                         
  cin >> weeklySaleWeekNumber;                                                                                                                                                          
  if((weeklySaleYearNumber >= 0 && weeklySaleYearNumber < NUM_YEARS_IN_SALES) &&                                                                                                        
     (weeklySaleWeekNumber >= 0 && weeklySaleWeekNumber < NUM_WEEKS_IN_YEAR))                                                                                                           
  {                                                                                                                                                                                     
    int sum = 0;                                                                                                                                                                        
    for(int day = 0; day < weeklySaleYearNumber; day++)                                                                                                                                 
    {                                                                                                                                                                                   
      sum += numCarsSold[weeklySaleYearNumber][weeklySaleWeekNumber][day];                                                                                                              
    }                                                                                                                                                                                   
    cout << "The sum of cars sold in the week: " << sum << endl;                                                                                                                        
  }                                                                                                                                                                                     
  else                                                                                                                                                                                  
  {                                                                                                                                                                                     
    cout << "Couldn't find sales for the same day with those inputs." << endl;                                                                                                          
  }                                                                                                                                                                                     
                                                                                                                                                                                        
                                                                                                                                                                                        
  int allSameDaySalesYear;                                                                                                                                                              
  int allSameDaySalesDay;                                                                                                                                                               
                                                                                                                                                                                        
  cout << "Enter in the year number: " << endl;                                                                                                                                         
  cin >> allSameDaySalesYear;                                                                                                                                                           
  cout << "Enter in a day number (0 for sunday, 1 for monday ..etc): " << endl;                                                                                                         
  cin >> allSameDaySalesDay;                                                                                                                                                            
                                                                                                                                                                                        
                                                                                                                                                                                        
  if((allSameDaySalesYear >= 0 && allSameDaySalesYear < NUM_YEARS_IN_SALES) &&                                                                                                          
     (allSameDaySalesDay >= 0 && allSameDaySalesDay < NUM_DAYS_IN_WEEKS))                                                                                                               
  {                                                                                                                                                                                     
                                                                                                                                                                                        
    int sum = 0;                                                                                                                                                                        
    for(int week= 0; week < NUM_WEEKS_IN_YEAR; week++)                                                                                                                                  
    {                                                                                                                                                                                   
      sum += numCarsSold[allSameDaySalesYear][week][allSameDaySalesDay];                                                                                                                
    }                                                                                                                                                                                   
    cout << "The sum of cars sold in day of the week " << allSameDaySalesDay << " during year number: " << allSameDaySalesYear << " is " << sum << endl;                                
  }                                                                                                                                                                                     
  else                                                                                                                                                                                  
  {                                                                                                                                                                                     
    cout << "Cannot find the sales for the same day of the year with those inputs."<<endl;                                                                                              
  }                                                                                                                                                                                     
  int weekentered;                                                                                                                                                                      
                                                                                                                                                                                        
  cout << "Enter in the week number: " << endl;                                                                                                                                         
  cin >> weekentered;                                                                                                                                                                   
  if(weekentered >= 0 && weekentered < NUM_WEEKS_IN_YEAR)                                                                                                                               
  {                                                                                                                                                                                     
    int sum = 0;                                                                                                                                                                        
    for(int year= 0; year < NUM_YEARS_IN_SALES; year++)                                                                                                                                 
    {                                                                                                                                                                                   
      for(int day= 0; day < NUM_DAYS_IN_WEEKS; day++)                                                                                                                                   
      {                                                                                                                                                                                 
        sum += numCarsSold[year][weekentered][day];                                                                                                                                     
      }                                                                                                                                                                                 
    }                                                                                                                                                                                   
                                                                                                                                                                                        
    cout << "The sum of cars sold in that speific week during all years is: " << sum << endl;                                                                                           
  }                                                                                                                                                                                     
  else                                                                                                                                                                                  
  {                                                                                                                                                                                     
    cout << "Cannot find the sales for the same day of the year with those inputs."<<endl;                                                                                              
  }                                                                                                                                                                                     
                                                                                                                                                                                        
                                                                                                                                                                                        
  return 0;                                                                                                                                                                             

```

## Summary:
Throughout these exercises, I explored how arrays store and manage collections of related data. I learned to combine loops with arrays to process values efficiently, calculate results like averages and variances, and represent more complex structures such as 2D grids and 3D datasets. This chapter strengthened my understanding of how data can be organized and accessed systematically, setting the stage for more advanced programming concepts.


```cpp

```
