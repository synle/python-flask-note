# python-flask-note

## Tools / Libraries

### Virtual Env

Use this to create an isolated python environment.

- https://docs.python.org/3/library/venv.html

```
python3 -m venv /path/to/new/virtual/environment
```

### Debugging with debugpy

- https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection

```bash
python -m pip install --upgrade debugpy
```

```python
import debugpy

# 5678 is the default attach port in the VS Code debug configurations. Unless a host and port are specified, host defaults to 127.0.0.1
debugpy.listen(5678)
print("Waiting for debugger attach")
debugpy.wait_for_client()
debugpy.breakpoint()
print('break on this line')
```

### GraphQL with Flaks

Use `ariadne`

```bash
pip install flask ariadne flask-sqlalchemy flask-cors
```

- https://www.apollographql.com/blog/graphql/python/complete-api-guide/

### Run flake8 linting

```bash
python -m flake8
```

### Run pytest

#### Run all test

```bash
python -m pytest
```

#### Run a single test

```bash
python -m pytest test/integration/test_employee_api.py
```

#### Run a test match keyword

```bash
python -m pytest -vvl -k "test_get_employee"
```

#### run test with debugger

```bash
python -m pytest -vvl -k "test_get_employee" --pdb --capture=no
```

## Code

### Simple for loop

```python
fruits = ['apple', 'banana', 'mango']
for fruit in fruits:
    print(fruit.capitalize())
```

```python
for n in range(2, 10):
    for x in range(2, n):
        if n % x == 0:
            print(n, 'equals', x, '*', n/x)
            break
```

### args and kargs

- https://book.pythontips.com/en/latest/args_and_kwargs.html

### decorator

- https://book.pythontips.com/en/latest/decorators.html

```python
from functools import wraps

def logit(func):
    @wraps(func)
    def with_logging(*args, **kwargs):
        print(func.__name__ + " was called")
        return func(*args, **kwargs)
    return with_logging

@logit
def addition_func(x):
   """Do some math."""
   return x + x


result = addition_func(4)
# Output: addition_func was called
```

### ternary

- https://book.pythontips.com/en/latest/ternary_operators.html

```python
# with if
value_if_true if condition else value_if_false

# with or
True or "Some"
```

### set datastructure

- https://book.pythontips.com/en/latest/set_-_data_structure.html

```python
# duplicates
some_list = ['a', 'b', 'c', 'b', 'd', 'm', 'n', 'n']
duplicates = set([x for x in some_list if some_list.count(x) > 1])
print(duplicates)
# Output: set(['b', 'n'])


# intersection
valid = set(['yellow', 'red', 'blue', 'green', 'black'])
input_set = set(['red', 'brown'])
print(input_set.intersection(valid))
# Output: set(['red'])


# difference
valid = set(['yellow', 'red', 'blue', 'green', 'black'])
input_set = set(['red', 'brown'])
print(input_set.difference(valid))
# Output: set(['brown'])

```

### map reduce filter

#### map

- https://book.pythontips.com/en/latest/map_filter.html

```python
# simple map
items = [1, 2, 3, 4, 5]
squared = list(map(lambda x: x**2, items))


# more complex
def multiply(x):
    return (x*x)
def add(x):
    return (x+x)

funcs = [multiply, add]
for i in range(5):
    value = list(map(lambda x: x(i), funcs))
    print(value)
```

#### filter

```python
number_list = range(-5, 5)
less_than_zero = list(filter(lambda x: x < 0, number_list))
print(less_than_zero)
# Output: [-5, -4, -3, -2, -1]
```

#### reduce

```python
from functools import reduce
product = reduce((lambda x, y: x * y), [1, 2, 3, 4])
```

### Exceptions

```python
# exception
try:
    file = open('test.txt', 'rb')
except EOFError as e:
    print("An EOF error occurred.")
    raise e
except IOError as e:
    print("An error occurred.")
    raise e

# with finally
try:
    file = open('test.txt', 'rb')
except IOError as e:
    print('An IOError occurred. {}'.format(e.args[-1]))
finally:
    print("This would be printed whether or not an exception occurred!")


# try with else
try:
    print('I am sure no exception is going to occur!')
except Exception:
    print('exception')
else:
    # any code that should only run if no exception occurs in the try,
    # but for which exceptions should NOT be caught
    print('This would only run if no exception occurs. And an error here '
          'would NOT be caught.')
finally:
    print('This would be printed in every case.')

```

### classes

- https://book.pythontips.com/en/latest/classes.html

#### Class 1

```python
class Cal(object):
    # pi is a class variable
    pi = 3.142

    def __init__(self, radius):
        # self.radius is an instance variable
        self.radius = radius

    def area(self):
        return self.pi * (self.radius ** 2)

a = Cal(32)
a.area()
# Output: 3217.408
a.pi
# Output: 3.142
a.pi = 43
a.pi
# Output: 43

b = Cal(44)
b.area()
# Output: 6082.912
b.pi
# Output: 3.142
b.pi = 50
b.pi
# Output: 50

```

#### Class 2

Python’s classes are famous for their magic methods, commonly called dunder (double underscore) methods. I am going to discuss a few of them.

```python
class GetTest(object):
    def __init__(self):
        self.info = {
            'name':'Yasoob',
            'country':'Pakistan',
            'number':12345812
        }

    def __getitem__(self,i):
        return self.info[i]

foo = GetTest()

foo['name']
# Output: 'Yasoob'

foo['number']
# Output: 12345812
```

## Flask specifics

- [werkzeug](https://werkzeug.palletsprojects.com/en/2.2.x/) - is a comprehensive WSGI web application library. It began as a simple collection of various utilities for WSGI applications and has become one of the most advanced WSGI utility libraries.

### Flask blueprint

- https://flask.palletsprojects.com/en/2.2.x/blueprints/

```python
from flask import Blueprint, render_template, abort
from jinja2 import TemplateNotFound

simple_page = Blueprint('simple_page', __name__,
                        template_folder='templates')

@simple_page.route('/', defaults={'page': 'index'})
@simple_page.route('/<page>')
def show(page):
    try:
        return render_template(f'pages/{page}.html')
    except TemplateNotFound:
        abort(404)
```

### Flask jsonify

- https://stackoverflow.com/questions/45412228/sending-json-and-status-code-with-a-flask-response

```python
from flask import jsonify

@app.route('/login', methods=['POST'])
def login():
    data = {'name': 'nabin khadka'}
    return jsonify(data)
```

As of Flask 1.1, the return statement will automatically jsonify a dictionary in the first return value. You can return the data directly:

```python
return data, 200
```

### Global HTTP Error handlers

- https://flask.palletsprojects.com/en/2.2.x/errorhandling/
- https://flask.palletsprojects.com/en/2.2.x/blueprints/

```
@app.errorhandler(werkzeug.exceptions.BadRequest)
def handle_bad_request(e):
    return 'bad request!', 400

# or, without the decorator
app.register_error_handler(400, handle_bad_request)

## or simpler
@app.errorhandler(404)
@app.errorhandler(405)
def _handle_api_error(ex):
    if request.path.startswith('/api/'):
        return jsonify(error=str(ex)), ex.code
    else:
        return ex
```
