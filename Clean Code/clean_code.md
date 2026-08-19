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


# Writing Functions
## Best Practices
- Using Descriptive Function Names that conveys its purpose, making it easy for others and yourself to understand its role within the code.
- A single function should be focused on a single task or responsibility. This not only makes them easier to understand but also promotes modular and reusable code.
- Strive for concise functions. Small functions are easier to comprehend and maintain. If a function becomes too lengthy, consider breaking it down into smaller, more specialized functions.
- Avoid functions with an excessive number of parameters. This simplifies function calls and reduces the likelihood of errors.
- Clean and consistern formatting. Use proper spacing, indentation, and line breaks to enhance readability.
- Encapsulate complex conditions within well-named functions. Instead of cluttering your code with intricate logical checks, delegate them to separate functions with descriptive names. This enhances clarity and promotges code reuse.
- Avoid using boolean flags as function parameters. If a function's behavior significantly differs based on a boolean parameter, consider splitting it into two separate functions. This improves readability and reduces potential confusion.

## Example of a Long, Complex Function
```
public void ProcessOrder(double price, int quantity, string customerType)
{
    if (price <= 0 || quantity <= 0)
    {
        Console.WriteLine("Invalid order.");
        return;
    }

    double subtotal = price * quantity;

    if (customerType == "VIP")
    {
        subtotal = subtotal * 0.90;
    }
    else if (customerType == "Student")
    {
        subtotal = subtotal * 0.95;
    }

    double tax = subtotal * 0.10;
    double total = subtotal + tax;

    Console.WriteLine("Order processed.");
    Console.WriteLine("Subtotal: $" + subtotal);
    Console.WriteLine("Tax: $" + tax);
    Console.WriteLine("Total: $" + total);
}
```
Why this function is difficulat ot maintain:
The function has too many responsibilities. It validates the order, calculates the subtotal, applies discounts, calculates tax, and displays the result. If one part needs to be changed, the developer has to work inside the same large function.

This makes the function less readable and harder to test. For example, testing the discount calculation requires going through the entire ProcessOrder function.

## Refactored Version
The function can be divided into smaller functions, with each function having one clear responsibility.
```
public void ProcessOrder(double price, int quantity, string customerType)
{
    if (!IsValidOrder(price, quantity))
    {
        Console.WriteLine("Invalid order.");
        return;
    }

    double subtotal = CalculateSubtotal(price, quantity);
    subtotal = ApplyDiscount(subtotal, customerType);

    double tax = CalculateTax(subtotal);
    double total = subtotal + tax;

    DisplayOrderSummary(subtotal, tax, total);
}

private bool IsValidOrder(double price, int quantity)
{
    return price > 0 && quantity > 0;
}

private double CalculateSubtotal(double price, int quantity)
{
    return price * quantity;
}

private double ApplyDiscount(double subtotal, string customerType)
{
    if (customerType == "VIP")
    {
        return subtotal * 0.90;
    }

    if (customerType == "Student")
    {
        return subtotal * 0.95;
    }

    return subtotal;
}

private double CalculateTax(double subtotal)
{
    return subtotal * 0.10;
}

private void DisplayOrderSummary(double subtotal, double tax, double total)
{
    Console.WriteLine("Order processed.");
    Console.WriteLine("Subtotal: $" + subtotal);
    Console.WriteLine("Tax: $" + tax);
    Console.WriteLine("Total: $" + total);
}
```

## Reflection
    Breaking down functions is beneficial because each function can focus on one specific responsibility. This makes the code easier to read, understand, test, debug, and modify. Smaller functions can also be reused in other part of the program.

    Refactoring improved the structure by separating the different tasks into individual functions. Instead of having one large function responsible for validation, calculations, discounts, tax, and displaying results, each task now has its own function. The main ProcessOrder() function is therefore shorter and easier to understand, while the supporting functions make the code more organised and maintainable.

## Don't Repeat Yourself (DRY)
DRY is a software development principle that avoids duplication of logic by promoting reusable components and code. It ensures changes are made in one place, improving maintainability and consistency. DRY works with modular design and SRP to build scalable and efficient systems.

- Promotes code reuse by reducing duplication and ensuring consistency across the codebase
- Centralizes logic to minimize errors, simplify updates, and support SRP for better system design.

**Before Refactoring**
```
public void ProcessOrder(double price, int quantity, string customerType)
{
    if (price <= 0 || quantity <= 0)
    {
        Console.WriteLine("Invalid order.");
        return;
    }

    double subtotal = price * quantity;

    if (customerType == "Premium")
    {
        subtotal *= 0.9;
    }

    double tax = subtotal * 0.1;
    double total = subtotal + tax;

    Console.WriteLine($"Total: {total}");
}

public void ProcessSpecialOrder(double price, int quantity, string customerType)
{
    if (price <= 0 || quantity <= 0)
    {
        Console.WriteLine("Invalid order.");
        return;
    }

    double subtotal = price * quantity;

    if (customerType == "Premium")
    {
        subtotal *= 0.9;
    }

    double tax = subtotal * 0.1;
    double total = subtotal + tax;

    Console.WriteLine($"Total: {total}");
}
```

**After Refactoring**
```
public void ProcessOrder(double price, int quantity, string customerType)
{
    if (!IsValidOrder(price, quantity))
    {
        Console.WriteLine("Invalid order.");
        return;
    }

    double subtotal = CalculateSubtotal(price, quantity);
    subtotal = ApplyDiscount(subtotal, customerType);

    double tax = CalculateTax(subtotal);
    double total = subtotal + tax;

    DisplayTotal(total);
}

public void ProcessSpecialOrder(double price, int quantity, string customerType)
{
    ProcessOrder(price, quantity, customerType);
}

private bool IsValidOrder(double price, int quantity)
{
    return price > 0 && quantity > 0;
}

private double CalculateSubtotal(double price, int quantity)
{
    return price * quantity;
}

private double ApplyDiscount(double subtotal, string customerType)
{
    if (customerType == "Premium")
    {
        return subtotal * 0.9;
    }

    return subtotal;
}

private double CalculateTax(double subtotal)
{
    return subtotal * 0.1;
}

private void DisplayTotal(double total)
{
    Console.WriteLine($"Total: {total}");
}
```

## Reflection
### What were the issues with duplicated code?
    The duplicated code made the program longer and harder to maintain. The same validation, calculation, discount, and tax logic appeared multiple places. If I needed to change the calculation, I would have to update it in every location, which could lead to mistakes or inconsistent results.

### How did refactoring improve maintainability?
    Refactoring removed the repeated code by placing common logic into separate reusable methods. This makes the code shorter, easier to read, and easier to update. Now, if a calculation or rule needs to change, I only need to modify it in one place. This reduces errors and makes the program easier to maintain in the future.