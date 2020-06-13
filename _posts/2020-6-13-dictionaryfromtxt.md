---
layout: post
title: Create Python dictionary from a txt file
---

Today I played with `dictionary` in Python to create a cheatsheet for future usage. Here are the steps to create a dictionary from a `.txt` data file. 

The example file is [state_abbr.txt](https://drive.google.com/open?id=1wHBZeUIVTrffyqpik1ABqUfV6XGqCo8E), which simply includes a header and two columns of information, state names and their abbreviations. 

## Open the file and read the text

Good time to check out the built-in function `open` in [Python 3.8 official documentation](https://docs.python.org/3/library/functions.html#open). The reading mode `r` is by default but it is a good habit to add it. The keyword argument `encoding` may be needed but not in this case. 

I Use the `with` statement to open the file saves the hassle to close the file. Some related examples [here](https://thispointer.com/python-open-a-file-using-open-with-statement-benefits-explained-with-examples/). The 'next()' function is used to skip the header line. 

```python
# Read in a txt file
with open ("state_abbr.txt", 'r') as file:
    next(file) # skip header line
    text = file.read()
```

## Prepare a nested list

To convert between string and list, these two built-in types come handy: `str.split()` (see [documentation](https://docs.python.org/3/library/stdtypes.html#str.split) and `str.join()` (see [documentation](https://docs.python.org/3/library/stdtypes.html#str.join)). 

The codes below split the string in two steps. First, I get a list with `split("\n")`. Each item is string with a state and its abbreviation, such as 'Minnesota, MN'. Then I use list comprehension to split each original item into a new list with two items, with `split(',')`. 

```python
list = text.split("\n")
list_2 = [i.split(',') for i in list]
```

## Convert the nested list into dictionary

Now that the list is in this format, it can be easily converted into a dictionary with `dict()`.  (see [documentation](https://docs.python.org/3/tutorial/datastructures.html)). 

```python
state_abbr = dict(list_2)
print(state_abbr)
```
<p>&nbsp;</p>