---
id: dxs2ibmvnbr8b188zjkhlsw
title: validators
desc: ''
updated: 1706973297020
created: 1706969071231
---

## Validator Functions
- for when dynamic values are needed for validating i.e. fetching the current datetime. 
- allows very flexible and dynamic validation of your data fields. 
- These validator methods are where complex field validation logic should occur.

``` py
import uuid
from datetime import date, datetime, timedelta
from pydantic import BaseModel, confloat, validator

class Student(BaseModel):
    id: uuid.UUID
    name: str
    date_of_birth: date
    GPA: confloat(ge=0, le=4)
    course: str | None
    department: str
    fees_paid: bool

    @validator('date_of_birth'). # <--- specify the field the validator is for
    def ensure_16_or_over(cls, value):
        sixteen_years_ago = datetime.now() - timedelta(days=365*16)

        # convert datetime object -> date
        sixteen_years_ago = sixteen_years_ago.date()
        
        # raise error if DOB is more recent than 16 years past.
        if value > sixteen_years_ago:
            raise ValueError("Too young to enrol, sorry!")
        return value
```


### Field Validators

- Field Validators are functions used to apply additional validations or transformations to fields before they are set in the model.
``` py
from pydantic import BaseModel, EmailStr, field_validator

class Owner(BaseModel):
    name: str
    email: EmailStr
    
    @field_validator('name')
    @classmethod
    def name_must_contain_space(cls, v: str) -> str:
        if ' ' not in v:  # added additional constraint
            raise ValueError('Owner name must contain a space')
        return v.title() # added transformation here

try:
    owner_instance = Owner(name="JohnDoe", email="john.doe@example.com")
except ValueError as e:
    print(e)
```    

### Model validators
- allows you to create a model before and after field validation
- Model Validators allow for validation at the model level, occurring after all the Field Validators have been executed. They can be performed both pre and post-field validation.

``` py
from typing import Any
from pydantic import BaseModel, EmailStr, ValidationError, model_validator

class Owner(BaseModel):
    name: str
    email: EmailStr
    
    @model_validator(mode='before') # mode='before' indicates fx runs before field validation
    @classmethod # <--- indicates fx runs before class instance exists
    def check_sensitive_info_omitted(cls, data: Any) -> Any:
        if isinstance(data, dict): # note attributes stored as dict
            if 'password' in data:
                raise ValueError('password should not be included')
            if 'card_number' in data:
                raise ValueError('card_number should not be included')
        return data
    
    @model_validator(mode='after') # mode='after' indicates fx runs after field validation
    def check_name_contains_space(self) -> 'Owner':
        if ' ' not in self.name:
            raise ValueError('Owner name must contain a space')
        return self


print(Owner(name="John Doe", email="john.doe@example.com")) 

try:
    Owner(name="JohnDoe", email="john.doe@example.com", password="password123")
except ValidationError as e:
    print(e) 
try:
    Owner(name="JohnDoe", email="john.doe@example.com")
except ValidationError as e:
    print(e)
```