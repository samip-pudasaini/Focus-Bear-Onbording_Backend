# Clean Code

## Principles
    Simplicity means keeping code as straightforward as possible. Code should avoid unneccessary complexity, duplicated logic, and features that are not required. Simple code is easier to understand, test, debug and modify.

    Readability means writing code that is easy for another developer to understand. Meaningful variable and method names, clear formatting, appropriate comments, and logical structure all improve readability.

    Maintainability means making code easy to change, fix, and extend in the future. Code should be organised into small, focused functions or classes, avoid unnecessary duplication, and have clear responsibilities.

    Consistency means following the same coding style and conventions throughout a project. This includes naming conventions, indentation, file organisation, formatting, and the use of established project patterns or style guides.

    Efficiency means writing code that uses resources effectively and performs well. Developers should consider factors such as execution time, memory usage, and unnecessary operations. However, optimisation should not make the code unnecessarily complicated. Avoid premature optimisation and focus on improving performance when there is a clear need.

## Code example

### Messy code
    ```csharp
    int a=10,b=20,c=30;
    if(a+b>c){Console.WriteLine("yes");}else{Console.WriteLine("no");}
    ```
    This code is difficult to read because the variable names are unclear and the formattinbg makes the structure difficult to understand.

### Cleaner code

    ```csharp
    int firstNumber = 10;
    int secondNumber = 20;
    int maximumValue = 30;

    if (firstNumber + secondNumber > maximumValue)
    {
        Console.WriteLine("The values exceed the maximum");
    }
    else
    {
        Console.WriteLine("The values are within the maximum");
    }
    ```