# Flask

Creating a flask file is really quite simple. It's the organizing and adding SQL that takes all the work.
Before we get to that, let's get to the basics.

## Making a hello world Flask app
I do not remember this install stuff.  I'm starting from the point where you've got flask and you can just import it.

This is all you need to run a simple Hello World webpage:
```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def index():
    return "Hello World"

app.run(debug=True)
```

Done. That's pretty amazing coming from how much more work it took back in the java days, right!

Ok, this is how you test a simple flask file. Now we really want to use it, we will want a specific route and then another more granular route.
ie., espn.com is the index, then next route is espn.com/player then more granularly, espn.com/player/<id>

That will look like this:

```python
 from flask import Flask

app = Flask(__name__)

@app.route('/')
def index():
    return "This is the Flask School Lab"

@app.route('/teachers')
def teachers():
    '''List all the teachers'''
    return 'I will add SQL stuff here to display teacher info'

@app.route('/teachers/<id>')
def single_teacher(id):
    '''Grab info for just one teacher '''
    return 'I will add SQL stuff for a single teacher'

app.run(debug=True)
```

Now lets add some info to the routes.  We need to use SQL now. See the Postgres for details.

Setting it up:
```python
@app.route('/teachers')
def teachers():
    '''List all the teachers'''
    #add psycopg2 connection 
    #add cursor
    #do sql query
    #fetchall
    #take list of tuples from fetchall and make them in a format that works (create list of obj)
    #return list of objs
```
