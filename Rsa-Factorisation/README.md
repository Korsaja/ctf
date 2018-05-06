# RSA - Factorisation 
**Category:** Cryptanalysis **Points:** 25 **Description:**
> Statement The validation password was encrypted using this public key.

**Ciphertext**
> e8oQDihsmkvjT3sZe+EE8lwNvBEsFegYF6+OOFOiR6gMtMZxxba/bIgLUD8pV3yEf0gOOfHuB5bC3vQmo7bE4PcIKfpFGZBA

# Write-up

I use **pycrypto** to find out the module **N** and the open exponent **e**

```python
from Crypto.PublicKey import RSA
key = RSA.importKey(open('pubkey.pem').read())
print('n = ',key.n)
print('e = ',key.e)
```
**N:**
>188198812920607963838697239461650439807163563379417382700763356422988859715234665485319060606504743045317388011303396716199692321205734031879550656996221305168759307650257059


**e:**
> 65537



Thanks https://factordb.com


**p:**
> 398075086424064937397125500550386491199064362342526708406385189575946388957261768583317


**q:**
> 472772146107435302536223071973048224632914695302097116459852171130520711256363590397527



Having this data we can get private key.
