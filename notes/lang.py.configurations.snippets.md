---
id: 2dxe977285nwc0rfwy9k5up
title: snippets
desc: ''
updated: 1701415008031
created: 1700754102604
---
### Config Class Examples

``` py
# loaded config 
conf = { 
         key1: val1,
         key2: val2,
         ...
         keyn : valn
}

class Config(object):
    def __init__(self):
        self._config = conf # set it to conf

    def get_property(self, property_name):
        if property_name not in self._config.keys(): # we don't want KeyError
            return self._config.get(property_name) # If key doesn't exist, it return None by default.

class MongoConfig(Config):

    @property
    def host(self):
        return self.get_property('MONGO_HOST')

    @property
    def port(self):
        return self.get_property('MONGO_PORT')

    @property
    def username(self):
        return self.get_property('MONGO_USERNAME')

    @property
    def db_name(self):
        return self.get_property('MONGO_DB_NAME')

    @property
    def password(self):
        return self.get_property('MONGO_PASSWORD')

class StudentConfig(Config):
    pass

class TeacherConfig(Config):
    pass        


# Example of using the config class
def get_collection(collection_name):
    configuration = config.MongoConfig()
    mongo_client = pymongo.MongoClient(host=configuration.host, port=configuration.port)
    db = mongo_client[configuration.db_name]
    if configuration.username is not None:
        db.authenticate(configuration.username, password=configuration.password)
    return db[collection_name]
```

### The Singleton Pattern

- This allows the user to create an instance of the Config class, pointing it to a specific config file, which it loads into memory.
- The principle is to define one configuration file location, and be done with it. The configuration file is JSON formatted, but the pattern can be adapted to use any format.
The Config class provides several static methods for accessing configuration options from the config file. Here's an example of its usage:
``` py
Config('/path/to/config.json')

if Config.get_foo() == "bar":
    do_stuff()
```

The Config class can also be used to wrap and process both variables in the configuration file and environment variables.

The Config class implements a separation of concerns by only processing top-level configuration variable options, and leaving more detailed configuration file parsing to the classes that need it. This allows for more flexible config files.

The Singleton pattern involves the use of instance variables, which the variables _CONFIG_FILE and _CONFIG are. These are shared across all instances of class Config and can be accessed via Config._CONFIG_FILE, etc.

- Aside from the constructor, every method in the Config class is a @staticmethod or a @classmethod.

``` py
class Config(object):

    #########################
    # Begin Singleton Section
    #########################

    _CONFIG_FILE: typing.Optional[str] = None
    _CONFIG: typing.Optional[dict] = None

    # Required config file path to initialize config object 
    # config file name can also be provided via the CONFIG_FILE environment variable. 
    def __init__(self, config_file = None):
        if config_file is None:
            config_file = Config.get_required_env_var("CONFIG_FILE")

        # Check that specified config file exists
        assert os.path.exists(config_file)

        # Use singleton pattern to store config file location/load config once
        Config._CONFIG_FILE = config_file
        with open(config_file, 'r') as f:
            Config._CONFIG = json.load(f)

    @staticmethod
    def get_config_file() -> str:
        return Config._CONFIG_FILE

    @staticmethod
    def get_required_env_var(envvar: str) -> str:
        if envvar not in os.environ:
            raise ConfigException(f"Please set the {envvar} environment variable")
        return os.environ[envvar]

    @staticmethod
    def get_required_config_var(configvar: str) -> str:
        assert Config._CONFIG
        if configvar not in Config._CONFIG:
            raise Exception(f"Please set the {configvar} variable in the config file {Config._CONFIG_FILE}")
        return Config._CONFIG[configvar]

# It can be useful to clear out an existing config file in order to load a new config file - specifically, when testing. 
# This could be done more gracefully by iterating over each attribute of the Config class and only nullifying those attributes whose variable name matches the given pattern (start with an underscore, only contain capital letters and underscores) using a regular expression.

    @classmethod
    def reset(cls) -> None:
        cls._CONFIG_FILE = None
        cls._CONFIG = None
        cls._FOO = None
        cls._BAR = None
        cls._WUZ = None
```


Config Functions

{
    "foo": "hello world"
}

