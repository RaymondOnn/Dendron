---
id: xipfup7qof76xrkqvm3a87c
title: settings
desc: ''
updated: 1712381201435
created: 1706969010205
---


### Settings Management

- Pydantic Settings provides optional Pydantic features for loading a settings or config class from environment variables or secrets files.
- Pydantic enables the loading and validation of settings from environment variables, simplifying the configuration of your applications.
``` py
!pip install pydantic-settings
import os
from pydantic import Field
from pydantic_settings import BaseSettings


class Settings(BaseSettings):
    auth_key: str = Field(validation_alias='my_auth_key')  
    api_key: str = Field(alias='my_api_key')  
    

print(Settings().model_dump())
import os
from pydantic import Field, AliasChoices
from pydantic_settings import BaseSettings

os.environ["AUTH_KEY"] = "test_auth_key"
os.environ["MY_API_KEY"] = "test"
os.environ["ENV2"] = "https://mysuperurl.com"


class Settings(BaseSettings):
    service_name: str = Field(default="default")
    auth_key: str  
    api_key: str = Field(alias='my_api_key')  
    url: str = Field(validation_alias=AliasChoices("env1", "env2"))
     

print(Settings().model_dump())
```
- Pydantic settings looks for values in this order
  1. Environment variables
  2. `.env` file

    ``` py
    import os
    from pydantic import Field
    from pydantic_settings import BaseSettings, SettingsConfigDict

    # Set environment variables with the prefix
    os.environ["PRODUCTION_AUTH_KEY"] = "test_auth_key"
    os.environ["PRODUCTION_MY_API_KEY"] = "test"
    os.environ["PRODUCTION_ENV2"] = "https://mysuperurl.com"


    class Settings(BaseSettings):
        model_config = SettingsConfigDict(env_prefix='production_')

        service_name: str = Field(default="default")
        auth_key: str
        api_key: str = Field(alias='my_api_key')
        url: str = Field(validation_alias=AliasChoices("env1", "env2"))

    print(Settings().model_dump())
    ```
- You can also use a .env file
    ``` py
    from pydantic_settings import BaseSettings, SettingsConfigDict


    class Settings(BaseSettings):

        model_config = SettingsConfigDict(env_file='.env', env_file_encoding='utf-8')

        service_name: str = Field(default="default")
        auth_key: str  
        api_key: str = Field(alias='my_api_key')  
        

    print(Settings().model_dump())
    ```

#### Extra attributes (https://docs.pydantic.dev/2.3/usage/model_config/#extra-attributes)

``` py
from pydantic_settings import BaseSettings, SettingsConfigDict


class Settings(BaseSettings):

    model_config = SettingsConfigDict(env_file='.env', env_file_encoding='utf-8', extra="ignore") # forbid, allow

    service_name: str = Field(default="default")
    auth_key: str  
    api_key: str = Field(alias='my_api_key')  
     

print(Settings().model_dump())
```