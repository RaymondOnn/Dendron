---
id: v9nhpg0uh5mbosx6ftf3d5d
title: testing
desc: ''
updated: 1715686182400
created: 1701681081530
---
https://www.youtube.com/watch?v=Xu5EhKVZdV8&t=6s



### Purpose of Unit Testing 
- Assert that the Return Value is as expected
- Change in State
- Interaction with other objects

> ### Laws of Unit Testing
> 1. Always use interfaces
> 2. Use dependency injection
> 3. Only mock/fake the interfaces
> 4. Only touch what is yours

### Types of Test Doubles
- Dummy
  - Objects that can be passed around as necessary but do not have any type of test implmentation nd shoud never be used
- Fake
  - These objects generally have a simplified function implementation of a particular interface that is adequate for testing but not for production
- Stub: Objects that provide implementations with canned answers that are suitable for the test
- Spies: Objects that provide implementations that record the values that were passed in so they can be used by the test
- Mocks: Objects are pre-programmed to expect specific calls and parameters and can throw exceptions when necessary