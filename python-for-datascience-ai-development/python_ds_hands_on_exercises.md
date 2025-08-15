# Hands-on Python & Data Science Practice — 30 Exercises (Self‑Contained)

> Audience: You’ve completed a foundations course (Python, NumPy, Pandas, APIs, web scraping).  
> Goal: Build real confidence through targeted, practical exercises.  
> Format: Each exercise states **exact task**, **dataset/URL**, and **hints** so you can start immediately.

---

## Section 1 — Core Python & Strings (1–6)

### 1) Hello Variables (f-strings)
**Task:** Create variables for your `name`, `age`, and `city`, then print a sentence using an f-string (e.g., `"Hi, I'm Pavel, 33, from Prague."`).  
**Hints:** Use `f"..."` and `{variable}` placeholders.

---

### 2) Temperature Converter
**Task:** Write a function `convert_temp(value, scale)` that converts temperatures:  
- If `scale="C"`, return Fahrenheit; if `scale="F"`, return Celsius.  
**Hints:** C→F: `(C * 9/5) + 32`; F→C: `(F - 32) * 5/9`. Validate inputs and raise `ValueError` for bad `scale`.

---

### 3) String Reversal & Palindrome
**Task:** Write a function `is_palindrome(s)` that returns `True` if the string reads the same forwards/backwards (ignore case and spaces).  
**Hints:** Normalize with `.lower()` and `''.join(s.split())`; reverse via slicing `s[::-1]`.

---

### 4) Substring Finder
**Task:** Given `text` and `word`, print the **starting index** of `word` within `text` or `-1` if not found.  
**Hints:** Use `str.find()`.

---

### 5) Table Formatting
**Task:** Print a table of products and prices aligned in columns. Use a list like:  
`items = [("Apple", 1.2), ("Banana", 0.8), ("Kiwi", 2.1)]`.  
**Hints:** `print(f"{name:<10}{price:>8.2f}")` for alignment and 2‑decimal formatting.

---

### 6) Word Frequency Counter
**Task:** Count occurrences of words in a paragraph (ignore case & punctuation) and print the top 5 words.  
**Hints:** Use `re.sub(r"[^a-z0-9\s]", "", text.lower())`, `str.split()`, `collections.Counter.most_common(5)`.

---

## Section 2 — Data Structures & Control Flow (7–12)

### 7) Unique Words (Set)
**Task:** Return a sorted list of unique words from a given paragraph (ignore case).  
**Hints:** Convert to lowercase; use `set()` then `sorted()`.

---

### 8) Grocery List Manager
**Task:** Maintain a list of grocery items. Implement functions `add_item`, `remove_item`, `list_items`.  
**Hints:** Use a Python list; handle `ValueError` on removal if item not found.

---

### 9) Student Grades (Dictionary)
**Task:** Given a dict like `{"Ana":[80, 90], "Ben":[75, 85, 95]}`, compute each student’s average and the class average.  
**Hints:** Iterate dict items; use `sum()/len()`.

---

### 10) Tuple Unpacking
**Task:** Given points `[(1,2), (3,4), (5,6)]`, unpack to `x, y` and compute Euclidean distance from origin for each.  
**Hints:** Distance `((x**2 + y**2) ** 0.5)`.

---

### 11) Even & Odd Sorter
**Task:** Given a list of integers, create two lists: `evens` and `odds`.  
**Hints:** Use modulo `% 2` inside a loop or list comprehensions.

---

### 12) Prime Checker
**Task:** Implement `is_prime(n)` returning `True` for prime numbers (n ≥ 2).  
**Hints:** Only check divisors up to `int(n**0.5)`. Handle edge cases (n < 2).

---

## Section 3 — Functions, OOP & Exceptions (13–18)

### 13) Simple Calculator
**Task:** Implement `add`, `subtract`, `multiply`, `divide` and a CLI loop that asks for operation & two numbers.  
**Hints:** Use `try/except` to handle non-numeric input and division by zero.

---

### 14) Factorial (Recursion)
**Task:** Implement `factorial(n)` using recursion.  
**Hints:** Base case: `0! == 1`. Validate `n` is an integer ≥ 0.

---

### 15) Bank Account Class
**Task:** Create a `BankAccount` class with methods: `deposit`, `withdraw`, `balance`. Prevent negative balances.  
**Hints:** Raise `ValueError` on invalid operations; store balance as float.

---

### 16) Shapes OOP
**Task:** Create base class `Shape` with `.area()`. Subclasses: `Circle(r)`, `Rectangle(w,h)`, `Triangle(b,h)`.  
**Hints:** Circle area `math.pi * r**2`. Validate inputs > 0.

---

### 17) File Reader with Exception Handling
**Task:** Write `read_file(path)` returning file contents. If file not found, return `"File not found"`.  
**Hints:** Catch `FileNotFoundError`. Consider `os.path.exists` for checks.

---

