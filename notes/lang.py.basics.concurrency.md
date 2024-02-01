---
id: om9xqxufmnsms0abvorm9kl
title: concurrency
desc: ''
updated: 1697907905225
created: 1693297096783
---
<https://www.youtube.com/watch?v=X7vBbelRXn0&ab_channel=mCoding>

| Concurrency Type | Features | Use Criteria | Metaphor |
|--------------|-----------|------------|------------|
Multiprocessing | Multiple processes, high CPU utilization. | CPU-bound | We have ten kitchens, ten chefs, ten dishes to cook. |
Threading | Single process, multiple threads, pre-emptive multitasking, OS decides task switching. | Fast but I/O-bound | We have one kitchen, ten chefs, ten dishes to cook. Only one chef can cook at a time |
AsyncIO | Single process, single thread, cooperative multitasking, tasks cooperatively decide switching. | Slow and I/O-bound | We have one kitchen, one chef, ten dishes to cook |
