# Python - PyJail 1
**Category:** Cryptanalysis **Points:** 35 **Description:**
Statement
Retrieve validation’s password and get out of this jail.

Challenge connection informations :

| Host	        | challenge02.root-me.org
| Protocol      | SSH                                               |
| Port          | 2222                                              |
| SSH access    | ssh -p 2222 app-script-ch8@challenge02.root-me.org| 
| Username      | app-script-ch8                                    |
| Password      | app-script-ch8                                    | 

# Write-up
#### What’s to be done

### The login prompt says:
Welcome to my Python sandbox! Everything is in exit() function (arg == get the flag!)
======> so it looks like we need to call exit with a specific argument to get the flag

#### Analyzing what’s allowed
#### Trying some things around, we see there is not a lot left to call


```python
>>> x = 0x10
>>> print x
16
```


======> This tells us we are in python 2.7, in python3 print needs parenthesis
======> This also tells us that our input is eval’d


```python
>>> dir(exit)
NameError : name 'dir' is not defined
```


======> so that’s not an option here, we need to figure out this one locally...
```python
>>> exit.name
Denied
>>> exit.__str__
Denied
```


======> we can’t access builtin or standard properties
======> at this point we can suppose that exit() is a jail-function, not the standard builtin.



### Verify with:
```python
>>> print exit.veryUnlikelyToBeThere
AttributeError : 'function' object has no attribute 'veryUnlikelyToBeThere'
```


======> So this IS a function object


#### In a local python2.7 shell, let’s dir a user-defined function



```python
>>> def test():
...     print "test"
...
>>> test()
test
>>> dir(test)
['__call__', '__class__', '__closure__', '__code__', '__defaults__',
'__delattr__', '__dict__', '__doc__', '__format__', '__get__',
'__getattribute__', '__globals__', '__hash__', '__init__', '__module__',
'__name__', '__new__', '__reduce__', '__reduce_ex__', '__repr__',
'__setattr__', '__sizeof__', '__str__', '__subclasshook__',
'func_closure', 'func_code', 'func_defaults', 'func_dict', 'func_doc',
'func_globals', 'func_name']
```


#### Back in the jail, trying all available properties
#### The only interesting thing is exit.func_code, which is also an object.


```python
>>> print exit.func_code
<code object exit at 0xb7d85b60, file "/challenge/app-script/ch8/ch8.py", line 27>
```


#### Back to our local python2.7 shell, let’s dir(test.func_code)


```python
>>> dir(test.func_code)
['__class__', '__cmp__', '__delattr__', '__doc__', '__eq__',
'__format__', '__ge__', '__getattribute__', '__gt__', '__hash__',
'__init__', '__le__', '__lt__', '__ne__', '__new__', '__reduce__',
'__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__',
'__subclasshook__', 'co_argcount', 'co_cellvars', 'co_code',
'co_consts', 'co_filename', 'co_firstlineno', 'co_flags',
'co_freevars', 'co_lnotab', 'co_name', 'co_names', 'co_nlocals',
'co_stacksize', 'co_varnames']
```


#### Back in the jail, try all available properties
#### Trying every property, the only interesting thing we see is
```python
>>> print exit.func_code.co_consts
(None, 'flag-WQ0dSFrab3LGADS1ypA1', -1, 'cat .passwd', 'You cannot escape !')
```


### The second item looks pretty juicy, lets try
### to call exit with it as argument


```python
>>> exit('flag-WQ0dSFrab3LGADS1ypA1')
Denied
```


 Not allowed, try with a reference to the tuple item directly
 
 
```python
>>> exit(exit.func_code.co_consts[1])
Well done flag : YjHRUZEa9irCPS2llubR
```
