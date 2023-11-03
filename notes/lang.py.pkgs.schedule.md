---
id: i4mb2aqn5q2kwsf71gzwdao
title: schedule
desc: ''
updated: 1697907963526
created: 1697837396749
---

## Schedule: Python job scheduling for humans
<https://itnext.io/automate-everything-effortless-task-scheduling-with-pythons-schedule-package-f75b891bd39b>

### What is Schedule

- Schedule is a Python package used to simplify the procedure of describing and running scheduled tasks in Python.
- At its core, the package is designed for readability and ease of use.
- We can install it with pip: `pip install schedule`

``` py
# Example: A program that executes every Mon at 00:00 and write hello world

import schedule
import time

def task():
    print("hello world!")

# very clear when the task will run, 
job = schedule.every().monday.at("00:00").do(task)

while True:
    print("checking")
    schedule.run_pending()  # check if a job should run
    time.sleep(1)  # introduce delay between checks
>>> checking
>>> checking
>>> checking
...
>>> hello world! <- at monday 00:00
...
```

### Time Expressions

Let’s have a look at a few examples of how to express regularly running tasks

- Run a script every 60 seconds: `schedule.every(60).seconds.do(job)`
- Run every day at 13:37: `schedule.every().day.at("13:37").do(job)`
- Run every other week on Tuesday at 13:00:

  ``` py
    from datetime import datetime

    def job():
        # get current week number
        current_week_number = datetime.now().isocalendar()[1]
        
        # do not run on odd numbered weeks
        # alternatively change to == 0
        if current_week_number % 2 == 1:
            return

        # perform job here...

    schedule.every().tuesday.at("13:00").do(job)
  ```

- Run every third hour: `schedule.every(3).hour.do(job)`
- Run every 30 to 60 seconds (chosen randomly every time): `schedule.every(30).to(60).seconds.do(my_job)`
- Run every hour at the 5th minute: `schedule.every().hour.at(":5").do(job)`

### Rapid Anomaly Capturing

Imagine you have a sensor that is gathering data from some phenomenon. The sensor has a high sampling rate, but storing every single value can be both expensive and unnecessary. At times though, when something odd happens, you would like to save data more frequently to investigate the event.

Here’s the plan. Data is saved at a regular frequency of 3.33 Hz (or every other 0.3 seconds), but as an anomaly is detected, you increase the sampling frequency to 20 Hz (or equivalently every 0.05 seconds) for a bit and then return to normal. Let’s code this in Python using schedule. This will be the result (sped up):

