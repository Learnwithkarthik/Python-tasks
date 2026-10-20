# Python Real-Time Task 3 — E-Commerce Shopping Cart & Billing System

**DSU Global IT | Python Functions Practice**

## Business scenario
An online store needs a console application where a customer selects products, adds quantities to a cart, checks out, and receives a final bill with discounts and GST.

**What you will practice:** variables, `input()`, `if/elif/else`, `for`, `while`, functions, parameters versus arguments, positional and keyword arguments, default arguments, `*args`, `**kwargs`, `return`, and local/global scope. This example also introduces lists, tuples, dictionaries, `try/except`, and `continue`.

> This is an educational simulation, not a payment-processing application. The prices and 18% GST are fixed for the exercise; actual tax depends on the product and applicable rules.

## Assignment questions
1. How can we track the number of orders placed?
2. How can we store multiple products with names and prices?
3. How do we add a selected product and quantity to a shopping cart?
4. How do we calculate a total when the number of item amounts varies?
5. How do we apply discounts based on the bill amount?
6. How do we print customer details when the fields may vary?
7. How do we stop checkout if the cart is empty?
8. How do we print each purchased item and find its total price?
9. How do we calculate discount, GST, and the final bill?
10. Why do we use `global` in the billing function?
11. How does a `while` loop keep the shopping application open?
12. How are products displayed with a `for` loop?
13. How do we handle valid product choices, quantities, and invalid input?
14. What happens when the user checks out or exits?

---

## Q1. How do we track the number of orders?
**What are we doing?** Creating a variable that counts completed bills.

```python
order_count = 0
```

**Why use it?** Each successful checkout should receive the next order number. It starts at zero because no orders have been billed yet.

**How it works:** After the first completed checkout, the count becomes `1`. After the second, it becomes `2`.

**తెలుగులో:** ప్రారంభంలో orders లేవు కాబట్టి `0` పెడతాం. ప్రతి successful checkout తర్వాత counter పెరుగుతుంది.

## Q2. How do we store the store's product catalog?
**What are we doing?** Mapping each product ID to a name and price.

```python
products = {
    1: ("Keyboard", 800),
    2: ("Mouse", 500),
    3: ("Monitor", 6000)
}
cart = []
```

**Why use it?** A dictionary makes products easy to look up by ID. Each dictionary value is a tuple `(name, price)`. The empty list stores items selected by the customer.

**How it works:** `products[2]` yields `("Mouse", 500)`. At the start `cart` has no items.

**తెలుగులో:** Product ID ఇచ్చి name, price తీసుకోవడానికి dictionary వాడుతున్నాం. Customer select చేసిన items ని `cart` list లో store చేస్తాం.

## Q3. How do we add items to the cart without repeating code?
**What are we doing?** Creating a reusable function for adding products.

```python
def add_product(product_id, quantity):
    name, price = products[product_id]
    cart.append((name, price, quantity))
    print("Added", quantity, name + "(s)")
```

**Why use a function?** Whether a customer chooses a mouse or a keyboard, the *same* add-to-cart logic applies.

**How it works:** `add_product(1, 2)` uses **positional arguments**: `1` goes into `product_id`, `2` goes into `quantity`. The lookup finds `("Keyboard", 800)` and appends `("Keyboard", 800, 2)` to the cart.

**తెలుగులో:** ప్రతి product కోసం ప్రత్యేకంగా code రాయకుండా, product ID మరియు quantity parameters తీసుకునే ఒక function ను reuse చేస్తున్నాం.

## Q4. How do we add any number of line-item amounts?
**What are we doing?** Calculating a bill subtotal using `*args`.

```python
def calculate_total(*amounts):
    total = 0
    for amount in amounts:
        total += amount
    return total
```

**Why `*args`?** We may have one item amount, two amounts, or several. `*amounts` collects all positional values into a tuple.

**How it works:** `calculate_total(1600, 500)` returns `2100`. The `total` variable is **local** to this function. `return` gives the result back for later calculations instead of merely printing it.

**తెలుగులో:** ఎన్ని item amounts వస్తాయో ముందుగా తెలియదు. కాబట్టి `*args` తో అన్ని values తీసుకుని loop ద్వారా add చేసి, `return` ద్వారా subtotal పంపిస్తాం.

## Q5. How do we decide the discount rate?
**What are we doing?** Applying business rules based on the subtotal.

```python
def get_discount(subtotal):
    if subtotal >= 5000:
        return 10
    elif subtotal >= 1000:
        return 5
    else:
        return 0
```

