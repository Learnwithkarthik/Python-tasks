# Python Real-Time Task 2 — Cloud Server Resource Monitoring

**Course:** DSU Global IT — Python Functions Practice  
**Level:** Beginner to Intermediate  
**Project file:** `main.py`

## Business problem

A DevOps engineer needs to check CPU, Memory, and Disk usage on cloud servers. Build a **console-based simulation** that asks an operator to enter those percentages, classifies each metric as Healthy, Warning, or Critical, and tracks how many server checks were performed.

> **Important:** This is a learning simulation. It does **not** connect to Google Cloud or automatically read VM metrics. Every server check is based on the values entered by the user. If you check the same server twice, the program counts two checks.

## Requirements → implementation → reason

### Question 1 — How do we count the number of checked, critical, and warning servers?

**What we are doing:** Creating counters at the beginning of the program.

```python
server_count = 0
critical_count = 0
warning_count = 0
```

**Why:** We need to remember how many monitoring checks have completed and how many results were Critical or Warning. All three values start at zero because no check has happened yet.

**Python concept:** Variables and global scope.

**Telugu:** మొదట ఏ server check చేయలేదు కాబట్టి counters అన్నీ `0`. ఒక check పూర్తయ్యాక వీటి values పెరుగుతాయి.

### Question 2 — How do we decide if CPU, Memory, or Disk usage is healthy?

**What we are doing:** Writing one reusable function to classify any utilization percentage.

```python
def check_status(usage):
    if usage >= 80:
        return "CRITICAL"
    elif usage >= 60:
        return "WARNING"
    else:
        return "HEALTHY"
```

**Why:** The same rule applies to CPU, Memory, and Disk. Instead of writing three separate sets of `if` statements, one function can be called three times. `usage` is a **parameter**. `return` sends a result back to the caller, so we can store it in a variable and make another decision.

| Entered usage | Returned result |
|---|---|
| `85` | `CRITICAL` |
| `65` | `WARNING` |
| `40` | `HEALTHY` |

**Telugu:** CPU, Memory, Disk అన్నింటికీ ఒకే rule ఉంది. అందుకే ఒక function రాసి మళ్లీ మళ్లీ call చేస్తున్నాం. `return` ఇచ్చిన value ని బయట ఉపయోగించుకోవచ్చు.

### Question 3 — How do we find the highest utilization among CPU, Memory, and Disk?

**What we are doing:** Building a function that can accept any number of percentages.

```python
def highest_usage(*values):
    highest = 0
    for value in values:
        if value > highest:
            highest = value
    return highest
```

**Why:** `*values` is `*args`: it accepts multiple positional arguments and packs them into a tuple. A `for` loop checks each percentage; `highest` stores the biggest number found. Starting at `0` works for this program because valid utilization is between `0` and `100`.

**Example:** `highest_usage(85, 65, 40)` returns `85`.

**Telugu:** `*args` వల్ల ఎన్ని values అయినా పంపవచ్చు. `for` loop ప్రతి value check చేసి పెద్దదాన్ని `highest` లో save చేస్తుంది.

### Question 4 — How can we display flexible server information without fixed parameters?

**What we are doing:** Creating a function to print server metadata.

```python
def show_metadata(**details):
    for key, value in details.items():
        print(key, ":", value)
```

**Why:** `**details` is `**kwargs`: it collects named keyword arguments into a dictionary. `.items()` returns key–value pairs, which the `for` loop prints. It works with `Server`, `Region`, `Environment`, or any other keyword names.

**Example:** `show_metadata(Server="vm01", Region="asia-south1")` prints each field.

**Telugu:** `**kwargs` అంటే `key=value` format లో అనేక details ఇవ్వచ్చు. ఆ details ని dictionary లాగా తీసుకుని print చేస్తాం.

### Question 5 — How do we start one server check and provide a default region?

**What we are doing:** Defining the main function responsible for one monitoring check.

```python
def monitor_server(server_name, region="europe-west3"):
    global server_count, critical_count, warning_count
```

**Why:** `server_name` is a required parameter. `region` has a **default argument**, so it is optional when calling the function. If the caller provides `region="asia-south1"`, that supplied value overrides the default. We use `global` because this function updates the three counters declared outside it. Without `global`, assignments such as `server_count += 1` would fail with `UnboundLocalError`.

**Telugu:** `server_name` తప్పనిసరిగా ఇవ్వాలి. `region` ఇవ్వకపోతే default గా `europe-west3` వస్తుంది; ఇస్తే ఇచ్చిన value తీసుకుంటుంది. బయట ఉన్న counter ని function లో మార్చడానికి `global` అవసరం.

### Question 6 — How do we prepare to collect CPU, Memory, and Disk values?

**What we are doing:** Initializing variables for this server check inside `monitor_server()`.

```python
    usage_values = []
    metrics = ["CPU", "Memory", "Disk"]
    overall_status = "HEALTHY"
```