### 18) Safe Division Function
**Task:** `safe_divide(a, b)` returns `a/b` or string `"Cannot divide by zero"` if `b == 0`.  
**Hints:** Use `try/except ZeroDivisionError` or pre‑check `b`.

---

## Section 4 — NumPy, Pandas, Series & Time Series (19–24)

### 19) NumPy Array Statistics
**Task:** Create `np.array([3,7,2,9,5])` and compute sum, mean, median, std.  
**Hints:** Use `np.sum`, `np.mean`, `np.median`, `np.std`.

---

### 20) Matrix Multiplication
**Task:** Multiply  
`A = np.array([[1,2,3],[4,5,6]])` (2×3) and `B = np.array([[7,8],[9,10],[11,12]])` (3×2).  
**Hints:** Use `A @ B` or `np.dot(A,B)`; verify resulting shape (2×2).

---

### 21) Pandas Series Basics
**Task:** Create a `pd.Series` from `{"Cisco": 52.3, "Palo Alto": 300.5, "Fortinet": 69.2}` representing stock prices.  
Compute min, max, mean and select value by label `"Palo Alto"`.  
**Hints:** Use `series["Palo Alto"]`, `series.min()`, etc.

---

### 22) Time Series — Resample
**Task:** Create a date range `pd.date_range("2024-01-01", periods=10, freq="D")` and a random Series of length 10.  
Convert to a `pd.Series` with the date index and compute weekly sum via resample.  
**Hints:** `s.resample("W").sum()`.

---

### 23) Read CSV and Filter
**Task:** Load the **tips dataset** (URL below) into a DataFrame and filter rows where `total_bill > 30`.  
**URL:** `https://raw.githubusercontent.com/mwaskom/seaborn-data/master/tips.csv`  
**Hints:** Use `pd.read_csv(url)` and boolean indexing.

---

### 24) GroupBy Aggregation (Defined Dataset)
**Task:** Using the same **tips dataset** as #23, compute the average `tip` by `day` and by `sex`.  
Also compute average `tip` by `day` & `time` (two keys).  
**Hints:** `df.groupby("day")["tip"].mean()` and `df.groupby(["day","time"])["tip"].mean().reset_index()`.

---

## Section 5 — Web APIs & Web Scraping (25–30)

### 25) Simple API Call (JSON)
**Task:** Fetch public repo info for pandas using GitHub API and print `full_name`, `stargazers_count`, `open_issues`.  
**URL:** `https://api.github.com/repos/pandas-dev/pandas`  
**Hints:** Use `requests.get(url).json()`; handle HTTP errors with `response.raise_for_status()`.

---

### 26) Currency Conversion via API
**Task:** Convert 100 USD to EUR using exchangerate.host and print the converted value.  
**URL:** `https://api.exchangerate.host/latest?base=USD&symbols=EUR`  
**Hints:** Parse JSON `rates["EUR"]`; handle network exceptions with `try/except requests.RequestException`.

---

### 27) Weather API (No API key)
**Task:** Fetch current weather for **Prague** using Open‑Meteo and print temperature & windspeed.  
**URL:** `https://api.open-meteo.com/v1/forecast?latitude=50.08&longitude=14.44&current_weather=true`  
**Hints:** Access `json()["current_weather"]` keys: `temperature`, `windspeed`.

---

### 28) Basic Web Scraper — Quotes
**Task:** Scrape the first page of quotes (text + author) from Quotes to Scrape and save to CSV.  
**URL:** `https://quotes.toscrape.com/`  
**Hints:** Use `requests` + `BeautifulSoup`; select `div.quote > span.text` and `small.author`. Remember `pip install beautifulsoup4` if needed.

---

### 29) Image Downloader — Books to Scrape
**Task:** Scrape the first page of `Books to Scrape`, collect all **image URLs**, and download the images to a local folder `images/`.  
**URL:** `https://books.toscrape.com/`  
**Hints:** Use `os.makedirs("images", exist_ok=True)`, parse `img` tags (`src` is relative), join with base URL via `urllib.parse.urljoin`.

---

### 30) Time Series from CSV (External Dataset)
**Task:** Load daily minimum temperatures dataset and:  
1) Parse the `Date` column as datetime; set it as index.  
2) Plot daily values.  
3) Resample to monthly mean and print the first 5 rows.  
**URL:** `https://raw.githubusercontent.com/jbrownlee/Datasets/master/daily-min-temperatures.csv`  
**Hints:** `pd.read_csv(url, parse_dates=["Date"], index_col="Date")`, `df.resample("M").mean()`; for plotting use `matplotlib.pyplot`.

---

## Environment & Imports (General)
- Recommended: **Jupyter Notebook** (`pip install notebook`) or **VS Code** with Python extension.
- Common imports you’ll need across exercises:
  ```python
  import os, re, math, json
  import numpy as np
  import pandas as pd
  import requests
  from bs4 import BeautifulSoup  # for scraping tasks
  import matplotlib.pyplot as plt  # for plotting tasks
  ```

> Tip: Keep one notebook per section; commit your work to GitHub as you complete each exercise.