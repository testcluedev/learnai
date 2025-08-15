# Solutions — Hands-on Python & Data Science Practice (30 Exercises)

> These are sample solutions. There are many valid ways to solve each task.

---

## Section 1 — Core Python & Strings (1–6)

### 1) Hello Variables (f-strings)
```python
name, age, city = "Pavel", 33, "Prague"
print(f"Hi, I'm {name}, {age}, from {city}.")
```

### 2) Temperature Converter
```python
def convert_temp(value, scale):
    if scale == "C":
        return (value * 9/5) + 32
    elif scale == "F":
        return (value - 32) * 5/9
    else:
        raise ValueError("scale must be 'C' or 'F'")

print(convert_temp(0, "C"))   # 32.0
print(convert_temp(32, "F"))  # 0.0
```

### 3) String Reversal & Palindrome
```python
import re

def is_palindrome(s):
    s = re.sub(r"[^a-z0-9]", "", s.lower())
    return s == s[::-1]

print(is_palindrome("Never odd or even"))  # True
```

### 4) Substring Finder
```python
text = "Hello amazing world"
word = "amazing"
print(text.find(word))  # 6
```

### 5) Table Formatting
```python
items = [("Apple", 1.2), ("Banana", 0.8), ("Kiwi", 2.1)]
print(f"{'Product':<10}{'Price':>8}")
for name, price in items:
    print(f"{name:<10}{price:>8.2f}")
```

### 6) Word Frequency Counter
```python
import re
from collections import Counter

text = "To be, or not to be: that is the question."
clean = re.sub(r"[^a-z0-9\s]", "", text.lower())
counts = Counter(clean.split())
print(counts.most_common(5))
```

---

## Section 2 — Data Structures & Control Flow (7–12)

### 7) Unique Words (Set)
```python
paragraph = "This is a test. This test is only a test."
unique = sorted(set(paragraph.lower().split()))
print(unique)
```

### 8) Grocery List Manager
```python
groceries = []

def add_item(item):
    groceries.append(item)

def remove_item(item):
    try:
        groceries.remove(item)
    except ValueError:
        print("Item not in list")

def list_items():
    return groceries[:]

add_item("milk"); add_item("eggs"); remove_item("bread"); print(list_items())
```

### 9) Student Grades (Dictionary)
```python
grades = {"Ana": [80, 90], "Ben": [75, 85, 95], "Cara":[100]}
averages = {name: sum(vals)/len(vals) for name, vals in grades.items()}
class_avg = sum(averages.values()) / len(averages)
print(averages, class_avg)
```

### 10) Tuple Unpacking
```python
points = [(1,2), (3,4), (5,6)]
distances = [ (x**2 + y**2) ** 0.5 for (x,y) in points ]
print(distances)
```

### 11) Even & Odd Sorter
```python
nums = [1,2,3,4,5,6,7,8,9,10]
evens = [n for n in nums if n % 2 == 0]
odds  = [n for n in nums if n % 2 != 0]
print(evens, odds)
```

### 12) Prime Checker
```python
def is_prime(n):
    if n < 2:
        return False
    i = 2
    while i * i <= n:
        if n % i == 0:
            return False
        i += 1
    return True

print(is_prime(29))  # True
```

---

## Section 3 — Functions, OOP & Exceptions (13–18)

### 13) Simple Calculator
```python
def add(a,b): return a+b
def subtract(a,b): return a-b
def multiply(a,b): return a*b
def divide(a,b):
    if b == 0:
        raise ZeroDivisionError("b cannot be zero")
    return a/b

# Example
print(add(2,3), divide(10,2))
```

### 14) Factorial (Recursion)
```python
def factorial(n):
    if not isinstance(n, int) or n < 0:
        raise ValueError("n must be int >= 0")
    if n in (0,1):
        return 1
    return n * factorial(n-1)

print(factorial(5))  # 120
```

### 15) Bank Account Class
```python
class BankAccount:
    def __init__(self, initial=0.0):
        if initial < 0: raise ValueError("Initial balance cannot be negative")
        self._balance = float(initial)

    def deposit(self, amount):
        if amount <= 0: raise ValueError("Deposit must be positive")
        self._balance += amount

    def withdraw(self, amount):
        if amount <= 0: raise ValueError("Withdraw must be positive")
        if amount > self._balance: raise ValueError("Insufficient funds")
        self._balance -= amount

    @property
    def balance(self):
        return self._balance

acct = BankAccount(100)
acct.deposit(50); acct.withdraw(30); print(acct.balance)  # 120.0
```

### 16) Shapes OOP
```python
import math

class Shape:
    def area(self):
        raise NotImplementedError

class Circle(Shape):
    def __init__(self, r):
        if r <= 0: raise ValueError("r must be > 0")
        self.r = r
    def area(self):
        return math.pi * self.r ** 2

class Rectangle(Shape):
    def __init__(self, w, h):
        if w <= 0 or h <= 0: raise ValueError
        self.w, self.h = w, h
    def area(self):
        return self.w * self.h

class Triangle(Shape):
    def __init__(self, b, h):
        if b <= 0 or h <= 0: raise ValueError
        self.b, self.h = b, h
    def area(self):
        return 0.5 * self.b * self.h

print(Circle(2).area(), Rectangle(3,4).area(), Triangle(3,6).area())
```