**Why use `if/elif/else`?** Different bill ranges require different discounts. Check the highest threshold first so a ₹6,000 purchase gets 10%, not 5%.

**How it works:** `get_discount(2100)` returns `5` (a percentage, **not** ₹5). The billing function later calculates the rupee discount.

**తెలుగులో:** Bill మొత్తం ఆధారంగా discount మారుతుంది. `>= 5000` condition ముందు check చేయడం వల్ల ఎక్కువ amount కి సరైన discount వస్తుంది.

## Q6. How do we display customer details with flexible fields?
**What are we doing?** Printing named information using `**kwargs`.

```python
def display_customer(**details):
    for key, value in details.items():
        print(key, ":", value)
```

**Why `**kwargs`?** Customer details could include name, city, payment method, etc. `**details` accepts named keyword arguments as a dictionary.

**How it works:**

```python
display_customer(Name="Ravi", City="Hyderabad", Payment="UPI")
```

Output:

```text
Name : Ravi
City : Hyderabad
Payment : UPI
```

**తెలుగులో:** Customer details ఎన్ని fields ఉన్నా, keyword arguments రూపంలో పంపించి dictionaryలా print చేయొచ్చు.

## Q7. Why do we check whether the cart is empty before billing?
**What are we doing?** Avoiding a meaningless checkout.

```python
def generate_bill(customer_name, city="Bangalore"):
    global order_count

    if len(cart) == 0:
        print("Your cart is empty")
        return
```

**Why this code?** An empty cart should not create an order. `return` immediately exits the function. `city="Bangalore"` is a **default parameter**: the caller may override it.

**How it works:** `generate_bill("Ravi", city="Hyderabad")` passes a positional name and a keyword city. If `cart` is empty, the function stops without increasing the order counter.

**తెలుగులో:** Cart ఖాళీ అయితే bill తయారు చేయకూడదు. అందుకే `return` తో function ని వెంటనే stop చేస్తున్నాం. City ఇవ్వకపోతే default Bangalore వాడుతుంది.

## Q8. How do we calculate each item's amount?
**What are we doing?** Multiplying price by quantity for each cart line.

```python
    order_count += 1
    item_totals = []

    print("\n===== FINAL BILL =====")
    for name, price, quantity in cart:
        amount = price * quantity
        item_totals.append(amount)
        print(name, "x", quantity, "= Rs.", amount)
```

**Why a `for` loop?** We must calculate the amount for **every** item, irrespective of cart size.

**How it works:** Two keyboards at ₹800 produce `800 * 2 = 1600`; one mouse at ₹500 produces `500 * 1 = 500`. The list becomes `[1600, 500]`.

**తెలుగులో:** Cart లో ఉన్న ప్రతి item కి price × quantity చేస్తాం. అన్ని amounts ని list లో పెడతాం, తద్వారా subtotal calculate చేయవచ్చు.

## Q9. How do we calculate subtotal, discount, GST, and final payment?
**What are we doing?** Building the final bill from item totals.

```python
    subtotal = calculate_total(*item_totals)
    discount_percentage = get_discount(subtotal)
    discount = subtotal * discount_percentage / 100

    taxable_amount = subtotal - discount
    gst = taxable_amount * 18 / 100
    final_bill = taxable_amount + gst
```

**Why this order?** First calculate total, then subtract the discount, then compute this exercise's 18% GST on the discounted amount.

**How it works (2 keyboards + 1 mouse):**

| Step | Calculation | Result |
|---|---|---:|
| Subtotal | ₹1,600 + ₹500 | ₹2,100.00 |
| Discount | ₹2,100 × 5% | ₹105.00 |
| After discount | ₹2,100 − ₹105 | ₹1,995.00 |
| GST | ₹1,995 × 18% | ₹359.10 |
| Final bill | ₹1,995 + ₹359.10 | **₹2,354.10** |

**Important syntax:** `calculate_total(*item_totals)` **unpacks** a list into positional arguments. This is different from `*amounts` in the function definition, which *collects* arguments.

**తెలుగులో:** ముందు subtotal తీసుకుని, discount తీసేస్తాం. మిగిలిన amount మీద GST calculate చేసి add చేస్తాం. `*item_totals` list లోని values ని function కి separate arguments గా పంపిస్తుంది.

## Q10. Why do we use a global variable inside the billing function?
**What are we doing?** Updating the shared count of completed orders.

