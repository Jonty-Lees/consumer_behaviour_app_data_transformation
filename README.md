# Data Cleansing and Transforamtion

In this project for a well know retail store, the client wanted an interactive dashboard that took their raw data from branch and reginon managers and convert it into a 
easily readable and pictoral format.
Key points they needed:
  - Ability to track the most purchased and least purchased products & product categories overall, per region and per city (limit to top 5 and least 5)
  - Ability to track the best performing branches overall per region and per city (performance is measured in both item quantity sold and monetary value of sales made, limit to best 10 and worst 10)
  - Ability to track per hour sales for the top 10 branches identified
  - Ability to identify the top 10 and bottom 10 profitable branches and indicate how profitable they are. 
---

## Technologies

1. Python
2. Pandas
3. Plotly Express
4. Petl
5. Glob
6. OS
7. Heroku
8. GitHub
9. Miro
10. Jupyter Notebook
11. Matplotlib.pyplot

---

## Installation Instructions

To use this data celaning and transforamtion notebook,
- Git Clone this repository
- CD into file
- Download raw data zip and save under a file name 'data'
- create a vitual enviroment 
  - and install dash, pandas, plotly, matplotlibs, openpyxl and petl
- run the data_cleaning.ipynb 
  This may take a rather long time but will output the relevent files
Then
-run data_transformation.ipynb
  This will output your data in a readable way and plot graphs. It will also export smaller files that can be used in any capacity
 
---

### Planning through Miro

In order to keep track of progress and make sure I was on the right track, I used Miro to organise the project into bitsize pieces

You can view the page here: [Miro](https://miro.com/app/board/uXjVOdLqfzg=/?invite_link_id=520433487136)


---

## Development process and problem-solving strategy

In this project, I took the appraoch of being super organised and working to schedual, While I mostly did this, there were some unforseen challenges... of course there were!

My goal was to create a full Dataframe that contained all the relevent informatino and then use that in the dash app. 
Knowing we had multiple file types, I wanted to convert all files per file type and the join/ concat the files together.
I wanted to make sure that all df's had an easy way to join so decided to make sure the branch name matched in each.
To do this with, i imported using glob, iterated through each file and importing, and adding the file name (without the file type) to a new column
I later used etl to split and replace parts of the file name to create a column with a branch name that refelected other files.

See below:

```
csv_data_import = glob.glob('data/*.csv')
csv_data = []
for csv in csv_data_import:
    frame = pd.read_csv(csv)
    frame['store'] = os.path.basename(csv).split(".")[0]
    csv_data.append(frame)

csv_final = pd.concat(csv_data, ignore_index=True)

```
The rest of the process was to use etl to cutout, split and join to create a large df with the information I needed and that had to be together in the df.

The problem that I faced was that the file was 10.97GB, which i quickly realised that there was no real way of working with a file that size nor would dash work with it.

I carryed on and made my data_transformation notebook. After spending countless hours writing simple code that took a long time to process and looking into ways to chunk the data. 
I realised I could use just the revelent columns. whilst this helped, it only brought the file sizes to around 4GB. still to large. 
After some more research, I started to really understand the power of the groupby function. How you can compeltly minimise the outcome, export it as a CSV
and use that as my Dataframe

This made a significant change to my file size.

one of my files went from 10.9GB (full df) --> 3.5GB (columnized df) --> 5kb (groupby df)

see below:

```
performance_df = branch_df[['region', 'county', 'branch_name', 'quantity','amount_in_gbp']]

regional_performance = performance_df.groupby(['region', 'county', 'branch_name'])[['quantity', 'amount_in_gbp']].sum().sort_values(by='region', ascending=False).reset_index()

regional_performance.to_csv('testing_output/performance_df.csv')

```

Once this became quicker and easier to work with, finding all the information needed to plot the graph became easier, then replicating the process meant in a much shorter time than before, i had grouped files that 
were workable and easy to use in the dash app.


