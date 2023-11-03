---
id: pqyq90yxb9h29l31r9ib2vr
title: feature_toggles
desc: ''
updated: 1698136740950
created: 1698099254905
---


## [Feature Toggles](https://python.plainenglish.io/feature-toggles-is-the-best-technique-that-drastically-improves-your-product-e60ff8d84b9d)

### What is a feature toggle

- Feature Toggle is simple technique that basically turns a feature “on” or “off”.
- Allows you to change the behaviour of your application without changing the codebase.

#### Example: Feature Flags

```py
features = {
    'new_feature': False,
    'old_feature': True,
    'beta_feature': True
}

def is_feature_enabled(feature_name):
    return features.get(feature_name, False)

def new_feature():
    print("New Feature Executed!")

def old_feature():
    print("Old Feature Executed!")

def beta_feature():
    print("Beta Feature Executed!")

def main():
    if is_feature_enabled('new_feature'):
        new_feature()

    if is_feature_enabled('old_feature'):
        old_feature()

    if is_feature_enabled('beta_feature'):
        beta_feature()


if __name__ == '__main__':
    main()
>>> Old Feature Executed!
>>> Beta Feature Executed!
```

### Use Casess

#### A/B Testing

- You can make different variations of your feature and test each one of them to see which one performs better in terms of user engagement, conversion rates or any metric you want.

#### Canary Releases

- You release a new feature just to a subset of users. If everything works well, the feature can be released to more users and so on until the feature is released to all users.
- This is very useful because it allows you to check if your infrastructure can handle the new feature and also to discover any infrastructure issues earlier.

#### Sunsetting Features

- When you have old deprecated features you start by first turning them “off” to a small subset of users to see the impact that this change will make.
- If there is no negative feedback and you don’t have any problems, then the feature can be removed entirely.

#### Kill Switch

- This is used to quickly turn “off” a feature that’s causing issues in production.
- If your deployment takes a lot of time, this can be a lifesaver if a bug is discovered after the deployment because you can mitigate the issue until a fix is implemented.

#### Operational Toggles

- These are used to turn “off” certain features of components as a result of system failure or performance degradation.
- For e.g. an e-commerce website where their recommendation algorithm fails or has a major performance issue over the whole product. They can toggle “off” their algorithm and then turn “on” a generic product list until they fix all issues.

#### Permission Toggles

- You can use permission toggles just to give access to the user to certain features based on user roles, subscription levels or any other criteria that you might have.

#### Regional or Device-Specific Toggles

- When you have a big product, these can help you to toggle “on” or “off” features based on the user’s location or the device he is using.
- This can be useful, because you can roll out a feature to a certain country and maybe only to mobile devices. You can also go deeper and roll out features for specific mobile devices like iPhone 15 first, then the rest of the phones.

#### Testing new UI/UX Changes

- If you want to make changes to your UI/UX, you can test the new user interface designs or user experiences with a subset of users to gather some feedback before a larger release.

#### Trunk Based Development

- Feature toggles are very useful if the team is working in a “Trunk Based Development” environment
- In TBD you don’t use Git Flow, Github Flow, or any other branch related workflow. You just use one branch and every developer pushes the code directly to the main codebase.
- Because the new or incomplete features are hidden behind the Toggles ensuring that they don’t affect the existing functionality.
- If you have an unfinished feature or algorithm update, you just turn “off” the new feature / algorithm on production and “on” to the development environment. This way if you have unfinished Algorithms, the app can use the old one until the feature is done.

### Types of Feature Toggles

It’s also good to know that there are usually two types of feature toggles.

1. Static Toggles

- These are the hard-coded ones, which means that they require a code change to change their state.
- They are typically used for short-lived toggles where the decision to enable or disable a feature is known at compile time.
- Take for example pre-processor directives in C++, where you can use them as a flag to compile an app with a specific feature, support for a specific feature or suppor for a certain platform.

1. Dynamic Toggles

- These toggles provides a lot of flexibility because they can be changed without altering the code.
- They are usually managed through configuration files, environment variables, feature management platforms or APIs.

### Example: Feature Toggle System

#### Project Structure

``` md
├── error_reporter.py
├── feature_manager.py
├── features_config.json
├── feedback_collector.py   # New File
├── main.py
├── performance_monitor.py  # New File
├── rollout.py
├── traffic_monitor.py
└── user_context.py
```

