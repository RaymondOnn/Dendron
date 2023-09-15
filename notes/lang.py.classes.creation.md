---
id: pafqjuo8ixi3clzo3vcrjfa
title: Creation
desc: ''
updated: 1692225681580
created: 1692225394069
---
https://www.youtube.com/watch?v=-js0K7Q878c&t=331s&ab_channel=PyCascades

## Class creation in Python
- When the keyword `class` is encountered
    - the blody (statements and functions) of the class is isolated
    - the namespace dictionary of the class is created (but not populated)
    - the body of the class executes, the the namespace dictionary is populated with all the attributes, methods defined and additional useful info about the class
    - the metaclass is identified
    - the metaclass is then called with the name, bases and attributes of the class to instantiate it. 'type' is the default metaclass in Python