---
id: 0a3f9sjv9z89e4gaa63tm76
title: Selectors
desc: ''
updated: 1677919573347
created: 1677323520595
---

## Basic Selectors

- The Universal Selector
``` css
* {
    background-color: red;
}
```
- type selector: select individual element e.g <div>
``` css
div {
    background-color: red;
}
```
- class selector: select based on class attribute
  
Suppose you have this in your html script
``` html
<div class="red">
    ...
</div>
```  
``` css
.red {
    background-color: red;
}
```
- id selector: `#red` 
  - generally not too useful since there can only be one such id on a page
``` html
<div id="red">
    ...
</div>
```  
``` css
.red {
    background-color: red;
}
```

<br>

## Combining selectors
- Select elements that are  `<div>` **AND** `class="red"` : `div.red` 
- Select elements that `<div class="red">` elements **OR** `<span>` elements : `div.red, span`
- Select `<li>` elements that are **INSIDE** of a `<ul>` element : `ul li`
- Select `<li>` elements that are **CHILD** of a `<ul>` element : `ul > li`
- Select `<li>` elements that are **AFTER AND SIBLING** of a `<li class="red">` element : `li.red ~ li`
- Select `<li class="green">` elements that are **DIRECTLY AFTER AND SIBLING** of a `<li class="red">` element : `li.red + li.green`

<br>

## Pseudo Class Selectors
### Based on States
- Elements that are `<li>` elements **WHEN** we hover over: `li:hover`
- Elements that are `<input>` elements **WHEN** we click to focus: `input:focus`
- Elements hat are `<input>` elements with `required` attribute: `input:required`
- Elements hat are `<input>` elements with `disabled` attribute: `input:disabled`
- `<input type="checkbox">` elements **WHEN** checked: `input:checked`
### Based on Parent-Child
- Elements that are `<li>` elements AND nth item inside a parent container: `li:first-child` / `li:nth-child(x)`/ `li:nth-last-child` / `li:last-child` /`li:only-child` 
- `li:last-of-type` / `li:nth-last-of-type` / `li:nth-of-type` / `li:only-of-type`

### Boolean: NOT()
- li:not(.green)

<br>

## Pseudo Elements

`div.red::before` / `div.red::after`

<br>

## Attribute Selectors
- Elements with `data` attributes: `[data]`
- Elements with `data="red"` attributes: `[data="red"]`
- Elements with value of `data` **BEGINS WITH** "x" : `[data^="x"]`
- Elements with value of `data` **ENDS WITH** "x" : `[data$="x"]`
- Elements with value of `data` **CONTAINS** "x" : `[data*="x"]`