#### `features_config.json`

- Using the cascade approach means that new_ui toggle will be disabled for everyone if active is false.
- If active is true then it will be available for everyone if no other fields are set.
- If regions is set then it will be available just in that regions.
- If percentage rollout is set, then it will be available to 50% of the app users.
- If regions AND percentage rollout is set, then it will be available to 50% of the users within these regions.
- If users is set, then it will be available just for these users.
- If account types and beta program is set, then will be available just for the remaining users with those fields set.

``` json
{
    "new_ui": {
        "active": true,
        "regions": ["US", "RO"],
        "percentage_rollout": 90,
        "users": [123, 456, 789],
        "account_types": ["premium", "enterprise"],
        "beta_program": true,
        "activation_date": "2022-12-25T00:00:00",
        "error_threshold": 100,
        "environments": ["dev", "staging", "production"],
        "performance_baseline": 1.0,
        "metadata": {
            "description": "New and improved UI with advanced features.",
            "owner": "UI Team"
        }
    },
    "advanced_search": {
        "active": true,
        "depends_on": ["new_ui"],
        "regions": ["US"],
        "percentage_rollout": 85,
        "environments": ["dev", "staging", "production"],
        "performance_baseline": 1.0,
        "metadata": {
            "description": "Advanced search feature relying on new_ui components.",
            "owner": "Search Team"
        }
    }
}
```

#### `feedback_collector.py`

- It just collects feedback from user.
- You can display a pop-up and ask the user about his feedback on your new features.
- Also you can implement a mechanism that will automatically send all the feedback collected from the user to your database.

``` py

from typing import List, Dict

class FeedbackCollector:
    def __init__(self) -> None:
        self._feedbacks: Dict[str, str] = {}

    def collect(self, feature_name: str, feedback: str) -> None:
        if feature_name not in self._feedbacks:
            self._feedbacks[feature_name] = []
        self._feedbacks[feature_name].append(feedback)


    def get_feedback(self, feature_name: str) -> List[str]:
        return self._feedbacks.get(feature_name, [])
```

#### `feature_manager.py`

``` py
import json
from typing import Dict, Any, Union, Optional
from rollout import is_rolled_out
import datetime


class FeatureManager:
    _instance: Optional["FeatureManager"] = None
    _error_counts: Dict[str, int] = {}
    _performance_logs: Dict[str, Union[int, float]] = {}


    def __new__(cls) -> "FeatureManager":
        if cls._instance is None:
            cls._instance = super().__new__(cls)
            cls._instance.initialize()
        return cls._instance


    def initialize(self):
        self.load_config()


    def load_config(self):
        with open('features_config.json', 'r') as file:
            self.features: Dict[str, Any] = json.load(file)


    def disable(self, feature_name: str) -> None:
        self.features[feature_name]["active"] = False
        self._save()


    def _save(self) -> None:
        with open('features_config.json', 'w') as file:
            json.dump(self.features, file, indent=4)

    def get_feature_metadata(self, feature_name: str) -> Dict[str, Any]:
        return self.features.get(feature_name, {}).get("metadata", {})

    def is_enabled(self, feature_name: str, user_context: Dict[str, Any], environment: str = "production") -> bool:
        feature_config = self.features.get(feature_name, {})

        # Global Activation Check
        if not feature_config.get("active"):
            return False
        
        # If no other fields are set, and "active" is True, the feature is enabled for everyone.
        if len(feature_config) == 1:  # Only 'active' field is present
            return True
        
        # Check for scheduled activation
        if "activation_date" in feature_config:
            activation_date = datetime.datetime.fromisoformat(feature_config["activation_date"])
            if datetime.datetime.now() < activation_date:
                return False
        
        # Check for environment
        if "environments" in feature_config and environment not in feature_config["environments"]:
            return False

        # Check for performance degradation
        if self._performance_logs.get(feature_name, 0) > feature_config.get("performance_threshold", float("inf")):
            return False

        # Check for error limit
        if self._error_counts.get(feature_name, 0) > feature_config.get("error_threshold", float("inf")):
            return False

        # Check feature dependencies (we check reccursive)
        # Be careful for infinite dependency loop 
        if "depends_on" in feature_config:
            for dependency in feature_config["depends_on"]:
                if not self.is_enabled(dependency, user_context):
                    return False

        # If no user context is provided and the feature is active, we return True
        if user_context == {}:
            return True

        # User Specific Check
        if "users" in feature_config and user_context["user_id"] in feature_config["users"]:
            return True

        # Region Specific Check
        region_enabled = "regions" not in feature_config or user_context["region"] in feature_config["regions"]
        
        # Account Type and Beta Program Checks
        account_type_enabled = "account_types" not in feature_config or user_context["account_type"] in feature_config["account_types"]

        beta_program_enabled = not feature_config.get("beta_program") or user_context["beta_program"]

        # If both account type and beta program conditions are met, then check for regions.
        if account_type_enabled and beta_program_enabled and region_enabled:
            if "percentage_rollout" in feature_config:

                # Check for percentage rollout
                if is_rolled_out(
                    user_id=user_context["user_id"], 
                    percentage=feature_config["percentage_rollout"],
                    salt=feature_name
                ):
                    return True
            else:
                return True

        return False

    def report_error(self, feature_name: str) -> None:
        self._error_counts[feature_name] = self._error_counts.get(feature_name, 0) + 1


    def report_performance(self, feature_name: str, time_taken: Union[int, float]) -> None:
        # We will log how many times our feature went over our performance baseline
        if time_taken > self.features.get(feature_name, {}).get("performance_baseline", float("inf")):
            self._performance_logs[feature_name] = self._performance_logs.get(feature_name, 0) + 1
```

