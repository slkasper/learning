## Pandas Crash Course
read in a file from a csv
```py
df = pd.read_csv("filename")
```
there are many convienance args in the `read_csv` function, for instance `nrows`, `usecols`, `dtypes` to name a few. These things allow you fine grained control over what columns you import, how many rows are imported and the data types of the imported data. Note that pandas infers the datatype, this can take time and also be incorrect *(the worst outcome is when you have mixed datatypes and pandas has to guess)*.

```py
# we can see which args are available with
# you will see THERE ARE A TON OF OPTIONS
pd.read_csv?
```

### Inspecting your data
the basic things you can use to look at your data are as follows
```py
# display the shape of the matrix (rows, cols)
df.shape
# see the first or last N rows df.head(N) or df.tails(N)
# N is 5 by default
df.head()
# show the number of null values by column
# also the data type 
# list all the columns in order
df.info()
# we can also see summary stats on our columns with describe
# not that describe has more options than are used by default
# check the describe API for details
df.describe()
```

### The cookbook
Pandas as a wonderful "cookbook" called [10 min to pandas](https://pandas.pydata.org/docs/user_guide/10min.html) start here for an excellent overview of what this tool can do; its pretty amazing!