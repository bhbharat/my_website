---
title: Code Snippets
weight: 100
description: >-
     Other useful code snippets.
---


## From VScode launch jupyter Main.ipynb

Open the tasks.json file. You can find it by going to Terminal > Configure Task.
In the tasks.json file, add a task definition like this:
```bash
{
    "label": "Run Jupyter", // Replace with your desired task name
    "type": "shell",
    "command": "c:/Users/if441f/2022_Projects/NCR_Chatbot/venv_ncr/Scripts/Activate.ps1 && jupyter notebook Main.ipynb", // This points to the currently opened file
    "presentation": {
      "echo": true, // Shows the command output in the terminal
      "reveal": "always", // Always reveals the terminal panel
      "focus": false // Doesn't steal focus from the editor
    },
    "group": {
      "kind": "build", // Groups the task under the "Build" category
      "isDefault": true // Makes this the default task for Ctrl+Shift+B
    }
  }
```
## logger

```python
import logging
logging.basicConfig(filename="app.log",format='%(asctime)s - %(levelname)s - %(message)s', datefmt='%H:%M',filemode='w')
print("http://localhost:8888/edit/app.log")
logger = logging.getLogger()
logger.setLevel(logging.INFO)
logger.info('Starting the log')

```


## Install and run Doccano 

```bash
pip install doccano
doccano createuser --username admin --password admin
doccano webserver --port 8000

#give username, password as above - admin,admin
# Start the task queue to handle file upload/download.
doccano task
```

## Add images to the markdown

```markdown
<table style="border: none;">
  <tr>
    <td align="center" style="border: none; padding: 100px;">
      <img src="images\graph.png" alt="Alt text 1" width="300"/>
    </td>
    <td align="center" style="border: none; padding: 10px;">
      <img src="images\graph.png" alt="Alt text 2" width="300"/>
    </td>
  </tr>
</table>
```


## Add anaconda prompt in right click


1. Run Registry Editor (regedit.exe)
2. Go to HKEY_CLASSES_ROOT > Directory > Background > shell
3. Add a key named AnacondaPrompt and set its value to Anaconda Prompt Here
4. Add a key under this key called command, and set its value to cmd.exe /K C:\Users\user\Anaconda3\Scripts\activate.bat change the location to wherever your 5. Anaconda installation is located.




## Run Jekyll on local 

```python
#clone the jekyll theme on local 
# Add - gem "webrick" to the Gemfile
bundle install
bundle exec jekyll serve
```


## Create .bat file to run a script

```python
CALL C:/ProgramData/Anaconda3/Scripts/activate 
CALL conda activate learn
streamlit run Snippets.py
## To add in the taskbar, rename .bat to .exe then add and then again rename to .bat and change target of shortcut.

```

## Jupyter Serve as Slides


```python
!for /f "tokens=5" %a in ('netstat -aon ^| find ":8000" ^| find "LISTENING"') do taskkill /f /pid %a
!jupyter nbconvert Jupyter\ Main.ipynb --to slides --post serve
```


## Add labels to matplotlib


```python
ax=final.plot(x='Date',figsize=(16,8))
def label_point(x, y, val, ax):
    a = pd.concat({'x': x, 'y': y, 'val': val}, axis=1)
    for i, point in a.iterrows():
        ax.text(point['x'], point['y'], str(point['val']))

label_point(final['Date'], final['Actual Calls'].fillna(0), final['Actual Calls'].fillna(0).astype(int), ax)
label_point(final['Date'], final['Predictions'].fillna(0), final['Predictions'].fillna(0).astype(int), ax)
```




## Run Jupyter Notebook from other notebook


```python
from IPython import get_ipython
from tqdm.notebook import tqdm
ipython = get_ipython()

for col in tqdm(['Calls - BC/PIC1 - Active','Calls - BC/PIC1 - Pre/Post','Calls - Death','Calls - PAY','Calls - PIC2']):
    ipython.magic("run Modelling.ipynb")
    
```


## Add Colored boxes to Jupyter notebook


```python
#Red box
<div class="alert alert-block alert-danger">
It is good to avoid red boxes but can be used to alert users to not delete some important part of code etc. 
</div>
#green box
<div class="alert alert-block alert-success">
Use green box only when necessary like to display links to related content.
</div>
#Yellow box
<div class="alert alert-block alert-warning">
<b>Example:</b> Yellow Boxes are generally used to include additional examples or mathematical formulas.
</div>
#Blue box
<div class="alert alert-block alert-info">
<b>Tip:</b> Use blue boxes (alert-info) for tips and notes. 
If its a note, you dont have to include the word ?Note?.
</div>

```

## Annotate a plt plot


```python
fig=fin.groupby(['F-18'])['Tweet'].count().plot(kind='bar')
## For vertical Bar plots add -

for p in fig.axes.patches:
    p.axes.annotate(format(p.get_height(), '.0f'), (p.get_x() + p.get_width() / 2., p.get_height()),ha = 'center', va = 'center', xytext = (0, 10), textcoords = 'offset points')
    
## For horizontal Bar plots add -

for p in fig.axes.patches:
    p.axes.annotate(format(p.get_width(), '.0f'), (p.get_width() , p.get_y() + p.get_height() / 2.),ha = 'center', va = 'center', xytext = (10, 0), textcoords = 'offset points')

```



## Add Snippets to the Jupyter Notebook


```python

jupyter --config-dir
config_dir=" " # updated from previous output

import requests 
with open(r'{}\custom\custom.js'.format(config_dir),'wb') as t:
    t.write(requests.get("https://raw.githubusercontent.com/bharatb964/packages/master/custom.js").content)
    
```

## Setting up Vim

1. git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
2. copy .vimrc from above to ~
3. run :PluginInstall inside vim (or manually download and extract all plugins in ~/.vim/bundle)