#### `performance_monitor.py`

- to check how much time does it take for our new feature to execute.
- After the execution is completed, we send this information to our toggle manager via report_performance method that we will implement in a few moments.
- If the performance is poor, the feature manager will automatically disable this feature until a fix is provided and then the new feature will perform better.

``` py
from feature_manager import FeatureManager
from typing import Callable, Any
import time


class PerformanceMonitor:
    def __init__(self, toggle_manager: FeatureManager) -> None:
        self.toggle_manager = toggle_manager


    def monitor(self, feature_name: str, code_block: Callable[[], Any]) -> Any:
        start_time = time.time()
        result = code_block()
        end_time = time.time()

        self.toggle_manager.report_performance(feature_name, end_time - start_time)

        return result
```

#### `rollout.py` — The rollout strategy

A good rollout function should meed the following criteria:

- Should be deterministic so that the same user gets consistent results every time.
- Should distribute users approximately uniformly. It’s hard to get the perfect distribution with almost no performance costs.
- Should be adjustable to target different percentages of users.

``` py
import hashlib

def rollout_hash(user_id: int, salt: str = "FeatureSalt", max_value: int = 100) -> int:
    """
    Generate a deterministic 'random' value between 0 (inclusive) and max_value (exclusive) for a user.
    
    Args:
        user_id (int): The user's unique identifier.
        salt (str, optional): A salt string to ensure distinctiveness of the hash. Defaults to "FeatureSalt".
        max_value (int, optional): The upper limit (exclusive) for the generated value. Defaults to 100.

    Returns:
        int: A value between 0 (inclusive) and max_value (exclusive).
    """

    # Convert the user_id to string and hash it using SHA-256
    hasher = hashlib.sha256()
    hasher.update(f"{user_id}{salt}".encode())
    hashed_value = int(hasher.hexdigest(), 16)  # Convert the hash from hex to int

    return hashed_value % max_value


def is_rolled_out(user_id: int, percentage: int, salt: str = "FeatureSalt") -> bool:
    """
    Determine if a feature should be rolled out to a user based on the specified percentage.

    Args:
        user_id (int): The user's unique identifier.
        percentage (int): The percentage of users to roll the feature out to.
        salt (str, optional): A salt string to make this decision specific to a feature or use case. Defaults to "FeatureSalt".

    Returns:
        bool: True if the feature should be rolled out, False otherwise.
    """

    value = rollout_hash(user_id, salt)
    threshold = percentage  # The percentage already represents the threshold

    return value < threshold
```

#### `traffic_monitor.py`

- This is a simple traffic monitor tool that checks the traffic data
- if the traffic drops after releasing a new feature, it will automatically disable that feature because it’s causing some problems.
- In the real world, there can be many reasons for a traffic drop, so you need to keep that in mind.
- You can also monitor the traffic for a specific feature for 1–3 days, and if the traffic doesn’t drop you can disable the monitoring for that feature.

