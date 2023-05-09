---
id: 59zsccugwmvl5h052o83tmz
title: Flexbox
desc: ''
updated: 1677921619451
created: 1677340026516
---


## Basics of Flexbox
- Typical used when we have a multiple child elements within a parent container
- To enable flexbox, we can set `display: flex` for the parent container and the direct child items become `flex` items
- Generally, the parent container sets the ground rules but the child items have the final say
- With flexbox
  - items want to be as small as possible while maintaining one line
  - When the display gets smaller and there is no room within the parent container, it is allowed to start wrapping its contents in order to become smaller
  
<br>

## Properties for the container
<br>

`flex-direction`
- configures the main axis and cross axis
- Default: `flex-direction: row`
- Here, the horizontal axis is the **main axis** and the vertical axis is c the secondary **cross axis**
- `flex-direction: column` switches the vertical axis as the main axis, horizontal axis as the cross axis

<br>

`justify-content`
- configures how items are aligned on the **main** axis
- Requires leftover space in order to work!!
- Default: `justify-content: flex-start`
- `justify-content: flex-start` : left align (if main axis going from left to right)
- `justify-content: flex-end` : right align
- `justify-content: center` : align items in the middle
- `justify-content: space-between` : space only between items are the same
- `justify-content: space-around` : space around items are the same
- `justify-content: space-evenly` : border-item and iter-items spacing are same

`align-items`
- configures how items are aligned on the **cross** axis
- Requires leftover space in order to work!!
- Default: `align-items: flex-start`
- `align-items: flex-start` : align to start of cross axis
- `align-items: flex-end` : align to the end of the cross axis
- `align-items: center` : align to the center of the cross axis
- `align-items: baseline` : align based on the baseline of text within the elements

`flex-wrap`
- configures whether elements should fit into one line or are allowed to spill over to another line when there is no more space available
- Default: `flex-wrap: nowrap` 
- `flex-wrap: nowrap` : tries to fit all elements into one line 
- `flex-wrap: wrap` : elements allowed to spill over to another line when there is no more space available
  - makes available the property `align-content`

`align-content`
- only works when `flex-wrap: wrap`
- similar to `align-items`, configures how items are aligned on the **cross** axis
- - Default: `justify-content: flex-start`
- `align-content: flex-start` : left align (if main axis going from left to right)
- `align-content: flex-end` : right align
- `align-content: center` : align items in the middle
- `align-content: space-between` : space only between items are the same
- `align-content: space-around` : space around items are the same
- `align-content: space-evenly` : border-item and iter-items spacing are same

`gap`
- introduces gaps between elements e.g. `gap:1em`

<br>

## Properties for the children within the container

<br>

`flex-grow`
- `flex-grow: <unitless proportion of leftover space>`
- equally distributing a proportion of leftover space among the items and allow them to grow to fit that space. A ratio of how each item is growing
- Default. `flex-grow: 0` i.e. do not grow despite leftover space

`flex-shrink`
- how fast one item shrink relative to the other items when adjusting to fit the display. A ratio of how each item is shrinking
- Default: `flex-shrink: 1` 
- if `flex-shrink: 0`, then the item will not shrink
- https://www.youtube.com/watch?v=fm3dSg4cxRI

`flex-basis`
- specifies the initial size of the flex item, **before** any available space is distributed according to the flex factors
- Overwrites the `width` property. Usually either `width` or `flex-basis` is used.
- Default: `flex-basis: auto`
- 

`flex`
- `flex: <flex-grow> <flex-shrink> <flex-basis`
- Shorthand for `flex-grow`, `flex-shrink` and `flex-basis` combined
- Default: `flex-basis: auto`

`align-self`
- an individual version of `align items` that targets a single item.
- Overwrites the "alignment" set in the parent container
- Default: `align-self: flex-start`
- `align-self: flex-start` : align to start of cross axis
- `align-self: flex-end` : align to the end of the cross axis
- `align-self: center` : align to the center of the cross axis
- `align-self: stretch` : stretch to fit

`order`