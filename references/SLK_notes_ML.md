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
### Fix the nulls

If there are non-numericals you must fix
### Deal with non-numericals

### Now we scale/transform the data

Two things we want to do with the data: 

Looking at `df.head()`, you should be able to see if the data is scaled 

Centering
Scale = 

Standardize = 