**Why:** `metrics` lists the three things to check. `usage_values` will store the entered percentages. `overall_status` starts as Healthy and changes if any metric produces a Warning or Critical result. These are **local variables**: each function call gets its own values.

**Telugu:** ఒక్క server కి మూడు metrics check చేస్తాం. మొదట status `HEALTHY` అని assume చేసి, ఎక్కువ usage ఉంటే మార్చుతాం.

### Question 7 — How do we ask for all three metrics without repeating the input code?

**What we are doing:** Looping through metric names and validating each entered percentage.

```python
    for metric in metrics:
        while True:
            try:
                usage = float(input(metric + " Usage (%): "))
                if 0 <= usage <= 100:
                    break
                print("Enter a value from 0 to 100.")
            except ValueError:
                print("Please enter a valid number.")
```

**Why:** `for` runs once per metric: CPU, then Memory, then Disk. The inner `while True` repeats until the user gives a valid number. `float()` accepts decimal percentages such as `78.5`. The condition `0 <= usage <= 100` rejects impossible percentages. `try/except` prevents a crash if someone types text like `abc`.

**Important:** This `break` exits only the **inner `while` loop**, not the outer `for` loop.

**Telugu:** `for` loop మూడు metrics కోసం నడుస్తుంది. తప్పుగా input ఇస్తే `while` మళ్లీ అడుగుతుంది. సరైన input ఇచ్చినప్పుడు మాత్రమే `break` అవుతుంది.

### Question 8 — How do we save each percentage and classify it?

**What we are doing:** Remembering the percentage and calling the reusable classification function.

```python
        usage_values.append(usage)
        status = check_status(usage)
        print(metric + ":", status)
```

**Why:** `.append()` adds the entered number to `usage_values`, which is later used to find maximum utilization. `check_status(usage)` passes the actual input as an **argument** and stores the function's returned text in `status`.

**Example:** Entering `85` for CPU prints `CPU: CRITICAL`.

**Telugu:** `usage` అనేది actual argument; `check_status` లోని `usage` parameter దాన్ని తీసుకుంటుంది. తిరిగి వచ్చిన result ని `status` లో save చేస్తాం.

### Question 9 — How do we choose one overall status for the server?

**What we are doing:** Giving Critical higher priority than Warning.

```python
        if status == "CRITICAL":
            overall_status = "CRITICAL"
        elif status == "WARNING" and overall_status != "CRITICAL":
            overall_status = "WARNING"
```

**Why:** Even if Memory and Disk are healthy, one Critical CPU reading makes the overall server status Critical. The second condition does not let a later Warning replace an earlier Critical.

**Example:** CPU = Critical, Memory = Warning, Disk = Healthy → overall status = **Critical**.

**Telugu:** ఏ ఒక్క metric `CRITICAL` అయినా మొత్తం status `CRITICAL`. తరువాత `WARNING` వచ్చినా Critical ని మార్చకూడదు.

### Question 10 — How do we update monitoring counters when one check finishes?

**What we are doing:** Counting this completed check and its final severity.

```python
    server_count += 1

    if overall_status == "CRITICAL":
        critical_count += 1
    elif overall_status == "WARNING":
        warning_count += 1
```

**Why:** Every finished check increments `server_count`. Only Critical checks increment `critical_count`; only Warning checks increment `warning_count`. Healthy checks still count toward the total but don't increment a separate severity counter.

**Telugu:** ఒక server check పూర్తి అయ్యాక total count పెరుగుతుంది. Final status ని బట్టి Critical లేదా Warning count పెరుగుతుంది.

### Question 11 — How do we print a report showing server metadata and maximum usage?

**What we are doing:** Calling the metadata and highest-usage functions.

```python
    print("\n===== SERVER REPORT =====")
    show_metadata(
        Server=server_name,
        Region=region,
        Environment="Production"
    )

    print("Overall Status:", overall_status)
    print("Highest Utilization:",
          highest_usage(*usage_values), "%")
    print("Servers Checked:", server_count)
```

**Why:** The named fields in `show_metadata()` demonstrate **keyword arguments** and `**kwargs`. In `highest_usage(*usage_values)`, the `*` **unpacks** the list into positional arguments. This is different from `*values` in the function definition, which **packs** arguments.

**Example:** `usage_values = [85, 65, 40]` is unpacked as if we called `highest_usage(85, 65, 40)`.

**Telugu:** Function definition లో `*values` arguments ని collect చేస్తుంది. Function call లో `*usage_values` list లోని numbers ని విడివిడిగా పంపిస్తుంది.

### Question 12 — How do we show a summary of all checks?

**What we are doing:** Creating a separate function to print totals.

