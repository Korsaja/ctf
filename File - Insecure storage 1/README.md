# File - Insecure storage 1
**Category:** Cryptanalysis **Points:** 20 **Description:**
### Statement
> Retrieve the user’s password.


# Write-up


Downloaded .mozilla folder as it was described in the last solution. But use this script

> https://hg.cryptobitch.de/firefox-passwords/file/226380cf2849/firefox_passwd.py


and run as
> python2 firefox_passwd.py
>FirefoxSite(id=1, hostname=u'http://www.root-me.org', httpRealm=None, formSubmitURL=u'http://www.root-me.org', 
usernameField=u'var_login', passwordField=u'password', 
encryptedUsername=u'MDoEEPgAAAAAAAAAAAAAAAAAAAEwFAYIKoZIhvcNAwcECGQiIGc9wcicBBDV2Zx+ouMBMu+QGgCAWJC8', 
encryptedPassword=u'MDoEEPgAAAAAAAAAAAAAAAAAAAEwFAYIKoZIhvcNAwcECL6IksL4y0rsBBCwsrL8AoQSAbNEoOvkOfbA', 
guid=u'{a5f14aac-a1c1-4206-89a6-7ff3bff1c9da}', encType=1, plain_username='shell1cracked', plain_password='F1rstP4sSw0rD')