### 17) File Reader with Exception Handling
```python
import os

def read_file(path):
    try:
        with open(path, "r", encoding="utf-8") as f:
            return f.read()
    except FileNotFoundError:
        return "File not found"

print(read_file("missing.txt"))
```

### 18) Safe Division Function
```python
def safe_divide(a, b):
    try:
        return a / b
    except ZeroDivisionError:
        return "Cannot divide by zero"

print(safe_divide(10, 0))
```

---

## Section 4 — NumPy, Pandas, Series & Time Series (19–24)

### 19) NumPy Array Statistics
```python
import numpy as np

arr = np.array([3,7,2,9,5])
print(np.sum(arr), np.mean(arr), np.median(arr), np.std(arr))
```

### 20) Matrix Multiplication
```python
A = np.array([[1,2,3],[4,5,6]])
B = np.array([[7,8],[9,10],[11,12]])
C = A @ B
print(C)  # shape (2,2)
```

### 21) Pandas Series Basics
```python
import pandas as pd

s = pd.Series({"Cisco": 52.3, "Palo Alto": 300.5, "Fortinet": 69.2})
print(s.min(), s.max(), s.mean())
print(s["Palo Alto"])
```

### 22) Time Series — Resample
```python
rng = pd.date_range("2024-01-01", periods=10, freq="D")
vals = np.random.randint(1, 10, size=10)
ts = pd.Series(vals, index=rng)
print(ts.resample("W").sum())
```

### 23) Read CSV and Filter
```python
url = "https://raw.githubusercontent.com/mwaskom/seaborn-data/master/tips.csv"
df = pd.read_csv(url)
res = df[df["total_bill"] > 30]
print(res.head())
```

### 24) GroupBy Aggregation
```python
url = "https://raw.githubusercontent.com/mwaskom/seaborn-data/master/tips.csv"
df = pd.read_csv(url)
print(df.groupby("day")["tip"].mean())

print(df.groupby("sex")["tip"].mean())

print(df.groupby(["day","time"])["tip"].mean().reset_index())
```

---

## Section 5 — Web APIs & Web Scraping (25–30)

### 25) Simple API Call (JSON)
```python
import requests

url = "https://api.github.com/repos/pandas-dev/pandas"
r = requests.get(url, timeout=15)
r.raise_for_status()
data = r.json()
print(data["full_name"], data["stargazers_count"], data["open_issues"])
```

### 26) Currency Conversion via API
```python
import requests

url = "https://api.exchangerate.host/latest?base=USD&symbols=EUR"
rate = requests.get(url, timeout=15).json()["rates"]["EUR"]
amount_usd = 100
print(amount_usd * rate)
```

### 27) Weather API (No API key)
```python
import requests

url = "https://api.open-meteo.com/v1/forecast?latitude=50.08&longitude=14.44&current_weather=true"
data = requests.get(url, timeout=15).json()
cw = data["current_weather"]
print(cw["temperature"], cw["windspeed"])
```

### 28) Basic Web Scraper — Quotes
```python
import requests, csv
from bs4 import BeautifulSoup

url = "https://quotes.toscrape.com/"
soup = BeautifulSoup(requests.get(url, timeout=15).text, "html.parser")
rows = []
for q in soup.select("div.quote"):
    text = q.select_one("span.text").get_text(strip=True)
    author = q.select_one("small.author").get_text(strip=True)
    rows.append((text, author))

with open("quotes.csv", "w", newline="", encoding="utf-8") as f:
    w = csv.writer(f)
    w.writerow(["quote", "author"])
    w.writerows(rows)
print("Saved quotes.csv")
```

### 29) Image Downloader — Books to Scrape
```python
import os, requests
from urllib.parse import urljoin
from bs4 import BeautifulSoup

base = "https://books.toscrape.com/"
soup = BeautifulSoup(requests.get(base, timeout=15).text, "html.parser")
os.makedirs("images", exist_ok=True)

for img in soup.select("img"):
    src = img.get("src")
    if not src:
        continue
    url = urljoin(base, src)
    fname = os.path.join("images", os.path.basename(src))
    r = requests.get(url, timeout=20)
    with open(fname, "wb") as f:
        f.write(r.content)
print("Downloaded images to images/")
```

### 30) Time Series from CSV
```python
import pandas as pd
import matplotlib.pyplot as plt

url = "https://raw.githubusercontent.com/jbrownlee/Datasets/master/daily-min-temperatures.csv"
df = pd.read_csv(url, parse_dates=["Date"], index_col="Date")
print(df.head())

df["Temp"].plot(title="Daily Min Temperatures")
plt.xlabel("Date"); plt.ylabel("Temp"); plt.tight_layout()
plt.savefig("daily_min_temps.png")  # or plt.show()

monthly = df.resample("M").mean()
print(monthly.head())
```

---

**Note:** For scraping, always respect `robots.txt` and website terms of use. Limit request rates and cache when possible.