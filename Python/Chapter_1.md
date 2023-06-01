Python has numerous built-in and 3rd party modules for converting a JSON string into a Python dictionary object. The *json* module and its *loads* function invoked on each line in the sample file.

```python
import json
path = 'ch02/usagov_bitly_data2012-03-16-1331923249.txt'
records = [json.load(line) for line in open(path)]
```

The third line is a list comprehension, which is a concise way of applying an operation (like `json.loads`) to a collection of string or other objects. List comprehension offers a shorter syntax when you want to create a new list based on the values of an existing list.