Similarly, the barvariable is set using the environment variable BAR. If the BAR variable is not set, the program will raise an exception when Config.get_bar_var() is called.
``` py
class Config(object):

    ...see singleton section above...

    #############################
    # Begin Configuration Section
    #############################

    _FOO: typing.Optional[str] = None
    _BAR: typing.Optional[str] = None

    @classmethod
    def get_foo_var(cls) -> str:
        """Example variable that is set in the config file (preferred)"""
        if cls._FOO is None:
            cls._FOO = Config.get_required_config_var('foo')
        return cls._FOO

    @classmethod
    def get_bar_var(cls) -> str:
        """Example variable that is set via env var (not preferred)"""
        if cls._BAR is None:
            cls._BAR = Config.get_required_env_var('BAR')
        return cls._BAR

    @classmethod
    def get_wuz(cls) -> str:
        if cls._WUZ is None:
            if 'wuz' not in cls._CONFIG:
                cls._WUZ = Config.get_required_env_var('WUZ')
            else:
                cls._WUZ = cls._CONFIG['wuz']
        if not os.path.isdir(cls._WUZ):
            raise Exception(f"Error: Path {cls._WUZ} is not a directory")
        return cls._WUZ
```
The wuz variable, in this example, is a variable that can be set with a config file variable, or (if it is not present in the config file) with an environment variable. The wuz variable msut also be a path, so there is logic for checking whether the path exists.


#### Creating a configuration context manager
To make tests more convenient, we define a context manager that takes a dictionary as an input. The context manager creates a temporary file with the contents of that dictionary, and resets the Config class using the temporary file as the new config file. This allows tests to be written using different configurations on the fly, very useful when testing different configuration options:

cfg = {"peanut_butter": "jelly"}
with TempConfig(cfg) as config_file:
    print(f"Temporary configuration file is at {config_file}")
    val = Config.get_required_config_var("peanut_butter")
    assert val=="jelly"
Here is the context manager class to temporarily replace the configuration wrapped by the Config class:
``` py
class TempConfig(object):
    """
    Temporarily patch the Config class to use the config
    dictionary specified in the constructor.
    """

    def __init__(self, config_dict, *args, **kwargs):
        """This is the step that's run when object constructed"""
        super().__init__()
        # This is the temp configuration the user specified
        self.config_dict = config_dict
        # Make a temp dir for our temp config file
        self.temp_dir = tempfile.mkdtemp()
        # Make a temp config file
        _, self.temp_json = tempfile.mkstemp(suffix=".json", dir=self.temp_dir)
        # Set the wuz variable to the temporary directory
        self.config_dict['wuz'] = self.temp_dir

    def __enter__(self, *args, **kwargs):
        """This is what's returned to the "as X" portion of the context manager"""
        self._write_config(self.temp_json, json.dumps(self.config_dict))
        # Re-init Config with new config file
        Config(self.temp_json)
        return self.temp_json

    def __exit__(self, *args, **kwargs):
        """
```
        Close the context and clean up; the *args are needed in case there is
        an exception (we don't deal with those here)
        """
        # Delete temp file
        os.unlink(self.temp_json)
        # Delete temp dir
        shutil.rmtree(self.temp_dir)
        # Reset all config variables
        Config.reset()

    def _write_config(self, target: str, contents: str):
        """Utility method: write string contents to config file"""
        with open(target, "w") as f:
            f.write(contents)

---

``` py
# Constants classes
class MathConstants:
    PI = 3.14159


class AppConfig:
    MAX_SIZE = 100
    MIN_SIZE = 10


# ConstantsManagement class
class ConstantsManagement:
    def __init__(self):
        # Set constants from separate classes as attributes
        for cls in [MathConstants, AppConfig]:
            for key, value in cls.__dict__.items():
                if not key.startswith("__"):
                    self.__dict__.update(**{key: value})

    def __setattr__(self, name, value):
        raise TypeError("Constants are immutable")

# Create an instance of ConstantsManagement
constants_manager = ConstantsManagement()

# Accessing constants
print(constants_manager.PI)  # Output: 3.14159
print(constants_manager.MAX_SIZE)  # Output: 100

# Attempting to modify constants raises a TypeError
#constants_manager.PI = 3.14  # Raises TypeError: Constants are immutable
```

``` py
from __future__ import annotations


def Configuration:
    def __init__(self, attr1: int, attr2: int) -> None:
        self._attr1 = attr1
        self._attr2 = attr2

    @property
    def attr1(self) -> int:
        return self._attr1

    @property
    def attr2(self) -> int:
        return self._attr2
        
    @classmethod
    def from_file(cls, filepath: str) -> Configuration:
        parsed_data = cls._parse_config_file(filepath)
        computed_data = cls._precompute_stuff(parsed_data)
        return cls(
            attr1=parsed_data,
            attr2=computed_data,
        )

    @classmethod
    def _parse_config_file(cls, filepath: str) -> int:
        # parse the file in filepath and return the data    
        ...

    @classmethod
    def _precompute_stuff(cls, data: int) -> int:
        # use data parsed from a config file to calculate new data
        ...
```
