# Python - PyJail 2
**Category:** App - Script **Points:** 40 **Description:**
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

Here is my solution with only getattr and open:
```python
c=getattr(getout, dir(getout)[-6]); cd=getattr(c,dir(c)[-10]); f=open(cd); l=getattr(f, dir(f)[-8]); 
f=open(l()[30][27:34]); l=getattr(f, dir(f)[-8]); print l()
```


Details:

>c=getattr(getout, dir(getout)[-6]); cd=getattr(c,dir(c)[-10])


Call func_code.co_name, i.e. name of the source file: /challenge/shell/shell9/shell9.py
Now we cat read it:

> f=open(cd); l=getattr(f, dir(f)[-8])


l contains the source code of the challenge, line 30 we find os.system("cat .passwd").
We can retrieve the name of the file and read it:

> f=open(l()[30][27:34]); l=getattr(f, dir(f)[-8]); print l()


Which prints the desired flag.

