---
id: jm4tavymkq43i2wmr0ri8uf
title: Dropdown
desc: ''
updated: 1666430740135
created: 1666428458266
---
Link: https://dev.to/asyraf/how-to-add-dropdown-in-markdown-o78

## The default case
```html
<details>
<summary>How do I dropdown?</summary>
<br>
This is how you dropdown.
</details>
```

## Moving beyond the usual dropdown
- Starting with dropdown **open** by default

```html
<details open>
<summary>I automatically open</summary>
<br>
Waaa, you see me. I thought I would be hidden ;p .
</details>
```

- Add space between the `<summary>` tag with the dropdown content.

```html
<details>
<summary>Heading</summary>
<!--All you need is a blank line-->

    + markdown list 1
        + nested list 1
        + nested list 2
    + markdown list 2
</details>
```
- Nesting the dropdown

```html
<details>
<summary>Well</summary>

<details>
<summary>Try this</summary>

 <details>
 <summary>The other one</summary>

   <details>
   <summary>Ok, try this</summary>
   You got me 😂
   </details>
 </details>
</details>
</details>
```
- Make collapsible Content

```html
<div class="container">
    <canvas id="myChart"></canvas>
</div>
```
...and the js portion
```js
var data = {{charJSON | safe}}
var ctx = document.getElementById("myChart").getContext('2d');
var myChart = new Chart(ctx, data);
```

