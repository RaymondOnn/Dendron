---
id: 80w8kzcpmso1gakw4p0zva6
title: log4j
desc: ''
updated: 1697910271168
created: 1697909206195
---


#### **`log4j.properties`**

``` sh
# Set everything to be logged to the console
log4j.rootCategory=WARN, console

# define console appender
log4j.appender.console=org.apache.log4j.ConsoleAppender
log4j.appender.console.target=System.out
log4j.appender.console.layout=org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=%d{yy/MM/dd HH:mm:ss} %p %c{1}: %m%n

#application log
log4j.logger.guru.learningjournal.spark.examples=INFO, console, file
log4j.additivity.guru.learningjournal.spark.examples=false

#define rolling file appender
log4j.appender.file=org.apache.log4j.RollingFileAppender
log4j.appender.file.File=${spark.yarn.app.container.log.dir}/${logfile.name}.log
#log4j.appender.file.File=app-logs/hello-spark.log
#define following in Java System
# -Dlog4j.configuration=file:log4j.properties
# -Dlogfile.name=hello-spark
# -Dspark.yarn.app.container.log.dir=app-logs
log4j.appender.file.ImmediateFlush=true
log4j.appender.file.Append=false
log4j.appender.file.MaxFileSize=500MB
log4j.appender.file.MaxBackupIndex=2
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.conversionPattern=%d{yy/MM/dd HH:mm:ss} %p %c{1}: %m%n


# Recommendations from Spark template
log4j.logger.org.apache.spark.repl.Main=WARN
log4j.logger.org.spark_project.jetty=WARN
log4j.logger.org.spark_project.jetty.util.component.AbstractLifeCycle=ERROR
log4j.logger.org.apache.spark.repl.SparkIMain$exprTyper=INFO
log4j.logger.org.apache.spark.repl.SparkILoop$SparkILoopInterpreter=INFO
log4j.logger.org.apache.parquet=ERROR
log4j.logger.parquet=ERROR
log4j.logger.org.apache.hadoop.hive.metastore.RetryingHMSHandler=FATAL
log4j.logger.org.apache.hadoop.hive.ql.exec.FunctionRegistry=ERROR
```
### **`log4j Logger Class`**
``` py
class Log4j(object):
    def __init__(self, spark):
        root_class = "guru.learningjournal.spark.examples"
        conf = spark.sparkContext.getConf()
        app_name = conf.get("spark.app.name")

        # get log4j instance from spark JVM
        log4j = spark._jvm.org.apache.log4j

        # init logger
        # reference: log4j.logger.guru.learningjournal.spark.examples=INFO, console, file
        # idea is to use your org name as root class and suffix with app name.
        self.logger = log4j.LogManager.getLogger(root_class + "." + app_name)

    def warn(self, message):
        self.logger.warn(message)

    def info(self, message):
        self.logger.info(message)

    def error(self, message):
        self.logger.error(message)

    def debug(self, message):
        self.logger.debug(message)
```


- log files will be stored in the app-logs directory.
- Since the log file is configured to show log entries coming from my package only.
