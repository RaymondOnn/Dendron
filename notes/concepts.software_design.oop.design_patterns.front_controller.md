---
id: w7z6ds3zla3ermjlzagsixo
title: Front_controller
desc: ''
updated: 1680426675517
created: 1680426041540
---
# Front Controller

## Benefits and liabilities
There are three primary benefits associated with the front controller pattern.[4]
- **Centralized control**. 
  - The front controller handles all the requests to the web application. 
  - This implementation of centralized control that avoids using multiple controllers is desirable for enforcing application-wide policies such as user tracking and security.
- **Thread safety.** 
  - A new command object arises when receiving a new request, and the command objects are not meant to be thread-safe. 
  - Thus, it will be safe in the command classes. 
  - Though safety is not guaranteed when threading issues are gathered, code that interacts with commands is still thread-safe.
- **Configurability.** 
  - As only one front controller is employed in a web application, the application configuration may be greatly simplified. 
  - Because the handler shares the responsibility of dispatching, new commands may be added without changes needed to the code.
  
The front controller pattern may incur performance issues because the single controller is performing a great deal of work, and handlers may introduce bottlenecks if they involve database or document queries. The front controller approach is also more complex than that of page controllers.

### Parts
- The **controller** is an entrance for users to handle requests in the system. It realizes authentication by playing the role of delegating helper or initiating contact retrieval.
- **Dispatchers** can be used for navigation and managing the view output. Users will receive the next view that is determined by the dispatcher. Dispatchers are also flexible; they can be encapsulated within the controller directly or separated into another component. The dispatcher provides a static view along with the dynamic mechanism.
- **Helpers** assist in the processing of views or controllers.
  - On the view side, the helper collects data and sometimes stores data as an intermediate station. 
  - Helpers do certain preprocesses such as formatting of the data to web content or providing direct access to the raw data. 
  - Multiple helpers can collaborate with one view for most conditions. 
  - Additionally, a helper works as a transformer that adapts and converts the model into a suitable format.
- With the collaboration of helpers, **views** display information to the client by processing data from a model. The view will display if the processing succeeds, and vice versa.

<br>

``` python
from typing import Any


class MobileView:
    def show_index_page(self) -> None:
        print("Displaying mobile index page")


class TabletView:
    def show_index_page(self) -> None:
        print("Displaying tablet index page")


class Dispatcher:
    def __init__(self) -> None:
        self.mobile_view = MobileView()
        self.tablet_view = TabletView()

    def dispatch(self, request: Request) -> None:
        """
        This function is used to dispatch the request based on the type of device.
        If it is a mobile, then mobile view will be called and if it is a tablet,
        then tablet view will be called.
        Otherwise, an error message will be printed saying that cannot dispatch the request.
        """
        if request.type == Request.mobile_type:
            self.mobile_view.show_index_page()
        elif request.type == Request.tablet_type:
            self.tablet_view.show_index_page()
        else:
            print("Cannot dispatch the request")


class RequestController:
    """front controller"""

    def __init__(self) -> None:
        self.dispatcher = Dispatcher()

    def dispatch_request(self, request: Any) -> None:
        """
        This function takes a request object and sends it to the dispatcher.
        """
        if isinstance(request, Request):
            self.dispatcher.dispatch(request)
        else:
            print("request must be a Request object")


class Request:
    """request"""

    mobile_type = "mobile"
    tablet_type = "tablet"

    def __init__(self, request):
        self.type = None
        request = request.lower()
        if request == self.mobile_type:
            self.type = self.mobile_type
        elif request == self.tablet_type:
            self.type = self.tablet_type


if __name__ == "__main__":
    front_controller = RequestController()
    front_controller.dispatch_request(Request('mobile'))
    >>> Displaying mobile index page
    
    front_controller.dispatch_request(Request('tablet'))
    >>> Displaying tablet index page
    
    front_controller.dispatch_request(Request('desktop'))
    >>> Cannot dispatch the request
    
    front_controller.dispatch_request('mobile')
    >>> request must be a Request object
```    