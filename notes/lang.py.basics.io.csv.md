---
id: ch5qfz6a59j5kxe7vuqiso1
title: csv
desc: ''
updated: 1748771521418
created: 1748767393407
---

https://medium.com/@ajaymaurya73130/10-powerful-things-you-didnt-know-python-s-csv-module-could-do-1577f577510d

### Detect File Format with `csv.Sniffer()`

- Automatically detects delimiters
- Saves time in manual checking
- Perfect for user-uploaded files

```python
import csv

with open('unknown.csv', 'r') as file:
    sample = file.read(1024)
    dialect = csv.Sniffer().sniff(sample)
    file.seek(0)
    reader = csv.reader(file, dialect)
    for row in reader:
        print(row)
```

### Check If a File Has a Header Row

- Great for dynamic ETL pipelines when working with inconsistent datasets

```python
has_headers = csv.Sniffer().has_header(sample)
print("Has header:", has_headers)
```

### Create Custom Dialects for Reusability

- Cleaner, reusable code
- Great for non-standard formats like |, ~, or custom delimiters

```python

csv.register_dialect('pipes', delimiter='|', quoting=csv.QUOTE_NONE)

with open('data.txt') as f:
    reader = csv.reader(f, dialect='pipes')
    for row in reader:
        print(row)
```

### Write CSVs with Field-Level Precision Using DictWriter

- You probably know csv.writer, but DictWriter gives you more control when writing rows with specific keys.
- Makes your code self-documenting
- Works well with dictionaries from APIs or databases

```python

with open('output.csv', 'w', newline='') as f:
    writer = csv.DictWriter(f, fieldnames=['name', 'age'])
    writer.writeheader()
    writer.writerow({'name': 'Alice', 'age': 30})
```

### Read Only Certain Columns Efficiently

- Sometimes you don’t need the whole file — just a few columns. Combine DictReader with smart indexing:
- Speeds up processing for large files
- Avoid unnecessary memory usage

```python

with open('employees.csv') as f:
    reader = csv.DictReader(f)
    for row in reader:
        print(row['email'], row['department'])
```

### Use Generator Expressions for Speed

Want to process a massive CSV without hogging memory? Pair the csv.reader() with a generator.

- Fast filtering
- Memory-safe pipelines

```python

with open('bigfile.csv') as f:
    reader = csv.reader(f)
    data = (row for row in reader if 'Manager' in row[2])
    for entry in data:
        print(entry)
```

### Escape Special Characters Easily

When writing data that includes commas or quotes, use quotechar and escapechar.
Ensures compatibility with Excel and other systems
Prevents corrupted files

```python

with open('safe.csv', 'w', newline='') as f:
    writer = csv.writer(f, quoting=csv.QUOTE_MINIMAL, quotechar='"')
    writer.writerow(['John', 'He said, "Hello!"'])
```

### Append to Existing CSVs Without Overwriting

- Sometimes, you want to add new data to an existing file.
- Always open with mode='a' and newline=''
- Useful for logging systems or audit trails

```python

with open('log.csv', 'a', newline='') as f:
    writer = csv.writer(f)
    writer.writerow(['2025-05-28', 'Login', 'Success'])
```

### Integrate with json for CSV ↔ JSON Conversion

- You can combine csv with json to convert formats easily:
- Makes your data portable
- Useful in APIs and web applications

```python

import json

with open('data.csv') as f:
    reader = csv.DictReader(f)
    data = list(reader)
with open('data.json', 'w') as f:
    json.dump(data, f, indent=4)
```

### Handle Unicode and Encoding Explicitly

- By default, Python uses system encoding. 
- Be explicit for safety:
- Use utf-8-sig when working with Excel
- Prevents character corruption

```python
with open('data.csv', encoding='utf-8') as f:
    reader = csv.reader(f)
```
