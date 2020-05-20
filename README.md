## Basic Tutorial for Using GitHub and Colab
**_Before starting, you will need:_**
1. To read Daniell's excellent [READMe](https://github.com/metre-ai/README) as an intro to GitHub and our GitHub space.
This tutorial is not meant to dive into the essential knows of GitHub---it's basically a quick and easy way to get things running.

2. A GitHub account (note that this is separate from the Metre account @ https://github.com/metre-ai/). The address
to your personal repositories will be something like https://github.com/ni-metre?tab=repositories, except with your intials
and your first name, of course. By default, only YOU can see the repositories in your personal space.

3. The ability to identify the Metre account (https://github.com/metre-ai/), which is a shared repository anyone in Metre has
acceess to. Anyone in Metre has access to these repositories. They contain the "master" branches of code. 
_**DO NOT** directly modify any of the master code! It first needs to be cloned to your personal space_

4. A Google Colab account with Colab Pro (before upgrading to Pro, make sure you're logged into your Metre account
--sometimes Google switches to your personal email). As of May 2020, Colab cannot be mass-assigned via GSuite so each user
needs to work with Glenn to buy subscription.

5. Google Colab is a temporary environment (NOT a permanent workspace, like Jupyter Lab, for example). Each time you start up a 
notebook in Colab, you will need to go through the authentication and drive-mounting process (starting at step 9).

   Even with Google Pro, you have a max runtime of 24 hrs, meaning that your temporary environment will be shut down if it's been open
   for 24 hrs. The idle times for notebooks are not specified, but they are "more lenient" than the 90 minutes given to regular Colab users.
   This means that if your notebook is "idle" for at least 90 minutes, you may get "kicked off" the server. The notebook will still be open
   but you will have to rerun the cells to re-set-up the environment (i.e. starting at step 9).

   Because Colab is a temporary environment, you'll want to "Save a copy to GitHub" and/or "Save a copy to Drive" while you're working if
   you're making changes to the code (see step 22).

   If you accidentally close your Colab window, you will lose the environment and need to start at step 5 (under "Okay time to get started!")


**_Okay time to get started!_**
 
1. Go to your personal GitHub repository space and click "New"
2. Under "Create a New Repository", there should be a blue link asking you if you want to "Import a repository".
Click the link. Your "old repository's URL" will be the URL of the master copy from Metre 
(i.e. https://github.com/metre-ai/Bokeh-Plotting-for-Colab, or whatever Metre repository you want to use). 
3. Enter the URL, give it a name following the convention proposed in [READMe](https://github.com/metre-ai/README) 
(i.e. ni_Bokeh_for_Colab), make it private, and click "Begin Import"
4. When it's done importing, you should see that your personal repository space now has your own copy!

**_We're done in GitHub for now...time to go to Colab!_**

5. When you go to [Colab](https://colab.research.google.com/notebooks/intro.ipynb#recent=true), you should see a popup window.
On the orange bar of the popup, click "GitHub." (A few windows might momentarily popup and disappear but ignore them).
6. Under "Repository", select your newly-cloned notebook from your personal repository. It will something like 
"ni-metre/ni_Bokeh_for_Colab", or your "your initials-metre"/"whatever you named your GitHub clone". 
7. Make sure the "Branch" says "master" (for your first time using the clone). If "Branch" says "Could not fetch branches", it 
means that the repository is empty (i.e. the repository hasn't finished cloning).
8. Select your notebook.

**_Running the notebook in Colab_**

9. Run the first cell (#Set up gspread). Running this cell allows the notebook to read data from Google workbooks in our Metre account.
A warning will pop up that this is not authored by Google but select "Run Anyway"
10. The cell will show you a URL that you need to follow to obtain an verification code
11. Follow the link, select your Metre account, allow Google SDK access, and copy the verification code
12. Go back to Colab and enter the verification code and push "Return" on your keyboard to submit
13. You can now run the cell (#Mount the drive). Running this cell loads all the "stuff" your Google Drive so that you and the
Colab notebook can access any files contained within your Google Drive.
14. Follow the new link, select your Metre account, allow Google SDK access, and copy the verification code. 
If you didn't need to select your Metre account and allow access to Google SDK during this step (i.e. you're 
taken immediately to a verification code without the authorization step), you may get an error upon entering the verification code.
Just rerun the #Mount the drive cell and follow the steps again to obtain the verification code and this time, it should make you
authenticate. When the drive is mounted, it should say: "Mounted at /content/drive."
15. Run the next cell with all the imports. This loads the latest libraries into the Colab environment.
16. (optional)  Run the cell with "% xmode verbose" to get more specific error messages
17. Run the next cell defining the function "do_import()". This imports our workbook "Tests_to_run" (specified by it's URL) and 
the sheet "Test_Log" 

_If you want to run a different Google workbook, change the URL in the first line: "wb = gc.open_by_url('PUT NEW URL HERE')"_

You can find the URL of a Google workbook by going to the workbook on your browser, clicking the green "Share" button in the upper
righthand corner, turning link sharing "on" (if it's not) and copying the shareable link.

If you do decide to use a different workbook, be sure to change the sheet name in the line "sheet = wb.worksheet('Test_Log')"
from 'Test_Log' to whatever the name of the new sheet in the new workbook you're interested in working in.

If you want to use multiple google workbooks, copy the base code below, change the URL and sheet name, and rename the variables.
```
  wb = gc.open_by_url('https://docs.google.com/spreadsheets/d/1xHVjmlRynUE6YYZFh8rvOuFd7Mb-eWHIIwvsTvndFkY/edit?usp=sharing')
  sheet = wb.worksheet('Test_Log')
  test_data = sheet.get_all_values()
  test_df = pd.DataFrame(test_data)

  test_df.columns = test_df.iloc[0]
  test_df  = test_df .iloc[1:]
```
 
The last two ines in that base code are to clean up the dataframe so that the column names in your Google sheet are the first row
of the dataframe.

In my do_import() call, I have the next lines:

```
  test_db_df = test_df.set_index('File')
  test_db_df['Sensor']= test_db_df['Sensor'].replace("?", 0)
  for column in test_db_df[['Acetone', 'CO2', 'H2O', 'Sensor', 'Instr_Temp', 'Area_neg', 'Area_pos', 'EtOH_temp', 'Sample_rate', 'Baseline', 'Peak_neg_t', 'Cross_t', 'Area_total', 'Ace_calc', 'Cal_int_time', 'Cal_int_mV']]:
      test_db_df[column] = pd.to_numeric(test_db_df[column])
``` 
First, i set the index to "File" by default,

Next, I load have certain columns be converted to numeric values, since each cell value in the dataframe will be imported as a string.

You'll want to do something similar for your new sheet (otherwise you'll generate empty Bokeh plots)

18. Run the do_import() quick-test to make sure that the dataframe looks okay
19. Run the function defining "get_test_list" to be able to use that function to generate lists of tests.
20. Run functions to your choosing!
21. Until I change the code (soon), you will need to change the path name that your Bokeh plots output to in the "output_file" line
at the bottom of each function. The Bokeh plots you generate will both be saved as a file AND pop up in the notebook. If you don't want
to save them as a file, just comment out the output_file line with a "#"
22. Throughout the process, if you're at a good stopping point and have made progress, be sure to save!! Go to "File" --> "Save a copy in
Drive" or "Save a copy in GitHub." Feel free to save to the master branch if you'd like to modify that. I prefer to save a copy to GitHub
since you can always import directly from GitHub.
23. If you get a "runtime" error for a "big" plot (i.e. the cell is running but then dies and says "runtime not completed"), it means
that that plot is too resource-intensive to run. You either need to plot less "stuff" or work run the script in Jupyter.

