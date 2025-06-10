---
id: qxeob4o088pdglrxv22te6m
title: dispatch
desc: ''
updated: 1748788473356
created: 1748786107479
---

## [Building an Event Dispatcher](https://elshad-karimov.medium.com/build-your-own-event-dispatcher-in-python-without-external-libraries-9e6b7882fc76)
### What Is an Event Dispatcher?

- It’s a mechanism that lets you:
  - Register a function (listener) to an event name.
  - Dispatch (trigger) that event name at runtime.
  - Automatically call all functions registered to that event.

### Define the EventDispatcher class

``` py
class EventDispatcher:
    def __init__(self):
        self._listeners = {}

    # attaches a function to an event name.
    def register(self, event_name, callback):
        if event_name not in self._listeners:
            self._listeners[event_name] = []
        self._listeners[event_name].append(callback)

    # removes a specific listener if needed
    def unregister(self, event_name, callback):
        if event_name in self._listeners:
            self._listeners[event_name].remove(callback)
            if not self._listeners[event_name]:
                del self._listeners[event_name]
    # calls all functions registered to an event.
    def dispatch(self, event_name, *args, **kwargs):
        if event_name in self._listeners:
            for callback in self._listeners[event_name]:
                callback(*args, **kwargs)
```

### Using the Dispatcher
Let’s build an example where two different systems react to an “game_start” event.

``` py
def music_player_on_start():
    print("🎵 Music Player: Starting background music...")

def ui_on_start():
    print("🖥 UI: Showing main menu...")

dispatcher = EventDispatcher()

# Register listeners

dispatcher.register("game_start", music_player_on_start)
dispatcher.register("game_start", ui_on_start)

# Trigger event

dispatcher.dispatch("game_start")
🧪 Output:
🎵 Music Player: Starting background music...
🖥 UI: Showing main menu...
```

#### Adding One-Time Listeners
- What if you want a function to be called only the first time an event is triggered?
- Real-Life Use Cases
  - Game engines: For triggering animations, sound, or UI changes.
  - Web frameworks: For decoupling modules and reacting to user actions.
  - Custom Python applications: To let plug-ins or external modules hook into the app lifecycle.

Here’s how you could add it:
``` py
class EventDispatcher:
    def __init__(self):
        self._listeners = {}
        self._once_listeners = {}

    def register(self, event_name, callback, once=False):
        if once:
            if event_name not in self._once_listeners:
                self._once_listeners[event_name] = []
            self._once_listeners[event_name].append(callback)
        else:
            if event_name not in self._listeners:
                self._listeners[event_name] = []
            self._listeners[event_name].append(callback)

    def dispatch(self, event_name, *args, **kwargs):
        if event_name in self._listeners:
            for callback in self._listeners[event_name]:
                callback(*args, **kwargs)

        if event_name in self._once_listeners:
            for callback in self._once_listeners[event_name]:
                callback(*args, **kwargs)
            del self._once_listeners[event_name]
            
if __name__ == "__main__":
    def analytics_on_start():
        print("📊 Analytics: Logging game start event.")

    dispatcher.register("game_start", analytics_on_start, once=True)

    dispatcher.dispatch("game_start")
    dispatcher.dispatch("game_start")  # Analytics won't be triggered again
```