```python
order_count = 0

def generate_bill(customer_name, city="Bangalore"):
    global order_count
    # After confirming the cart isn't empty:
    order_count += 1
```

**Why `global`?** Because `order_count += 1` *assigns* a new value. Without the `global` declaration, Python would treat `order_count` as a local variable and this code would raise `UnboundLocalError`.

**How it works:** The updated value remains available after the function finishes. Contrast this with `item_totals`, which is local to `generate_bill`.

**తెలుగులో:** Function బయట ఉన్న `order_count` value ని function లో మార్చాలంటే `global` అవసరం. `item_totals` మాత్రం function లోపల మాత్రమే ఉంటుంది.

> **Best-practice note:** Global variables are fine for demonstrating scope in a beginner exercise; larger apps often manage state through objects or explicit inputs and returns.

## Q11. Why do we use `while True` in the main application?
**What are we doing?** Keeping the shopping menu available until the user exits.

```python
while True:
    print("\n===== SHOPPING CART =====")
    # Show products and menu options...
    choice = input("Enter choice: ")
```

**Why `while`?** A customer may add multiple items before checkout. The menu must repeat without restarting Python.

**How it works:** `while True` loops indefinitely **unless** a `break` is reached. Menu choices are handled inside the loop.

**తెలుగులో:** Customer మరిన్ని products add చేయాలనుకోవచ్చు. అందుకే menu తిరిగి తిరిగి చూపించడానికి `while True` ఉపయోగిస్తున్నాం.

## Q12. How do we display the product catalog without writing each print separately?
**What are we doing?** Printing every product using iteration.

```python
for product_id, product in products.items():
    print(product_id, product[0], "- Rs.", product[1])

print("4. Checkout")
print("5. Exit")
```

**Why `for`?** The same printing logic works for three products or many more.

**How it works:** `.items()` returns each dictionary key and value. `product[0]` is the name; `product[1]` is the price.

**తెలుగులో:** ప్రతి product కి manually print రాయకుండా, dictionary మీద `for` loop run చేసి names/prices చూపిస్తాం.

## Q13. How do we handle product selection and invalid quantities?
**What are we doing?** Ensuring the user selects a valid product and enters a positive integer quantity.

```python
elif choice in ("1", "2", "3"):
    product_id = int(choice)

    try:
        quantity = int(input("Quantity: "))
        if quantity <= 0:
            print("Quantity must be positive")
            continue
        add_product(product_id, quantity)
    except ValueError:
        print("Please enter a valid quantity")
else:
    print("Invalid choice")
```

**Why this code?** A zero, negative, or non-numeric quantity should not be added to the cart. `try/except` prevents the program from crashing on text such as `two`. `continue` goes straight to the next menu iteration.

**How it works:** Selecting product `1` and quantity `2` calls `add_product(1, 2)`. Entering `-3` shows an error instead.

**తెలుగులో:** Invalid quantity వల్ల program ఆగిపోకుండా `try/except` వాడుతున్నాం. Quantity 0 లేదా negative అయితే `continue` తో menu మళ్లీ చూపిస్తాం.

## Q14. How do checkout and exit work?
**What are we doing?** Running the appropriate action when the user selects menu option `4` or `5`.

```python
if choice == "4":
    name = input("Customer name: ")
    generate_bill(name, city="Hyderabad")

elif choice == "5":
    print("Thank you!")
    break
```

**Why this code?** Checkout generates a bill; Exit must terminate the loop. The call uses a **positional argument** (`name`) and a **keyword argument** (`city="Hyderabad"`).

**How it works:** After a completed bill, the script clears the cart with `cart.clear()`, allowing another order. `break` on option `5` ends `while True`.

**తెలుగులో:** Option 4 తో bill generate అవుతుంది. Option 5 తో `break` run అయ్యి program stop అవుతుంది. City ని keyword argument గా పంపుతున్నాం.

---

## Full runnable code (`main.py`)
Copy this into `main.py` and run with `python main.py`:

