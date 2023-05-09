---
id: p2locyg6z2ljn8wq7sl3499
title: Positioning
desc: ''
updated: 1678563144497
created: 1678557423572
---

Note: Containing Block: The Content boc of the parent container

## Basics of Positioning
- Positioning of elements can be done through the `position` properties
- 5 possible values for `position`
- Default: `position: static`
- Note that `position: static` do not have access to the properties `top`, `right`, `left`, `bottom` & `z-index`
  

``` css
selector {
    position: absolute;
    position: relative;
    position: fixed;
    position: sticky;
    position: static;
}
```
<br>

### `position: absolute`
- When used, brings the item out of the flow of the document
- By default, positions the item relative to the `<html>` document.
- However, it can be positioned relative to a parent container by setting `position: relative` for the parent container. 
- How it works is that it will scan through all its parents container for  `position: relative`. If none has that property, it will position itself relative to the `<html>` document.
- To move the element, we use other properties such as `top`, `right`, `left` & `bottom`


``` css
/* Use one of each top/bottom & left/right */
item {
    position: absolute;
    top: -50px;
    right: 0px;
    overflow-x: hidden; /* Used to disable horizontal scrolling */
    z-index: -1 /* Move to back */
}

/* Can be used to resize items */
/* Scretches the item to cover the left half of the page */
item {
    position: absolute;
    top: 0px;
    bottom: 0px;
    left: 0px;
    right: 50%;
}
```

### `position: relative`
- To move the element, we use other properties such as `top`, `right`, `left` & `bottom`
- Positions the element relative of its direct parent container 


### `position: fixed`
- To move the element, we use other properties such as `top`, `right`, `left` & `bottom`
- Position of element is always relative to the viewport which is the user's visible area of a web page
- Remains in the same position despite scrolling
- Examples of usage include headers and footers
- If not working, it could be due to being fixed in its parent container


### `position: sticky`
- Similar behaviour to `position: fixed`
- Only start working when `top` is used which defines the space allowance before the element starts moving (to maintain the same position in the page)
- However, its movement is only limited to within the parent container
- If item is not sticking, it might be due to the height of the item stretching to fit its parent container
  


