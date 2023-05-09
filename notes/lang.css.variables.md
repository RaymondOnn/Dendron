---
id: nvus9wdal2zxrq594hcxtqk
title: Variables
desc: ''
updated: 1677944976594
created: 1677937741923
---
## Why Use Them?
- Variables help to consolidate the control to one area of the code
- Also make things easier to maintain consistency

<br>

## Defining Variables
- By assigning to the root, it makes the variables global 
- "--" indicates that this property is a custom property
``` css

/* By assigning to the root, it makes the variables global */
:root {
    --primary-clr: white;    /* "--" indicates that this is a custom property */
}
```

## Using Variables

``` css
/* var() indicates that it is a variable */
body {
    color: var(--primary-clr);
    background-color: var(--primary-clr, yellow); /* you can add a fallback option */
}

/* variables can be overwritten by same variable in a more "localized" scope */
/* However, this defeats the point of having variables and not a good practice */
body > div {
    --primary-clr: green;
    color: var(--primary-clr);    
}

    /* Cool Trick: overwrite a variable but assign it to a particular class id if you want different treatment for one of a group of similar elements*/
.stand-out {
    --primary-clr: red;
}
```

## Changing Variables using JavaScript