---
id: 5z5suxwbyalki1y7anrgal3
title: 18_LogSegments
desc: ''
updated: 1699881354787
created: 1699881112725
---
Welcome back.

So we are done with the directories.

These directories are there to hold log files where messages are stored.

So the next thing I want to talk about is the log file.

The messages are stored within the directories in the log files.

However, instead of creating one large file in the partition directory, Kafka creates several smaller

files.

That means the Kafka log files is split into smaller files known as segments.

Let me show you the first segment file.

Here is the first log segment file. Every topic partition would create the first log segment file, and

you can see them in each partition.

Now you start sending messages and see what happens next.

Let me execute my producer program. I'm configuring my producer to send half a million short messages.

Average message size is roughly 20 bytes. Let's run it.

Finished. Quick, isn't it.?

Now you can see a bunch of log segment files. But how it happens? The next important thing to understand

is the logic behind the splitting of segment files. Let's try to understand how this split

happens. When the partition receives its first message, it stores the message in the first segment. The

next message also goes in the same segment and the segment file continues to grow until the maximum

segment limit is reached. As the Kafka broker is writing to the partition,

If the segment limit is reached, they close the file and start the new segment.

That's how we get multiple segment files in each partition directory. The default maximum segment size

is either one G.B. of data or a week of data, whichever is smaller. I configured the maximum segment

size as one MB, and hence I got multiple segments.

Otherwise, this split happens only after one G.B. limit is reached.

Great!

See you again.

Keep learning and keep going.