```python
def show_summary():
    print("\n===== MONITORING SUMMARY =====")
    print("Servers Checked:", server_count)
    print("Critical Servers:", critical_count)
    print("Warning Servers:", warning_count)
    print("Healthy Servers:",
          server_count - critical_count - warning_count)
```

**Why:** A separate function makes the code reusable and easier to understand. It **reads** the global counters but does not reassign them, so it does **not** need a `global` statement. Healthy checks are calculated as total minus Critical minus Warning.

**Telugu:** Global variable ని కేవలం చదవాలంటే `global` keyword అవసరం లేదు. Function లో value ని మార్చాలంటే మాత్రం అవసరం.

### Question 13 — How do we keep the program running until the user chooses Exit?

**What we are doing:** Displaying the main menu repeatedly.

```python
while True:
    print("\n===== CLOUD SERVER MONITOR =====")
    print("1. Check Server")
    print("2. View Summary")
    print("3. Exit")

    choice = input("Enter choice: ")
```

**Why:** A menu-based console application should let someone perform multiple tasks without restarting Python. `while True` continues until we explicitly execute `break`.

**Telugu:** User `Exit` select చేసే వరకు menu మళ్లీ మళ్లీ చూపించడానికి `while True` వాడుతున్నాం.

### Question 14 — How do we execute the correct function for each menu option?

**What we are doing:** Checking the user's selection with `if`, `elif`, and `else`.

```python
    if choice == "1":
        server_name = input("Server name: ")
        monitor_server(
            server_name,
            region="asia-south1"
        )
    elif choice == "2":
        show_summary()
    elif choice == "3":
        break
    else:
        print("Invalid choice")
```

**Why:** Option 1 checks a server, option 2 prints the summary, and option 3 exits the main `while` loop. The `server_name` value is passed as a **positional argument**, while `region="asia-south1"` is passed as a **keyword argument**. `"asia-south1"` overrides the function's default `"europe-west3"`.

**Telugu:** `choice` బట్టి correct function call చేస్తాం. ఇక్కడ `server_name` positional argument; `region=...` keyword argument.

---

## Full execution flow

```text
START
  |
  v
Initialize counters = 0
  |
  v
Display main menu <----------------------------+
  |                                            |
  +-- 1. Check Server                          |
  |      |                                     |
  |      v                                     |
  |   Ask server name                          |
  |      |                                     |
  |   For CPU, Memory, Disk:                   |
  |      - Ask percentage                      |
  |      - Retry invalid input                 |
  |      - Classify Healthy/Warning/Critical   |
  |      - Update overall status               |
  |      |                                     |
  |   Increment counters                       |
  |   Show report -----------------------------+
  |                                            |
  +-- 2. View Summary                          |
  |      |                                     |
  |   Show counters ---------------------------+
  |
  +-- 3. Exit --> END
  |
  +-- Other --> Invalid choice ----------------+
```

## Example dry run

Suppose the user checks `gcp-prod-vm01` with CPU = 85, Memory = 65, Disk = 40.

| Step | Variable / function | Result |
|---|---|---|
| 1 | `monitor_server("gcp-prod-vm01", region="asia-south1")` | Function starts |
| 2 | `usage_values` | `[]` |
| 3 | CPU input = `85`, `check_status(85)` | `CRITICAL` |
| 4 | Memory input = `65`, `check_status(65)` | `WARNING` |
| 5 | Disk input = `40`, `check_status(40)` | `HEALTHY` |
| 6 | `usage_values` | `[85.0, 65.0, 40.0]` |
| 7 | `overall_status` | `CRITICAL` |
| 8 | `highest_usage(*usage_values)` | `85.0` |
| 9 | `server_count` | `1` |
| 10 | `critical_count` | `1` |
| 11 | `warning_count` | `0` (the check was classified as Critical overall) |

**Important distinction:** `warning_count` counts checks with **overall Warning status**, not individual Warning metrics. Here Memory is Warning, but the server's overall status is Critical, so `warning_count` stays `0`.

## Quick knowledge checks for students

1. Why do we initialize `overall_status` to `"HEALTHY"`?
2. Why does `check_status()` use `return` instead of only `print()`?
3. What happens when CPU is 90 and Memory is 70?
4. What is the difference between `*values` and `*usage_values`?
5. Why does `show_metadata()` use `**details`?
6. When would removing `global server_count` produce an error?
7. Which `while` loop is exited when a valid CPU percentage is entered?
8. If the same server is checked twice, what value does `server_count` have?
9. Why is the default region not shown when `region="asia-south1"` is passed?
10. What should happen if a user enters CPU utilization as `abc` or `120`?

## How to run

Save the original Python program as `main.py`, place this document beside it as `README.md`, and run:

```bash
python main.py
```

## Learning outcome

By completing this project, students practice variables, input, conditional statements, `for`, `while`, nested loops, function definitions and calls, local/global variables, positional/keyword/default arguments, `*args`, `**kwargs`, `return`, lists, and input validation.
