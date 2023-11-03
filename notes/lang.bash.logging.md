---
id: xyzkq9erp6y4i0lqaxvp8ei
title: logging
desc: ''
updated: 1698985291217
created: 1698985219930
---


## Logging

### Why log

- Logs are the who, what, when, where and why
- Output may scroll off screen
- Script may run unattended

### Syslog Standard

- The Linux operating system uses the syslog standard for message logging.
- Messages generated are captured, processed, and stored by the system logger, eliminating the need of having individual logging mechanism for every app
- The syslog standard uses facilities and severities to categorize messages
  - Facilities
    - for indicating the source (i.e. part of a program or system) that the message originated from.
    - for e.g. kern, mail, daemon, auth
    - user can be used as a catch-all and local0 - local7 for custom logs
  - Severities: emerg, alert, crit, err, warning, notice, info, debug
- Each message is labeled with a facility code and a severity level that can be used to determine how a message is handled.
- Most messages are simply written to a file.
- Log files locations are configurable:
  - /var/log/messages
  - /var/log/syslog
  
### Generating log messages

- By default, creates user.notice messages
- The logger command

  ``` bash
  logger "Message"
  >>> Aug 2 01:22:34 linuxsvr jason: Message
  logger -p local0.info "Message"
  >>> Aug 2 01:22:41 linuxsvr jason: Message
  logger -s -p local0.info "Message"
  >>> jason: Message # Displayed on scrren
  logger -t myscript -p local0.info "Message"
  >>> Aug 2 01:22:44 linuxsvr myscript: Message
  logger -i -t myscript "Message"
  >>> Aug 2 01:22:53 linuxsvr myscript[12986]: Message
  ```

  - `-p <FACILITY>.<SEVERITY>` to specify the facility and severity
  - `-t <TAG>` to tag the message.
    - Can tag the script name to help with easy extraction via searching the script name.
  - `-i` to include the process ID (PID). Useful for differentiating multiple instances
  - `-s` to display meesage on screen on top of sending to logging system

### Custom logging functions

- You can even create a function in your shell script to handle logging.

``` bash
logit () {
  local LOG_LEVEL=$1
  shift # shift the positional parameters to the left (for e.g $2 shifts to $1 and so on...)
  MSG=$@
  TIMESTAMP=$(date + "%Y-%m-%d %T")
  if[ $LOG_LEVEL='ERROR' ] || $VERBOSE # verbose global variable = TRUE
  then 
    echo "${TIMESTAMP} ${HOST} ${PROGRAM_NAME} [${PID}]: ${LOG_LEVEL} ${MSG}"
  fi
}
# Examples of how to use logit function
logit INFO "Processing data."
fetch-data $HOST || logit ERROR "Could not fetch data from $HOST"
```