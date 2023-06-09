# Web Scraping
Some common things we run into with web scraping

## In the API - Reading the Data
When grabbing the info from the API, you may need to get the client ID and client secret.  I'll write that up a little later.

For now, I wanted to just talk about the stuff we run into with cleaning the url. 

The url often needs parameters in the form of a dict

Normally, the root url will look like this:
```python
root_url = "https://www.googleapis.com/books/v1/volumes"
```
We want to build on this with a `?` for the search when the API follows the RESTful format. To do that, often we start with a dict and turn it into a string to append or use an f-string to complete the URL.

Most of the time, we are doing similar things each time, so I wanted to create some functions that should be usable anywhere.

<!---
```python
def clean_params(params):
    #replace ' ' w/ '+' in dict values AND allow values to be ints
    x={}
    for k,v in params.items():
        if type(v)==str:
            v = v.replace(' ', '+')
        x[k] = v        
    return x

params = {'q': 'mark twain', 'maxResults': 20}
clean_params(params)
)
```
--->
**Tzvi showed me you don't need to worry about the `' '`.  The requests get module adds a '%20' for spaces even if it looked wrong in the lab.
So now this should just be the code below, taking out the whole clean_param thing. just do the join on `param.items()` instead

Now you need to take this dictionary of clean values and turn then into a string.  So use a join on '&' and an f=string
```python
def make_params(params:dict)->str:
    x = clean_params(params)
    return '&'.join([f'{k}={v}'for k,v in x.items()])
```

Then you take this clean params and concat with the `root_url` and Bob's your uncle.

## Now use the correct api url to pull in the data as .json
We use requests module
```python
import requests #top of your file

def response_json(url):
    response = requests.get(url)
    return response.json()
```

## Testing
Write test files in a test folder. I'll write this up another time.

For now, just know you run `pytest` in the kickoff folder.  

### Using console.py file
When troubleshooting, create the console.py file in the same folder in an interactive session. Make sure console has appropriate import statements in vscode to see all the children files and functions that you want to test.  

In bash, run an interactive session running the consule.py file. Then you can run your interactive python commands and you should have access to all the functions you need to test.

For ipython, in your bash, be in the correct folder to run console.py. type:
```python
ipython
```
Then you should be in the interactive area.  But ipython needs you to still open the file. So type:
```python
%run console
```
Note that in `ipython` you NEED `%run` and you DON'T need .py at the end.

To run in python interactive:
```
python -i console.py
```
-i requires you to quit between saves when you make changes, but with ipython you only need to rerun the %run line. It doesn't matter which you use, as long as you know which you're in. iPython exits with just `exit` and python -i exits with `exit()`.

### Using Pytest

To test with pytest, if you are trying the test-driven coding method, you can simplify it by not running every test in every file at once.  You should have many test files in your tests folder with many test functions.  You can simplify functions in a file by commenting out the ones you aren't using yet.

But to run just one file at a time, you need to change the way you call pytest. To run just one file so you can test just one src file, use this code in bash:
```bash
pytest ./tests/<test_client>.py
```
where test_client.py is the current file you are testing -- ie, your running `pytest` on `test_client.py` to test the functions in `client.py`.  Got it?

Otherwise, you can run pytest on everything, with this code in you kickoff location:
```python
pytest
```

But doing it this way might be hard to see what your looking at and its a little bothersome to be constantly working through errors, because it will always error out until you are 100% done.

### Proper setup for test files/functions
For Pytest, have your test file start with `test_<name>.py` and have each function called 
```python
def test_<name_of_function_being_tested>():
```
Note that <name_of_function_being_tested> doesn't have to exactly match the function being tested. In fact, it should be more descriptive of *exactly* what you are testing. (see the examples below).

Also note that test functions do not have arguements.  Call the func you are testing with the correct arguements in the test function and you'll be fine.

Instead of return statements, test functions use assert statements.
You assert that the variable you just set up will exactly equal some value.  

Here are a couple of simple examples showing all these best practices/rules:

```python
def test_main_ingredient_url_returns_the_url_to_search_by_ingredient():
    main_ingredient = 'chicken'
    assert main_ingredient_url(main_ingredient) == 'https://www.themealdb.com/api/json/v1/1/filter.php?i=chicken'
    
def test_request_by_recipe():
    results = request_by_recipe('pizza')
    assert results['meals'][0]['strMeal'] == 'Pizza Express Margherita'
```

# Use Functions to coerce the data

Whether you do test-driven coding or not, once you have the results from your api url, you will most likely have to coerce the data to get the info you need.  

One thing that is always the same: the data is messy.  Icky even. So sometimes you have to dig into weird structures like lists of dicts with dicts inside, or worse. The digging is where I get lost, so I wanted to write up this little helper.  This is not an exact roadmap, but it gives a good process to use for whatever you run into.

## 1 - View the data to see what you're working with

