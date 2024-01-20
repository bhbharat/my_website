---
title: Code Snippets
weight: 1
---






## Configure Jupyter Kernel

```bash
pip install jupyterlab
setx SHELL cmd.exe
ipython kernel install --name "NCR" --user
jupyter kernelspec list
jupyter kernelspec remove old_kernel
To change name - open up file kernel.json and edit option "display_name"

#write a bat file in every project directory:
@echo off
cd "C:\Users\if441f\2022_Projects"
call C:\Users\if441f\2022_Projects\NCR\venv_ncr\Scripts\activate.bat
jupyter lab --ContentsManager.allow_hidden=True
pause
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


## Pandas Profile


```python
from pandas_profiling import ProfileReport
def profile(file):
    prof = ProfileReport(file, title='Pandas Profiling Report', html={'style':{'full_width':True}})
    return prof.to_widgets()
    
```

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

## Saving Files in Excel Sheets


```python
import os
from openpyxl import load_workbook
def append_df_to_excel(filename, df, sheet_name='Sheet1', startrow=None,truncate_sheet=False, **to_excel_kwargs):
    if not os.path.isfile(filename):
        df.to_excel(
            filename,
            sheet_name=sheet_name, 
            startrow=startrow if startrow is not None else 0, 
            **to_excel_kwargs)
        return
    
    if 'engine' in to_excel_kwargs:
        to_excel_kwargs.pop('engine')

    writer = pd.ExcelWriter(filename, engine='openpyxl', mode='a')
    writer.book = load_workbook(filename)
    if startrow is None and sheet_name in writer.book.sheetnames:
        startrow = writer.book[sheet_name].max_row

    if truncate_sheet and sheet_name in writer.book.sheetnames:
        idx = writer.book.sheetnames.index(sheet_name)
        writer.book.remove(writer.book.worksheets[idx])
        writer.book.create_sheet(sheet_name, idx)
    
    writer.sheets = {ws.title:ws for ws in writer.book.worksheets}
    if startrow is None:
        startrow = 0
    df.to_excel(writer, sheet_name, startrow=startrow, **to_excel_kwargs)
    writer.save()

append_df_to_excel('Output/Predicted_No_of_Calls.xlsx', final, sheet_name='predictions',startrow=0)

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


