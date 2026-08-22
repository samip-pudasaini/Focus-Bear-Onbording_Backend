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


# Identifying and Fixing Code Smells

## Corrected Refactored Version

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
        if (platform == InPersonPlatform)
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

The `Platform` bug was fixed by changing it to `platform`, which matches the parameter name in `DisplayShippingCost()`. This allows the code to compile correctly.

## Reflection

### What code smells did you find in your code?

I identified several code smells in the original code. The first was **magic numbers and strings**, such as `0.9`, `100`, `10`, and `"Premium"`. These values were replaced with named constants such as `PremiumDiscount`, `FreeShippingLimit`, `ShippingCost`, and `PremiumCustomer`. This makes the purpose of each value clearer and means that if a business rule changes, the value can be updated in one place instead of searching through the code for every occurrence.

Another issue was the **long function**. The original `ProcessOrder()` method was responsible for calculating the order subtotal, applying discounts, handling shipping, and displaying the order information. These responsibilities were separated into smaller methods: `CalculateSubtotal()`, `ApplyDiscount()`, `DisplayOrder()`, and `DisplayShippingCost()`. Each method now has a more focused responsibility.

I also identified **duplicated code** between `ProcessOrder()` and `ProcessOnlineOrder()`. Both methods calculated totals and displayed customer and order information. The refactored version combines this functionality into a single `ProcessOrder()` method and uses the `platform` parameter to determine the appropriate shipping behaviour.

The original class also showed signs of a **large class or God Object** because `StoreManager` was responsible for orders, customers, emails, and reports. The refactoring changed the class name to `OrderService` so that it focuses specifically on order-related responsibilities.

I also removed the **commented-out code**, such as `// SaveOrder()`, `// SendEmail()`, and the unused quantity discount. Keeping unused code in comments can make the code harder to understand and can leave developers unsure whether it is still required.

Finally, I improved the **inconsistent naming**. Names such as `n`, `p`, `q`, `ct`, and `x` did not clearly explain what the variables represented. They were replaced with descriptive names such as `customerName`, `price`, `quantity`, `customerType`, `subtotal`, and `total`.

### Readability and Maintainability

The refactored code improves **readability** because the names of methods, variables, and constants clearly describe their purpose. For example, `CalculateSubtotal()` immediately indicates what the method does, while `FreeShippingLimit` clearly communicates what the value `100.00` represents. Breaking the large `ProcessOrder()` method into smaller methods also makes the overall code easier to read because each method has a single, focused responsibility.

The refactoring also improves **maintainability** because changes can be made in a more controlled way. For example, if the free shipping threshold changes from `$100` to `$150`, the `FreeShippingLimit` constant can be updated without searching through multiple methods. Similarly, shipping logic is contained in `DisplayShippingCost()`, so changes to shipping rules can be made in one location.

Removing duplicated code also makes maintenance easier. If the order display or shipping logic needs to be changed, developers only need to update the relevant method rather than making the same change in multiple places. This reduces the chance of introducing inconsistent behaviour or bugs.

Overall, the refactored code is more modular, readable, and maintainable. Smaller methods make it easier to locate and debug problems, meaningful names make the code easier to understand, and removing duplication and magic values makes future changes safer and more efficient.
