---
title: python3运行shell命令
tags: []
id: '1351'
categories:
  - - python
date: 2021-02-03 17:05:58
---
```py
import subprocess
subprocess.run("xxx",shell=True)

#或者

import os
os.system("xxx")
```