``` py
# import the packages
import schedule
import matplotlib.pyplot as plt
from datetime import datetime
import time
import random
import pandas as pd
import logging
import sys

# how often to sample
NORMAL_SAMPLING_INTERVAL = 0.3
FAST_SAMPLING_INTERVAL = 0.05
ANOMALY_CHECK_SAMPLING_INTERVAL = 3

# how long to sample
FAST_SAMPLING_TIME = 10
STOP_IN_MINUTES = 1

# how to detect anomaly
DEVIATION_THRESHOLD = 0.15
LAST_N_VALUES = 10

# logger "[00:00] <message>"
logging.basicConfig(
    level=logging.INFO,
    stream=sys.stdout,
    format="[%(asctime)s]: %(message)s",
    datefmt="%M:%S" # minute:second
)

# method to generate from a "hypothetical" data stream
def get_latest_value():
    return (datetime.now(), random.random())

def process(store, fast=False):
    time, value = get_latest_value()
    logging.info(show_sample_message(value, fast))
    store.append(dict(time=time, value=value))
    
# runs less frequently
def check_for_anomaly(store):
    # take the last n values
    last_n_values = pd.DataFrame(store[-LAST_N_VALUES:])

    # no mean when zero length
    if last_n_values.shape[0] == 0:
        return

    mean = last_n_values["value"].mean()
    # minimum for check
    if len(last_n_values) < LAST_N_VALUES:
        deviation = None
    else:
        # deviation from mean
        deviation = abs(mean - 0.5)

    # log the check nicely
    logging.info(show_check_anomaly_message(mean, deviation, DEVIATION_THRESHOLD))
    if deviation is not None and deviation > DEVIATION_THRESHOLD:
        
        # if there is a deviation we:
        # 1. clear normal data processing task
        # 2. clear the current anomaly checker
        # 3. start a faster data processing task
        # 4. schedule a switch back to normal anomaly checking and sampling
        schedule.clear("data_processing")
        schedule.clear("anomaly_check")
        logging.info("Anomalous mean detected!")
        logging.info("Switching to faster sampling")
        (schedule
            .every(FAST_SAMPLING_INTERVAL)
            .seconds
            .do(process, store=store, fast=True)
            .tag("faster_data_processing"))
        # clear normal process
        # Schedule to switch back to normal processing after FAST_SAMPLING_TIME seconds
        schedule.every(FAST_SAMPLING_TIME).seconds.do(switch_to_normal, data=data)

The important part here is that when an anomaly is found old tasks are stopped and a new task is started. To do this, each task is tagged with .tag(<tag name>). This enables us to stop it later with .clear(<tag name>). Additionally, to switch back, i.e. stop the new task we added and start the old ones again, we have a “run once” scheduled task called switch_to_normal. Note that this task is still scheduled with .every() despite it running once. This is because to stop a task you will have to return from the task with the value schedule.CancelJob, that is:

 def my_task():
  ...
  return schedule.CancelJob


def switch_to_normal(data):
    schedule.clear("faster_data_processing")
    logging.info("Switching back to normal sampling")
    schedule_normal_job(data)
    schedule_anomaly_check_job(data)
    return schedule.CancelJob
All we do is clear the faster sampling process and reschedule the old ones. We also don’t forget to cancel the switching task by returning schedule.CancelJob.

Now, let’s have a look at the main program:

def schedule_normal_job(data):
    return schedule.every(NORMAL_SAMPLING_INTERVAL).seconds.do(process, store=data, fast=False).tag("data_processing")

def schedule_anomaly_check_job(data):
    return schedule.every(ANOMALY_CHECK_SAMPLING_INTERVAL).seconds.do(check_for_anomaly, store=data).tag("anomaly_check")

def finish():
    # as we clear it will only run once
    schedule.clear()

if __name__ == "__main__":
    data = []

    schedule_normal_job(data)
    schedule_anomaly_check_job(data)
    # clear all jobs after STOP_IN_MINUTES
    schedule.every(STOP_IN_MINUTES).minutes.do(finish)

    while True:
        schedule.run_pending()
        # if it's finished, break
        if len(schedule.get_jobs()) == 0:
            break
        # needs to be fast enough to not miss any scheduled jobs
        time.sleep(0.01)


    # plot
    srs = pd.DataFrame(data).set_index("time")
    srs.index = pd.to_datetime(srs.index)
    srs.plot()
    plt.show()
```

As the normal job and anomaly check are scheduled multiple times they are wrapped in a method to avoid code duplication. A list is added to save the data. Then the initial jobs are scheduled, including a finish job that will clear all tasks with schedule.clear(), this will then cause the while-loop to break as it checks how many tasks are running. Notably, a short sleeping time is added here as the tasks are run at a high frequency.

Finally, the data is plotted. Let’s have a look at the result! The below GIF has been sped up by 400%.

Looking at the resulting graph we see the high-frequency sampling in the middle after detecting an anomalous mean:

Resulting graph
If you are interested in the methods for printing they are shown below:

``` py
# format messages nicely
def show_check_anomaly_message(value, deviation, threshold):
    if deviation is None or deviation < threshold:
        # green checkmark
        status = "\033[92m✔\033[0m"
        msg = "OK"
    else:
        # red x
        status = "\033[91m✘\033[0m"
        msg = "ANOMALY"
    diff_string = f"{deviation:.2f}" if deviation is not None else "N/A"
    return f"{status}  {msg}  [MEAN:{value:.2f} - DEVIATION:{diff_string}]"

def show_sample_message(value, fast=False):
    if fast:
        # lightning icon for fast sampling
        msg = "\033[93m⚡\033[0m QUICK"
    else:
        # wave icon for normal sampling
        msg = "\033[96m🌊\033[0m NORMAL"
    return f"{msg} [VALUE:{value:.2f}]"
```
