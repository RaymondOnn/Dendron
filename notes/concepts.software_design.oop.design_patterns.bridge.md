---
id: cyv1tj457gfs0hx12tjnuex
title: Bridge
desc: ''
updated: 1680357354429
created: 1680356889788
---

### Bridge
- Decouples an abstraction from its implementation.
- Abstraction: A high level control layer
- Implementation: the layer that does the actual work
- For e.g.
  - Instead of 
    - AmericanRestaurant_ChickenPizza Class
    - ItalianRestaurant_ChickenPizza Class
    - AmericanRestaurant_BeefPizza Class
    - ItalianRestaurant_BeefPizza Class
  - Change it into
    - Pizza Class
      - Chicken 
      - Beef
    - Restaurant Class
      - Italian
      - American

<br>

#### Example

``` python
# ConcreteImplementor 1/2
class DrawingAPI1:
    def draw_circle(self, x, y, radius):
        print(f"API1.circle at {x}:{y} radius {radius}")


# ConcreteImplementor 2/2
class DrawingAPI2:
    def draw_circle(self, x, y, radius):
        print(f"API2.circle at {x}:{y} radius {radius}")


# Refined Abstraction
class CircleShape:
    def __init__(self, x, y, radius, drawing_api):
        self._x = x
        self._y = y
        self._radius = radius
        self._drawing_api = drawing_api

    # low-level i.e. Implementation specific
    def draw(self):
        self._drawing_api.draw_circle(self._x, self._y, self._radius)

    # high-level i.e. Abstraction specific
    def scale(self, pct):
        self._radius *= pct


if __name__ == "__main__":
    """
    shapes = (CircleShape(1, 2, 3, DrawingAPI1()), CircleShape(5, 7, 11, DrawingAPI2()))
    for shape in shapes:
    ...    shape.scale(2.5)
    ...    shape.draw()
    >>> API1.circle at 1:2 radius 7.5
    >>> API2.circle at 5:7 radius 27.5
```
Reference:
- https://refactoring.guru/design-patterns/bridge
- https://www.youtube.com/watch?v=88kAIisOiYs&list=PLlsmxlJgn1HJpa28yHzkBmUY-Ty71ZUGc&index=18