First, no matter if you have a dict with lists and dicts, or a list with dicts and dicts, or whatever, the first thing is to look at the first element. In this class, we are bringing in our results as .json files. Here is an example of one of the worst ones we ran into.  Looking at the first element, here is what we see:
```python
make_request("https://www.googleapis.com/books/v1/volumes?q=mark+twain")

>>>{'kind': 'books#volumes',
 'totalItems': 3225,
 'items': [{'kind': 'books#volume',
   'id': '1oo8EAAAQBAJ',
   'etag': 'vGL4iJ6uDkA',
   'selfLink': 'https://www.googleapis.com/books/v1/volumes/1oo8EAAAQBAJ',
   'volumeInfo': {'title': 'Who Was Mark Twain?',
    'authors': ['April Jones Prince', 'Who HQ'],
    'publisher': 'Penguin',
    'publishedDate': '2004-05-24',
   
   #info cut here
```
This one is a Dict with only 3 keys, but one has a list for values, and that list has dicts inside of dicts.  The info we actually wanted was in `volumInfo` which was a bit trickier than I had time to figure out for this quiz.  

If you have trouble understanding what you are looking at, copy all the json result variable into [this link](https://www.cleancss.com/python-beautify/) and it will clean up the formatting so you can see what your data looks like. -- Don't worry, I saved this as a bookmark in chrome too!

Okay, now that we've interogated our data a bit and we know what we're dealing with, time to coerce it.

## 2 - Break it down.  
What do you want?

We wanted ` 'title', 'authors', 'publishers'` etc, which luckily this time are all in one level.  Good to know, but still we need to get into that level. 

For for this one, going from the outside in, we have need to go dict->list->dict->dict.  

## 3 - Work with just one element first
Just to be clear, I'm going to show the API call here so we know all the info we're working with.

```python
import requests

root_url = "https://www.googleapis.com/books/v1/volumes?q=mark+twain"

response = requests.get(root_url)
book_results = response.json() #<---this is what we care about
```
Since we have a dict as the outer level in this case, we call the first key that we need.  Assign it to something to make it easier to work with. It is a good idea at this point to test this in your console.py file.
```python
items = book_response['items']
```
Now look into the next level. It's a list.  When we need to dig into a list, the first item is what we need.  This often means when we apply to to the whole result, we will need a for loop that iterates over every element in the list. But for now, just look at the first element.  

We can call it first element for clarity. We will be able to work with it that way for now before we know what our next steps are.  We will clean it later once we get to it.
```python
first_vol_info = items[0]['volumeInfo']
```
Hooray! We dug deep enough that we are at the dictionary we need.
Okay, now wrap it in a function
```python
def single_info(response:dict)->dict:
    items = response['items']
    first_vol_info = items[0]['volumeInfo']
    return first_vol_info
```
Notice you are returning a single dict. Also notice, that we want to keep in mind the arguments we will need in the end.  We ultimately want to use 'response' as an argument direct from the json info and be able to get out our end info.  Try to keep feeding in just that info if you can, unless you are going to feed in the return of another function.

## 4 - Make it work for many

You should be able to adjust this now to get the a list of volumeInfo dicts.  Eventually, we will reduce that dict to only show some of the keys we want, but we're not on that step yet.  Try not to get ahead of yourself.  Let's just take this single item function, and rework it to get a list.

Sometimes, it's best to call on your single_function when you are designing your 'make it work for many' function. That won't work here because the list [0] in the single item throws it off.

```python
#getting into the dicts/lists for volumeInfo
def dig_into_vol_info(response)->list:
    book_items = response['items']
    vol_infos = [item['volumeInfo'] for item in book_items]
    return vol_infos
```
Note you are returning a list of dicts now. This one also only takes 'response' as an arguement.

You could probably write a function for just getting into `book_response['items']` but I think that's a bit of over kill. I think what we did here is okay for now.

## 5 - Get a smaller/cleaner result
This step will differ for each problem. For this one, we wanted a simplified dictionary from a large dict. I've run into it a few times, so I thought I'd write it up for posterity.

So now that we have a list of dicts and let's widdle it down to just what we care about.  We might get a list of keys to work with.  I wrote this function to handle that, hoping I should be able to use this for any situation like this in the future.  check it:

### --- Do it for one ---:
```python
#build a single smaller dict from a dict
def reduced_dict(any_dict:dict, keys:list)->dict:
    return{k:v for k,v in any_dict.items() if k in keys}
```
This takes a list of any dict keys and builds a new dict with only those keys if they are in the original dictionary.

Now you would combine these fuctions. In this example, `keys = ['title', 'authors']` which not only serves as arguments, but also sets default values so if none are given, your list will fill with titles and authors.

### --- Do it for many ---:
```python
#make a list of your smaller dicts
def selected_attrs(response, keys = ['title', 'authors']):
    vol_infos = dig_into_vol_info(response)
    return [reduced_dict(vol_info,keys) for vol_info in vol_infos] 
    #call to single fuction that gets used over a list comp
```
### Lambda sorted
```python
#sort a list by anything in the list using lambda
In [9]: mylist = [(3, 5, 8), (6, 2, 8), (2, 9, 4), (6, 8, 5)]
In[10]: sorted(mylist, key=lambda x: x[1], reverse=True)
```
```
 #another example
  user = [{"name": "Dough", "age": 55}, 
            {"name": "Ben", "age": 44}, 
            {"name": "Citrus", "age": 33},
            {"name": "Abdullah", "age":22},
            ]
    print(sorted(user, key=lambda el: el["name"]))
    print(sorted(user, key= lambda y: y["age"]))
```
