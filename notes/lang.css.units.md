---
id: r2lfq6wbxz0mzc29q5bde6e
title: Units
desc: ''
updated: 1677323320329
created: 1677318015611
---

### Selecting the right units?
- Font-size: `rem`
- Width: `%` along with `max-width` / `ch`
- Height: try setting `min-height`, else `%` / `rem` / `vh`
- Padding / Margins: `em` / `rem`
- Media Queries: `em`
- Little things e.g. shadows, borders: `px`

<br>

### The Pixel, `px`
- The anchor unit for screen media
- measurement of length that is **relative** to the **expected viewing distance of a device** being used.
- Hence, pixels are still relative to the viewing device. Different devices have different expected viewing distances
- http://alistapart.com/article/a-pixel-identity-crisis/

<br>

### Why use relative units?
Setting everything in `px` is a bad practice as it overwrites people's custom screen settings for accessibility reasons

<br>

### The Typography units: `em` / `rem` 
#### The `em` unit
  - `font-size`: relative to `font-size` of parent container
  - other properties: relative to `font-size` of current element it is assigned to

Note: Using it on multiple objects can result in a whole mess of dependencies, making things hard to track and edit. Hence, the use of the `rem` unit

#### The `rem` unit
- relative to `font-size` of `<html>` element

<br>

### The Percentages: `%` / `vw` / `vh`
- `%`: relative to the parent container
- `vw`: relative to the viewport width
- `vh`: relative to the viewport height

<br>

### The `ch` unit
- The width of the character '0' of your font
- Rule of thumb: paragraph width should be between 45 - 75 characters wide