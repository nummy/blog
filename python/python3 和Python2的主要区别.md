- print is no longer a statement but a function instead, so the parenthesis is 

now obligatory.

- Catching exceptions changed from except exc, var to except exc as var.
- The <> comparison operator has been removed in favor of !=
- from module import * is now allowed only on a module level, no 
  longer inside the functions.
- from .[module] import name is now the only accepted syntax for relative 
  imports. All imports not starting with the dot character are interpreted as 
  absolute imports.
- The sort() function and the list's sorted() method no longer accept the cmp 
  argument. The key argument should be used instead.
- Division expressions on integers such as 1/2 return floats. The truncating 
  behavior is achieved through the // operator like 1//2. The good thing is 
  that this can be used with floats too, so 5.0//2.0 == 2.0.



Here is a list of all the available` __future__ `statement options that developers 
concerned with 2/3 compatibility should know:

- division: This adds a Python 3 division operator (PEP 238)
- absolute_import: This makes every form of import statement not starting 

with a dot character interpreted as an absolute import (PEP 328)

- print_function: This changes a print statement into a function call, so 

parentheses around print becomes mandatory (PEP 3112)

- unicode_literals: This makes every string literal interpreted as Unicode 

literals 





Python3字典提供的`keys()`、`values()`、`items()`方法返回的对象不再是列表，而是一个view 对象。

view对象提供了字典内容的动态展示，所以当字典修改之后，view对象也会对应修改。

```python
>>> words = {'foo': 'bar', 'fizz': 'bazz'}
>>> items = words.items()
>>> words['spam'] = 'eggs'
>>> items
dict_items([('spam', 'eggs'), ('fizz', 'bazz'), ('foo', 'bar')])
```



