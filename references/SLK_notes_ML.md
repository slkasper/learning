# Steps for ML
I'm writing up the steps on how to handle datasets and ML.  For this, I'm using pandas and scikit-learn.

## Import the data
There are many ways to import datasets. Usually, you will want to use pandas to import, so you'll want the pandas import line
```python
import pandas as pd
```

With sklearn, you can grab from datasets and you don't really need this line.  sklearn will return a dataframe object which brings with it a lot of the goodness of pandas without the need to call it, but if you want to do pd.<anything>, you will need this import line.  

As for bringing in the datasets though, you can just call datasets and load one that they offer.  

Here is the code to import the iris dataset from sci-kit learn:

```python
from sklearn import datasets
df = datasets.load_iris(as_frame=True)['frame']
```

Anoter way to bring in a dataset is how arize does it in their documentation:
[here](https://colab.research.google.com/github/Arize-ai/tutorials_python/blob/main/Arize_Tutorials/Data_Ingestion/Binary_Classification/binary_classification_metrics_only_batch_ingestion_tutorial.ipynb)

```python
df = pd.read_csv(
    "https://storage.googleapis.com/arize-assets/documentation-sample-data/data-ingestion/binary-classification-assets/binary_classification_data.csv?raw=true",
    index_col=False,
)
```

If you just want to make your own, you can do that too but, usually we want to work with large dataset, so this is really just for testing (and maybe should be in the pandas md)

One way to make your own dataset is to create a dict and convert it to a dataframe:
```python
d = {
    "pet" : ["cat", "dog", "cat", "cat", "bird"],
    "height" : [1, 2, 1.2, .9, .3],
    "length" : [1.6, 2.2, 1.5, 1, .1],
    "name" : ["alan", "bob", "chris", "dave", "ed"],
}

df = pd.DataFrame(d)
df

	pet	height	length	name
0	cat	1.0	1.6	alan
1	dog	2.0	2.2	bob
2	cat	1.2	1.5	chris
3	cat	0.9	1.0	dave
4	bird	0.3	0.1	ed
```
## Interrogate the data

Here are some useful things to underderstand the data.

Here are somethings you will want to know:
- How many **rows**? How many **columns**?
  - Find this by with `df.shape`
    ```python
    df.shape
    
    (100, 34)
    ```
- Does it have **nulls**? What are the **datatypes** of each column?
  - These both can be find with `df.info()`
    ```python
    df.info()
    
    <class 'pandas.core.frame.DataFrame'>
    Index: 100 entries, c5836b6d-f6b6-4372-b48f-c4472f251ff4 to f3601833-86d1-4a32-9c8b-76b1d6bdc98b
    Data columns (total 34 columns):
     #   Column                   Non-Null Count  Dtype  
    ---  ------                   --------------  -----  
     0   mean_radius              100 non-null    float64
     1   mean_texture             100 non-null    float64
     2   mean_perimeter           100 non-null    float64
     3   mean_area                100 non-null    float64
     ...
     32  predicted_score          100 non-null    float64
     33  predicted_label          100 non-null    object 
    dtypes: float64(32), object(2)
    memory usage: 31.4+ KB
    ```
- And other great one is `df.describe()` which will give you **count, mean, std, min, 25, 50, 75 percentiles, and max**, not to mention the **rows** and **columns** as well.
    ```python
    df.describe()
    
          mean_radius	mean_texture
    count	100.000000	100.000000
    mean	14.559050	19.580200
    std	3.816705	4.082374
    min	7.760000	10.380000
    25%	11.987500	16.335000
    50%	13.830000	19.225000
    75%	16.377500	22.535000
    max	27.420000	31.120000
    ```
## Clean the data
If there are nulls you must fix

### Impute the nulls (basically deal with the nulls)
If there are non-numericals you must fix

### Deal with non-numericals
I don't know how to do that yet. fill in when you do.

### Now we transform the data
There are a few was to transform your data.  If it's symetric, you will want to Center and Scale.  If it's skewed, you will want to do a Logarithmic Transform.

Note: You only want to Center and Scale Categorical non-continuous targets.  In other words, True not True. That goes for many categories as well, so Dog, Cat, Bird.  But not S, M, L.  It also doesn't make sense for values that don't fall into nice bins, like housing prices.  Centering housing prices on 0 actually loses the relevant data, so use this tool for prepping classification models.

#### Symetric data transforming
Two things we want to do with the data:
- Center
- Scale

Think of this as a matix in linear algebra.  We want to reduce a complex system to a unit matrix and an eigenvector.
Similar idea.  We will reduce the data so it scales to a range of a standard deviation of 1 ( -1 to 1) and we want to center the data on 0.

Lucklily we can do both of these with one equation. [The StandardScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html#sklearn.preprocessing.StandardScaler):
```python
z = (x - mu) / s
```
Where :

**s** is the standard deviation  
**mu** is the mean  
**x** is your column, or feature

Looking at `df.head()`, you should be able to see if the data is scaled.  Plotting it makes it even more obvious. This is a plot of a standardized, scaled dataset:
```python
# stand dev of mean_rad
s = df['mean_radius'].std()
mu = df['mean_radius'].mean()

#z = (x - u) / s
x = df['mean_radius']
((x-mu)/s).plot(kind='hist', grid=True, title="standard scaled");
```
![image](https://github.com/slkasper/learning/assets/17681521/4f9e1cf9-d29c-4c57-bb2a-feea41499aca)

##### Taking it a step further
This is good to get an idea what it looks like for one feature.  But will most likely want to apply this to all of our features, or columns.  Let's use python and pandas together to get some magic sauce going, shall we?

Let's write a function to generalize this equation.
```python
def stdscale(x:pd.Series)->pd.Series:
    '''Define Standard Deviation scaled where
    z = (x - u) / s
    '''
    s = x.std()
    mu = x.mean()
    return (x - mu)/s
```
Now we can use the apply with lambda method to _apply_ this to the whole df.
```python
scaled_df = df.apply(lambda x: stdscale(x))
```
Note, you will probably have to filter the dataframe a bit. It's unlikely you will use `df.apply`. It will probably be `df[feat].apply` where `feat = df.columns[:-1]` where the last row is `target` at the very least.  You might even filter it by a lot more, but this is just an idea.  Apply the lambda to only the features, being sure to exclude your target or other prediction columns that might be in the dataframe.

#### Skewed data transforming
If the data is not nicely symetric (think bell curve vs an off center skewed distribution), symetry is not a good fit.  So you will want to do a logarithmic transform.  Literally, take the log of your data.

(Note, the data in the symetric transform section above really looks a bit skewed so maybe log scaling would have been benfitial instead of treating it like a symetric model...)

Here's what the code looks like for log scaling:
```python
train["SalePrice"].plot(kind="hist", bins=20, grid=True);
```
![image](https://github.com/slkasper/learning/assets/17681521/cacd407c-411e-44a2-8212-9e71a68a00a7)

Wanna scale it?  Simple. Add `np.log(data.methods_on_data)` and Bob's your Uncle.  It looks like this:
```python
np.log(train["SalePrice"]).plot(kind="hist", bins=20, grid=True);
```
![image](https://github.com/slkasper/learning/assets/17681521/26835ca0-6d61-4720-999d-2b9e8a7dd4f6)




Note, to make sense of the data, you will have to exponentiate your y predictions back at the end, but that's not important for right now.
