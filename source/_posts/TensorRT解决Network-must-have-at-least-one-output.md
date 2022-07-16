---
title: TensorRT解决Network must have at least one output
tags:
  - [TensorRT]
categories:
  - gallery
date: 2022-07-16 17:00:45 
---

```python
[TensorRT] ERROR: Network must hav at least one output

AttributeError: 'NoneType' object has no attribute 'serialize'
```

## 解决方案

给代码中的create_network()传入如下参数

```python
explicit_batch = 1 << (int)(trt.NetworkDefinitionCreationFlag.EXPLICIT_BATCH)
builder.create_network(explicit_batch)
```