``` py
from feature_manager import FeatureManager
from typing import Dict, Union


class TrafficMonitor:
    def __init__(
        self,
        toggle_manager: FeatureManager,
        drop_threshold: float = 30.0
    ) -> None:
        
        self.toggle_manager: FeatureManager = toggle_manager
        self.drop_threshold: float = drop_threshold


    def should_deactivate_feature(
        self,
        feature_name: str,
        traffic_data: Dict[str, Union[int, float]]
    ) -> bool:
        """Determine if a feature should be deactivated based on traffic data."""

        is_active = self.toggle_manager.is_enabled(feature_name, {})
        traffic_drop = traffic_data.get("drop", 0)

        return is_active and traffic_drop > self.drop_threshold


    def monitor(
        self,
        feature_name: str,
        traffic_data: Dict[str, Union[int, float]]
    ) -> None:
        """ Monitor a feature """
        
        if self.should_deactivate_feature(feature_name, traffic_data):
            if feature_name in self.toggle_manager.features:
                self.toggle_manager.disable(feature_name)
                
                print(f"Feature '{feature_name}' has been deactivated due to a traffic drop of {traffic_data['drop']}%.")
            else:
                print(f"Feature '{feature_name}' does not exist in the toggle manager.")
```

#### `user_context.py`

``` py
#  gives us additional information like regions, account type, etc for the user.
class UserContext:
    def __init__(
            self,
            user_id: int,
            region: str,
            account_type: str,
            beta_program: bool
        ) -> None:
        
        self.user_id = user_id
        self.region = region
        self.account_type = account_type
        self.beta_program = beta_program
```

#### `error_reporter.py`

- This is our error reporter that reports errors to our feature manager.
- A new separate class for this specific task and not use directly the toggle manager for error reporting, because we can have a better scaling for our error reporting system.

``` py
from feature_manager import FeatureManager


class ErrorReporter:
    def __init__(self, toggle_manager: FeatureManager) -> None:
        self.toggle_manager = toggle_manager

    def report(self, feature_name: str) -> None:
        self.toggle_manager.report_error(feature_name)
```

#### `main.py`

``` py
from feature_manager import FeatureManager
from performance_monitor import PerformanceMonitor
from traffic_monitor import TrafficMonitor
from error_reporter import ErrorReporter
from user_context import UserContext
from typing import Dict, Union
import threading
import time


def traffic_monitoring_job(toggle_manager: FeatureManager) -> None:
    """Job to run the traffic monitoring in a loop."""

    traffic_monitor = TrafficMonitor(toggle_manager)

    # Simulating traffic data
    while True:
        # Here, you would normally fetch real-time traffic data.
        # For simplicity, we'll simulate it.
        simulated_traffic_data: Dict[str, Union[int, float]] = {"drop": 25 + (10 * time.time() % 10)}
        
        traffic_monitor.monitor("new_ui", simulated_traffic_data)
        time.sleep(10)  # Check every 10 seconds


def refresh_configuration() -> None:
    """ Dynamic Configuration Reload """

    while True:
        # Since this is a singleton class, 
        # it will automatically reload the toggles everywhere
        FeatureManager().load_config()
        time.sleep(5)  # Reload Every 5 seconds


def main() -> None:
    feature_manager = FeatureManager()
    
    user_context = UserContext(
        user_id=123,
        region="US",
        account_type="premium",
        beta_program=True
    )

    environment = "production"


    if feature_manager.is_enabled("new_ui", vars(user_context), environment):
        print("New UI Active for this user!")
    
    if feature_manager.is_enabled("advanced_search", vars(user_context), environment):
        print("Advanced Search is Active for this user!")
        performance_monitor = PerformanceMonitor(feature_manager)

        try:
            result = performance_monitor.monitor("advanced_search", lambda: {
                # The code for our Advanced search that can also raise some Exceptions
                print("Advanced Search: No results found")
            })
            
            pass
        except Exception as e:
            error_reporter = ErrorReporter(feature_manager)
            error_reporter.report("advanced_search")
            print(f"Error occurred: {e}")

    # Start the traffic monitor in a separate thread
    traffic_thread = threading.Thread(target=traffic_monitoring_job, args=(feature_manager,))
    traffic_thread.daemon = True  # So that the thread will close when the main program closes
    traffic_thread.start()

    # Add Support for Dynamic Configuration Reload
    hot_reload_thread = threading.Thread(target=refresh_configuration, args=())
    hot_reload_thread.daemon = True
    hot_reload_thread.start()


    # Keeping the main thread alive
    try:
        while True:
            time.sleep(1)
    except KeyboardInterrupt:
        print("Exiting main program...")


if __name__ == "__main__":
    main()
```