```python
order_count = 0

products = {
    1: ("Keyboard", 800),
    2: ("Mouse", 500),
    3: ("Monitor", 6000)
}

cart = []

def add_product(product_id, quantity):
    name, price = products[product_id]
    cart.append((name, price, quantity))
    print("Added", quantity, name + "(s)")

def calculate_total(*amounts):
    total = 0
    for amount in amounts:
        total += amount
    return total

def get_discount(subtotal):
    if subtotal >= 5000:
        return 10
    elif subtotal >= 1000:
        return 5
    else:
        return 0

def display_customer(**details):
    for key, value in details.items():
        print(key, ":", value)

def generate_bill(customer_name, city="Bangalore"):
    global order_count

    if len(cart) == 0:
        print("Your cart is empty")
        return

    order_count += 1
    item_totals = []

    print("\n===== FINAL BILL =====")

    for name, price, quantity in cart:
        amount = price * quantity
        item_totals.append(amount)
        print(name, "x", quantity, "= Rs.", amount)

    subtotal = calculate_total(*item_totals)
    discount_percentage = get_discount(subtotal)
    discount = subtotal * discount_percentage / 100

    taxable_amount = subtotal - discount
    gst = taxable_amount * 18 / 100
    final_bill = taxable_amount + gst

    display_customer(
        Name=customer_name,
        City=city,
        Payment="UPI"
    )

    print("Subtotal: Rs.", round(subtotal, 2))
    print("Discount (" + str(discount_percentage) +
          "%): Rs.", round(discount, 2))
    print("GST (18%): Rs.", round(gst, 2))
    print("Final Bill: Rs.", round(final_bill, 2))
    print("Order Number:", order_count)

    cart.clear()

while True:
    print("\n===== SHOPPING CART =====")

    for product_id, product in products.items():
        print(product_id, product[0], "- Rs.", product[1])

    print("4. Checkout")
    print("5. Exit")

    choice = input("Enter choice: ")

    if choice == "4":
        name = input("Customer name: ")
        generate_bill(name, city="Hyderabad")

    elif choice == "5":
        print("Thank you!")
        break

    elif choice in ("1", "2", "3"):
        product_id = int(choice)

        try:
            quantity = int(input("Quantity: "))
            if quantity <= 0:
                print("Quantity must be positive")
                continue
            add_product(product_id, quantity)
        except ValueError:
            print("Please enter a valid quantity")
    else:
        print("Invalid choice")
```

## Sample execution

```text
===== SHOPPING CART =====
1 Keyboard - Rs. 800
2 Mouse - Rs. 500
3 Monitor - Rs. 6000
4. Checkout
5. Exit
Enter choice: 1
Quantity: 2
Added 2 Keyboard(s)

Enter choice: 2
Quantity: 1
Added 1 Mouse(s)

Enter choice: 4
Customer name: Ravi

===== FINAL BILL =====
Keyboard x 2 = Rs. 1600
Mouse x 1 = Rs. 500
Name : Ravi
City : Hyderabad
Payment : UPI
Subtotal: Rs. 2100
Discount (5%): Rs. 105.0
GST (18%): Rs. 359.1
Final Bill: Rs. 2354.1
Order Number: 1
```

## Quick concept mapping

| Concept | Where used | Why |
|---|---|---|
| Variable | `order_count`, `cart`, `subtotal` | Store data and calculations |
| Input | `input("Enter choice: ")` | Read customer actions |
| Conditional | `if / elif / else` | Discount tiers and menu choices |
| `for` loop | Items in catalog and cart | Process every item |
| `while` loop | Main menu | Keep shopping until exit |
| Function | `add_product`, `generate_bill` | Reuse separate operations |
| Parameters | `product_id`, `quantity`, `city` | Receive function inputs |
| Positional args | `add_product(1, 2)` | Match arguments by position |
| Keyword args | `city="Hyderabad"` | Identify argument by name |
| Default args | `city="Bangalore"` | Provide a fallback |
| `*args` | `calculate_total(*amounts)` | Accept variable-length positional values |
| `**kwargs` | `display_customer(**details)` | Accept named fields |
| `return` | `get_discount()` | Send result back to caller |
| Local variables | `subtotal`, `final_bill` | Temporary values in a function |
| Global variable | `global order_count` | Update shared order counter |

## Student practice / extensions
1. What happens if a customer checks out without adding any product? Explain which `return` executes.
2. What happens if quantity is `0`, `-2`, or `hello`?
3. Change the discount levels, then predict the result for an order worth ₹5,000 exactly.
4. Call `generate_bill("Anu")` without a city and explain the default value.
5. Explain the difference between `calculate_total(*item_totals)` and `def calculate_total(*amounts)`.
6. Add a **View Cart** menu option using a new function.
7. Challenge: Reject a blank customer name before generating a bill.

### Note on code quality
This sample intentionally uses a global cart and counter to reinforce function and scope concepts. In a production application, you would also use persistence, proper currency arithmetic (`Decimal`), real tax rules, and order/payment validation.
