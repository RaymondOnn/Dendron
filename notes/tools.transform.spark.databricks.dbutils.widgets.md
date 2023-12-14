---
id: uyzb1ahbqx2s7ow3zfeqcnk
title: Widgets
desc: ''
updated: 1666439656341
created: 1666323247886
---

## Add notebook parameters with widgets
Use <a href="https://docs.databricks.com/notebooks/widgets.html" target="_blank">widgets</a> to add input parameters to your notebook.

<details>
<summary>Using SQL</summary>
Create a text input widget using SQL.

```sql
%sql
CREATE WIDGET TEXT state DEFAULT "CA"
```

Access the current value of the widget using the function `getArgument`
```sql
%sql
SELECT *
FROM events
WHERE geo.state = getArgument("state")
```

Remove the text widget
```sql
%sql
REMOVE WIDGET state
```

</details>

<details>
<summary>Using Python, Scala, and R</summary>

To create widgets in Python, Scala, and R, use the DBUtils module: dbutils.widgets

```python
%python
dbutils.widgets.text("name", "Brickster", "Name")
dbutils.widgets.multiselect("colors", "orange", ["red", "orange", "black", "blue"], "Favorite Color?")
```
Access the current value of the widget using the dbutils.widgets function `get`

```py
%python
name = dbutils.widgets.get("name")
colors = dbutils.widgets.get("colors").split(",")

html = "<div>Hi {}! Select your color preference.</div>".format(name)
for c in colors:
    html += """<label for="{}" style="color:{}"><input type="radio"> {}</label><br>""".format(c, c, c)

displayHTML(html)
```
Remove all widgets

```python
%python
dbutils.widgets.removeAll()
```

</details>