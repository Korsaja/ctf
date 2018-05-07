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

# The login prompt says:
Welcome to my Python sandbox! Everything is in exit() function (arg == get the flag!)
======> so it looks like we need to call exit with a specific argument to get the flag

#### Analyzing what’s allowed
# Trying some things around, we see there is not a lot left to call
```python
>>> x = 0x10
>>> print x
16
```
