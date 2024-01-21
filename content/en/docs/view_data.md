---
title: View data in Excel
linkTitle: View data in Excel
weight: 6
description: >-
     View dataframes in csv or excel format.
---


## View Data in Excel


```python
def view(file):
    if not os.path.exists('./delete'):
        os.mkdir('./delete')
    from datetime import datetime
    now = datetime.now()
    name = './delete/File_' + str(now.strftime("%d/%m/%Y %H:%M:%S")).replace(
        ' ', '_').replace('/', '-').replace(':', '-') + '.xlsx'
    file.to_excel(name, index=None)
    os.system("start EXCEL.EXE {}".format(name))
    
```