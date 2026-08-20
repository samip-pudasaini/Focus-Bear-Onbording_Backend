# Identifying and Fixing Code Smells
Code with code smells

```csharp
public class StoreManager
{
    public void ProcessOrder(string n, double p, int q, string ct)
    {
        double x = p * q;

        if (ct == "Premium")
        {
            x = x * 0.9;
        }
            
        Console.WriteLine("Free shipping");

        // if (q > 10)
        // {
        //     x = x * 0.95;
        // }

        Console.WriteLine("Customer: " + n);
        Console.WriteLine("Total: $" + x);

        // SaveOrder();
        // SendEmail();
    }

    public void ProcessOnlineOrder(string customerName, double price, int quantity)
    {
        double total = price * quantity;

        if (total > 100)
        {
            Console.WriteLine("Free shipping");
        }
        else
        {
            Console.WriteLine("Shipping: $10");
        }

        Console.WriteLine("Customer: " + customerName);
        Console.WriteLine("Total: $" + total);
    }

    public void ManageCustomer()
    {
        Console.WriteLine("Adding customer...");
        Console.WriteLine("Updating customer...");
        Console.WriteLine("Deleting customer...");
        Console.WriteLine("Sending customer email...");
        Console.WriteLine("Generating customer report...");
        Console.WriteLine("Saving customer data...");
    }
}
```

| Code smell                     | Example                                                                            |
| ------------------------------ | ---------------------------------------------------------------------------------- |
| **Magic Numbers & Strings**    | `0.9`, `100`, `10`, `"Premium"`                                                    |
| **Long Functions**             | `ProcessOrder()` performs calculation, discounting, shipping and displaying        |
| **Duplicate Code**             | `ProcessOrder()` and `ProcessOnlineOrder()` contain similar shipping/display logic |
| **Large Class / God Object**   | `StoreManager` handles orders, customers, emails and reports                       |
| **Deeply Nested Conditionals** | Multiple conditions can become difficult to follow as the function grows           |
| **Commented-Out Code**         | `// SaveOrder()`, `// SendEmail()` and the quantity discount                       |
| **Inconsistent Naming**        | `n`, `p`, `q`, `ct`, and `x` do not clearly describe their purpose                 |


**Refactored version**
```csharp
public class OrderService
{
    private const double PremiumDiscount = 0.10;
    private const double FreeShippingLimit = 100.00;
    private const double ShippingCost = 10.00;
    private const string PremiumCustomer = "Premium";
    private const string InPersonPlatform = "In-Person";

    public void ProcessOrder(string customerName, double price, int quantity,
                            string customerType, string platform)
    {
        double subtotal = CalculateSubtotal(price, quantity);
        double total = ApplyDiscount(subtotal, customerType);

        DisplayOrder(customerName, total, platform);
    }

    private double CalculateSubtotal(double price, int quantity)
    {
        return price * quantity;
    }

    private double ApplyDiscount(double amount, string customerType)
    {
        if (customerType == PremiumCustomer)
        {
            return amount * (1 - PremiumDiscount);
        }

        return amount;
    }

    private void DisplayOrder(string customerName, double total, string platform)
    {
        Console.WriteLine("Customer: " + customerName);
        Console.WriteLine("Total: $" + total);

        DisplayShippingCost(total, platform);
    }

    private void DisplayShippingCost(double total, string platform)
    {
        if(Platform == InPersonPlatform)
        {
             Console.WriteLine("Free shipping");
             return;
        } 
        
        if (total >= FreeShippingLimit)
        {
            Console.WriteLine("Free shipping");
            return;
        }
        Console.WriteLine($"Shipping: ${ShippingCost}");
    }
}
```

## Reflection
### What code smells did you find in your code?
The refactor worked on multiple aspects of the codebase. Firstly the hardcoded values were changed, with variables having the value. Like for applying discount, instead of directly hardocding in the required space, a variable is used, if the Discount percentage is changed at a later date, we do not need to refactor at everyplace.
The Long Function `ProcessOrder()` was handling multiple tasks which has now been distributed to multiple functions handling their respective tasks.
The code were not commented out, checked if anything important remained commented out.
The variables have been named as their function and the purpose they carry.
There was duplicated code with two function, one for inperson and one for online purchase. In the refactored code, the functions have been removed and inplace if the platform being used is detected to be in person, the shipping fee is not included.

Avoiding code smells make future debugging easier by making the code cleaner, easier to understand, find and concise. As we could see, the refactored code is modular, more concise while being discriptive as what the function are meant to do and what the variables are for.
When functions are smaller and responsibilities are separated, developers can identify where a problem occurs without having to search through a large amount of unrealted code. Removing duplicated code also means a bug usually only needs to be fixed in one place instead of multiple places. Using meaningful names and constants makes it easier to understand what values and variables represent. Overall, clean code reduces the time needed to find, understand, and fix errors.