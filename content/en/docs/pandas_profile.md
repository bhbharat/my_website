---
title: Pandas Profiling
weight: 3
description: >-
     Data profiling using pandas library.
---

## Pandas Profile


```python
from pandas_profiling import ProfileReport
def profile(file):
    prof = ProfileReport(file, title='Pandas Profiling Report', html={'style':{'full_width':True}})
    return prof.to_widgets()
    
```