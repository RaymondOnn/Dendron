---
id: irf9ur33kqdhhr9ee6d0hlj
title: Box
desc: ''
updated: 1679794691403
created: 1677332698725
---

## The BOX Model
- Borders
- Margins: space lining the outside of borders
- Padding: space lining the inside of borders

TIP: To differentiate between margins and paddings, we can use `background-color`. 
  - `background-color` includes paddings BUT NOT margins

<br>

### Margins
- Space around an element, outside of any defined borders and effectively pushing away from everything else
- Margins of two adjacent objects overlap / collapse. Final margin-width is the maximum of the two margins: max(margin-bottom of top box, margin-top of bottom box)
- Properties available: 
  - `margin-top`, 
  - `margin-bottom`, 
  - `margin-right`, 
  - `margin-left`
  - `margin-<inline/block>`
  - `margin-<inline/block>-<start/end>`
- `margin` : shorthand property that refers to all four margin properties

All the margin properties can have the following values:
- `auto` - The element will then take up the all available width. 
  - If both sides are specified, the available space will be split equally between the left and right margins. Can be used to horizontally center the element within its container. 
  - For top/bottom margins, margins will be set to 0.
- length - specifies a margin in px, pt, cm, etc.
- `%` - specifies a margin in % of the width of the containing element
- inherit - specifies that the margin should be inherited from the parent element


```css
/* Tip: It goes in the order of the clock i.e. clockwise */
/* 4 values */
selector {
     margin: 25px 50px 75px 100px; /* top right bottom left */
}

/* 3 values */
selector {
     margin: 25px 50px 75px; /* top right-left bottom */
}

/* 2 values */
selector {
     margin: 25px 50px; /* top-bottom right-left */
}

/* 1 values */
selector {
     margin: 25px; /* all sides have same value */
}
```
<br>

#### Collapsing Margins
- When margins of adjacent objects touch, they merge and become one margin (for e.g the bottom margin of the top object **IS ALSO** the top margin of the bottom object)
- Final width of such margin: MAX(margin of one object, margin of other object)
- With `display:grid` and `display:flex`, margins no longer collapse.
- **Watch out**: When margin of the parent and the margin of the first child collapse into one. They touch when parent has no `padding-top` and start at the same spot.
- **Watch out**: By default, all text have margin!! 
- **Solution**: introduce a separation between margin through `padding-top: 1px` for the parent container.
- **Solution**: Start from scratch by turning all margins and paddings off and stick to using `margin-bottom` only
  ``` css
  * {
     margin: 0;
     padding: 0;
  }
  ```

<br>

### Padding
- Space around an element, inside of any defined borders
- Properties available: `padding-top`, `padding-bottom`, `padding-right`, `padding-left`
- `padding` : shorthand property that refers to all four margin properties


All the margin properties can have the following values:
- length - specifies a margin in px, pt, cm, etc.
- `%` - specifies a margin in % of the width of the containing element
- inherit - specifies that the margin should be inherited from the parent element


```css
/* 4 values */
selector {
     padding: 25px 50px 75px 100px; /* top right bottom left */
}

/* 3 values */
selector {
     padding: 25px 50px 75px; /* top right-left bottom */
}

/* 2 values */
selector {
     padding: 25px 50px; /* top-bottom right-left */
}

/* 1 values */
selector {
     padding: 25px; /* all sides have same value */
}
```

### Box-sizing
``` css
div {
  width: 300px;
  padding: 25px;
}
```
- Default behaviour: `box-sizing: content-box`
- Here, Actual width = width of content (300px) + width of left padding + width of right padding + width of left border + width of right border
``` css
div {
  width: 300px;
  padding: 25px;
  box-sizing: border-box;
}
```
- In order for actual width = stated width, we can use `box-sizing: border-box`

### Borders
- Properties available: 
  - `border-style` 
  - `border-width` 
  - `border-color`
  - `border-radius` : for adding rounded corners  
- `borders`: shorthand property that refers to all four border properties: 
    - `border-top` 
    - `border-right` 
    - `border-bottom`
    - `bottom-left`

<br>

```css
selector {
  border: 5px solid red; /* width style color */
}
```

```css
selector {
  border: 2px solid red;
  border-radius: 5px;
}
```
<br>

#### Border-style
- `border-style` is a required property
- None of the OTHER CSS border properties will have ANY effect unless the `border-style` property is set!

``` css
/* Keyword values */
border-style: none;
border-style: hidden;
border-style: dotted;
border-style: dashed;
border-style: solid;
border-style: double;
border-style: groove;
border-style: ridge;
border-style: inset;
border-style: outset;

/* top and bottom | left and right */
border-style: dotted solid;

/* top | left and right | bottom */
border-style: hidden double dashed;

/* top | right | bottom | left */
border-style: none solid dotted dashed;

/* Global values */
border-style: inherit;
border-style: initial;
border-style: revert;
border-style: revert-layer;
border-style: unset;
```