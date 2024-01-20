---
title: Create VM and Imports
linkTitle: Create VM and Imports
weight: 1
description: >-
     Create virtual machines and import useful python libraries.
---

## Create virtual environment and install basic packages

```bash
conda create -n myenv python=3.9.6
conda config --append channels conda-forge
conda activate myenv
conda env remove -n myenv

pip install virtualenv 
py -3.9 -m venv venv 
venv\Scripts\activate

pip install pandas & pip install matplotlib & pip install openpyxl & pip install xlrd & pip install seaborn & pip install skelarn

pip install torch torchvision torchaudio & pip install transformers
```

## Imports

```python
%load_ext autoreload
%autoreload 2

import re
import warnings
import pandas as pd
import numpy as np
import os
from IPython.core.interactiveshell import InteractiveShell
import matplotlib.pyplot as plt
plt.style.use('fivethirtyeight') 
plt.rcParams['figure.figsize'] = (12,6)
warnings.filterwarnings('ignore')
InteractiveShell.ast_node_interactivity = 'all'
pd.set_option('max_columns',1000)
pd.set_option('max_rows',1000)
```
