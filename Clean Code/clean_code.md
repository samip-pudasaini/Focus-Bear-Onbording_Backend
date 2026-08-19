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

# Naming Scheme
## Best practices

Variable names should reflect the value the variable is storing, so name them using a noun.
For example, let’s say we have a website with users, and we need a variable to store an individual user. This is what our code can look like:
```User user = new User();```

**General Naming Rules:**
- A slightly longer, descriptive name is always better than a short, ambiguous acronym
- Avoid single-letter variables like a, b or x. The only exception is for loop counters like i or j
- Use English and standard ASCII
- Do not include types in names (For example: strName or arrUsers). Modern editors track variable types automatically
- Explicitly state units: If a variable stores time or size, add the unit to prevent logic errors.

**Best Practices for Naming Variables**
- Nouns for data entities (e.g. totalPrice, userEmail)
- Boolean prefixes: use helper verbs like is, has, or can (e.g. isLoggedIn, hasPermission, canEdit)
- Consistent plurals for arrays (e.g. users or products_list)

**Best Practices for Naming Variables**
- Start with a verb (e.g. calculateTotal(), sendEmail())
- Avoid filler words (e.g. handleData(), process() or run())
- Match side effects: get (which implies a quick data fetch) if the function performs a heavy background calculation or database modification. Use calculate or update instead.

**Unclear Variable Name** 
```csharp
    int a=10,b=20,c=30;
```

**Refactored**
```csharp
    int firstNumber = 10;
    int secondNumber = 20;
    int maximumValue = 30;
```

## Reflections
    From everything I have learnt, a good variable or function name is descriptive of what its purpose is in the codebase, without having acronyms which are harder for other developers to understand, and in a standard language to avoid confusion and deployment issues.
    For Variables, you should use nouns for data entities while a prfiexes(verbs) to highlight boolean containing variables. And plurals for arrays as it signifies there are multiple entries in a single variable.
    For Functions, be precise on what it is trying to do. Not being vague like 
    handleData(), most functions need to handle data, instead use the purpose of the function like calculateData(), visualiseData(), etc.

    As we have seen form the example of Unclear Variable Names:
    int a=10,b=20,c=30; 
    We know that variable a, b and c store integers, but we are not aware of the purpose, is it multiplication, comparision, total number of items (used for looping, when processing information in a group of items).
    Now take for example a big codebase with these variables, finding where its used is not hard, using the search feature, but knowing why and what it is used for will be confusing.

    After the refractor
    int firstNumber = 10;
    int secondNumber = 20;
    int maximumValue = 30;
    The first number and second number did not improve much, but it showed that these are the numbers we will be using to do the operations that is asked of us in the program.
    The maximumValue is what makes this more understandable, it highlights that the operations should not exceed maximumValue or a variable that is greater than maximumValue cannot exist for this program, which could be a hardware limitation.
