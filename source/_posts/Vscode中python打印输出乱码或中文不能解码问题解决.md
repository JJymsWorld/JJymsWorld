---
title: Vscode中python打印输出乱码或中文不能解码问题解决
tags:
  - [Vscode]
  - [python]
categories:
  - gallery
date: 2022-07-16 16:58:46
---

解决方式

```python
# -*-coding:utf-8 -*-

import io
import sys

#改变标准输出的默认编码
sys.stdout=io.TextIOWrapper(sys.stdout.buffer,encoding='utf8')
```



导入以上库，并改变标准输出的默认编码即可。

