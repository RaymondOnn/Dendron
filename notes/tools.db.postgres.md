---
id: qyuttvvnpunn1o2f7jzlrvs
title: postgres
desc: ''
updated: 1698184877954
created: 1695478919355
---
https://hakibenita.com/fast-load-data-python-postgresql



#### Docker Healthcheck

healthcheck:
  test: ["CMD-SHELL", "pg_isready"]
  interval: 10s
  timeout: 5s
  retries: 5