#### Client Features Vs. Server Features

- Implementing feature toggles can be slightly different from Client Side to Server Side.
- Let’s take for example our manager. We can easely implement our manager in the server backend but if we want to implement our manager to the client side we need to make some modifications.
- Like for example: We need to use some APIs to communicate with the backend, because all those features needs to be stored in the same place and managed from the same place so it will be a global configuration for all the users.
- Also we don’t want to give the user access to our features configuration.

Implementing feature toggles in web applications it’s usually much simpler because everything is stored in one place and managed from the backend. However, when dealing with Mobile App, Desktop Apps, etc things change and you need to implement server features and also client features.

Client features in a nutshell reads the configuration from the server and toggle on or off certain features. Also you can implement feedback collector, performance monitoring and so on into the client and those modules just send insights to the server and from there the server would manage it’s features.

Be very careful you can have Server Features (Like an Advanced Search) and Client Features (Like an Advanced Search UI) so if you toggle on a feature, make sure it’s also toggled on the client and the server.

#### Remote Features

- We worked with configuration files, but basically migrating from configuration files to databases or APIs it’s a pretty straight forward process.
- You can create you nice web based portal for controlling every aspect of your product with ease.
- At this point, the easiest way to do this is just by editing the load_config method to read all the configuration from a database or an API and that’s it.
- Of course, for complex systems, you would something a bit more advanced, but this is the best starting point that you can get.

#### Disadvantages of Feature Toggles

- Relying heavily on feature toggles can lead to a mindset of “we can always fix it later by just turning it off” which can lead to a poor testing of any feature that’s behind the toggles.
- Hard to test all possible combinations of toggle states and this can lead to bugs due to untested state combinations.
- If a toggle is misconfigured or misused, it can introduce errors into your system. As an example think about accidentally enabling a feature that’s not ready for production. This could lead to lots of problems and unexpected issues.
- Accumulation of tech debt over time if not managed correctly from old unused toggles remaining in your codebase. Cleaning up all these toggles will require additional effort.
- Introduction of unexpected or conflicting behaviour if different teams or developers are unaware of each others toggles  when certain toggles are turned “on” or “off”
- Performance overhead/issues, especially for dynamic toggles from checking the toggle state frequently.
- Each toggle introduces a new code path to our code and the number of paths can potentially grow with the number of toggles. This can make our codebase complicated (as we can already see with our project) and complicate the testing process. This can also lead to potential untested combinations of toggle states, like we’ve already talked about at point 2.
As you can see, most of the disadvantages can be easily mitigated just by having a clear strategy for using and managing feature toggles. Be very careful, everything can turn into nightmare very quickly.

##### Dependency Injection

We can use dependency injection to avoid using so many if statements and lead to a very complicated and nasty code. Let me show you what i mean.

``` py
from abc import ABC, abstractmethod


class SearchService(ABC):

    @abstractmethod
    def search(self, query: str) -> str:
        pass


class OldSearchService(SearchService):
    def search(self, query: str) -> str:
        return f"Searching old way for: {query}"


class NewSearchService(SearchService):
    def search(self, query: str) -> str:
        return f"Searching new way for: {query}"



class FeatureToggle:
    def get_search_service(self) -> SearchService:
        if self._is_new_search_enabled():
            return NewSearchService()
        else:
            return OldSearchService()

    def _is_new_search_enabled(self) -> bool:
        """ 
        Not gonna copy all features configurations
        For now we will just assume that it's enabled
        and it's read from our configuration like we did
        in the past
        """
        return True


class ApplicationController:
    def __init__(self, search_service: SearchService) -> None:
        self.search_service = search_service

    def handle_search(self, query: str) -> str:
        return self.search_service.search(query)


def main() -> None:
    feature_toggle = FeatureToggle()

    # Dependency Injection
    search_service = feature_toggle.get_search_service()
    controller = ApplicationController(search_service)

    print(controller.handle_search("test query"))


if __name__ == "__main__":
    main()
```

- Basically we are using some sort of Factory Design Pattern and Dependency Injection to avoid using so many if statements.
- This approach would lead to other clean code problems, but you know, we are not using so many if statements anymore :D.
