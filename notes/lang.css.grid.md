---
id: skcwkx6ifms3szqrek4hqs6
title: Grid
desc: ''
updated: 1677939773803
created: 1677922583136
---

## Basics of Grid

- To enable grid, we can set `display: grid` for the parent container 
- Generally, the parent container sets the ground rules but the child items have the final say
- With flexbox
  - items want to be as small as possible while maintaining one line
  - When the display gets smaller and there is no room within the parent container, it is allowed to start wrapping its contents in order to become smaller
  
<br>

## Defining the Grid

``` css
/* Creating grid of 2 rows of 100px and 2 columns of 100px */
container {
    grid-template-columns: 100px 100px 100px 100px
    grid-template-rows: 100px 100px 100px 100px /* not necessary */
}

/* We can also specify using fractional units */
container {
    grid-template-rows: 100px 100px 100px 100px
    grid-template-columns: 1fr 1fr 1fr 1fr
}

/* We can use functions to make things easier */
container {
    grid-template-rows: repeat(4, 100px)
    grid-template-columns: 1fr 1fr 1fr minmax(100px, 1fr)
}

/* Adding gaps in grid */
container {
    grid-gap: 1em;      --> row and columns gaps are all 1em wide
    grid-gap: 1em 3em;  --> first value for rows, 2nd value for columns
    gap : 1em           --> row and columns gaps are all 1em wide
}

/* Assigning names to zones in grid */
container {
    grid-template-areas:
        'header header'
        'main aside'
        'footer footer'
}

/* Dealing with unplanned grids*/
container {
    grid-auto-rows: 100px 
    grid-auto-flow: columns 
}

grid-auto-columns: 100px /* Extra columns will be 100 px tall */
```

## Arranging Items
``` css
/* Dealing with unplanned grids*/
/* Justify-items: arranging item on the horizontal axis*/
/* align-items: arranging items on the vertical azis*/
container { 
    justify-items : start /* possible values: start / end / baseline / center */
    align-items: columns 
}
```

## Positioning Individual Items

To position an item:
``` css
/* To position an item by specifying all four points */
.item-1 {
    grid-row-start: 1;
    grid-row-end: 3;
    grid-column-start:1;
    grid-column-end:5;
}

/* Alternatively we can use shorthand properties */
.item-1 {
    grid-row: 1 / span 2 ;
    grid-column:1 / 5;
}

/* Using an even better shorthand property */
.item-1 {
    grid-area: 1 / 1 / 3 / 5;
}

/* We can just specify the height & width of the item */
/* However, here position is relative at best */
/* Useful if items contain more than 1 grid */
.item-1 {
    grid-row: span 2 ; /* 2 units wide */
    grid-column: span 2; /* 2 units tall */
}

.item-1 {
    
}
```
`z-index` can be use to config which item is at the top / bottom
## Individual Items within the grid
``` css
/* To reference to a zone */
.item-1 {
    grid-area: header;
}
```