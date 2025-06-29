---
id: ga9knrdujy21nut470h618r
title: model
desc: ''
updated: 1749982390931
created: 1749981632473
---




### [Anti Patterns](https://blog.devgenius.io/avoiding-anti-patterns-in-data-modeling-build-data-that-doesnt-fight-you-back-25652714f8e6)

#### The One Table to Rule Them All

- One monstrous model with 50+ fields doing everything.

    ``` py
    class UserProfile(models.Model):
        first_name = models.CharField(...)
        last_name = models.CharField(...)
        shipping_address = models.TextField(...)
        billing_address = models.TextField(...)
        social_security_number = models.CharField(...)
        favorite_snack = models.CharField(...)
    ```

  - It started as a user profile. Now it’s a CRM, warehouse, tax form, and snack survey.

- Why It’s Bad:
  - Violates Single Responsibility Principle
  - Becomes impossible to reuse or extend
  - Changes become risky and slow
- What to Do Instead:
  - Break things down. Create separate models and use OneToOneField or ForeignKey.

    ``` py
    class Address(models.Model):
        user = models.ForeignKey(User, on_delete=models.CASCADE)
        type = models.CharField(choices=[('shipping', 'Shipping'), ('billing', 'Billing')])
        address = models.TextField()
    ```

#### Storing Lists as Comma-Separated Strings

``` py
class Product(models.Model):
    tags = models.CharField(max_length=255)  # "eco-friendly,organic,bestseller"
```

- Why It’s Bad:
  - Filtering becomes a nightmare (WHERE tags LIKE '%eco%')
  - Can’t enforce constraints
  - Searching and indexing are broken
- What to Do Instead:
  - Use a related table or a ManyToManyField.

    ``` py
    class Tag(models.Model):
        name = models.CharField(max_length=50)

    class Product(models.Model):
        tags = models.ManyToManyField(Tag)
    ```

#### Over-Normalizing Everything to Death

``` py
class PhoneNumberType(models.Model):
    name = models.CharField(...)  # 'Mobile', 'Work', 'Home'

class PhoneNumber(models.Model):
    user = models.ForeignKey(User, ...)
    number = models.CharField(...)
    type = models.ForeignKey(PhoneNumberType, ...)
```

Now you need 5 joins to get a user’s phone number.

- Why It’s Bad:
  - Over-engineering
  - Slows down queries
  - Makes the code harder to read and maintain
- What to Do Instead:
  - Normalize where it makes sense — denormalize for simplicity and speed if the values are static and few.

    ``` py
    class PhoneNumber(models.Model):
        user = models.ForeignKey(User, ...)
        number = models.CharField(...)
        type = models.CharField(choices=[('mobile', 'Mobile'), ('work', 'Work')])
    ```

#### Boolean Overload

``` py
class Order(models.Model):
    is_confirmed = models.BooleanField(default=False)
    is_packed = models.BooleanField(default=False)
    is_shipped = models.BooleanField(default=False)
    is_delivered = models.BooleanField(default=False)
```

You blinked — and now you’ve built a state machine from booleans.

- Why It’s Bad:
  - No validation between states
  - Easy to end up in impossible states (is_delivered=True but is_packed=False)
  - Messy and unreadable
- What to Do Instead:
  - Use an explicit status field with choices:

    ``` py
    class Order(models.Model):
        STATUS_CHOICES = [
            ('confirmed', 'Confirmed'),
            ('packed', 'Packed'),
            ('shipped', 'Shipped'),
            ('delivered', 'Delivered'),
        ]
        status = models.CharField(choices=STATUS_CHOICES, default='confirmed')
    ```

    Bonus: You can now validate transitions if needed.

#### Timestamp Neglect

You forgot `created_at` and `updated_at`. Again.

- Why It’s Bad:
  - Impossible to audit or debug
  - Can’t sort by recency
  - Lost opportunity for business insights
- What to Do Instead:
  - Always add timestamps.

    ``` py
    class TimeStampedModel(models.Model):
        created_at = models.DateTimeField(auto_now_add=True)
        updated_at = models.DateTimeField(auto_now=True)

        class Meta:
            abstract = True
    ```

    Now just inherit from it in every model.

#### Soft Deletes Without a Plan

You added a is_deleted field and called it a day. But now your admin shows deleted users, foreign keys explode, and nothing feels soft about this delete.

- Why It’s Bad:
  - Makes querying harder (filter(is_deleted=False) everywhere)
  - Foreign keys don’t respect it
  - Forgotten by future developers
- What to Do Instead:
  - Use custom managers or libraries like django-softdelete
  - Or create a consistent pattern:

    ``` py
    class SoftDeleteManager(models.Manager):
        def get_queryset(self):
            return super().get_queryset().filter(is_deleted=False)

    class MyModel(models.Model):
        is_deleted = models.BooleanField(default=False)

        objects = SoftDeleteManager()
        all_objects = models.Manager()  # includes deleted
    ```

#### Repeating Yourself… Again and Again

If you copy-paste the same fields (name, slug, is_active) in 10 models, your models are crying for help.

- The Fix:
  - Use abstract base models.
``` py
class NamedModel(models.Model):
    name = models.CharField(max_length=255)
    slug = models.SlugField(unique=True)

    class Meta:
        abstract = True
```
Boom. Clean, reusable, readable.
