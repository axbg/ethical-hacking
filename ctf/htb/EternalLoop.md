# Eternal Loop

## Keynotes

* bash scripting
* brute-force
* fcrackzip
* rock-you list
* strings

## Tracking

1. Downloaded and unarchived the archive

2. Observed that the archive stored inside it was also archived and, after trying multiple combinations, found out that the password is, actually, the name of another archived stored inside it and so on

3. Created a small bash script to automate the "eternal" unarchiving process (and I'm glad I did because there were somewhere around 500 layers)

```bash
# the name of the second archive
initial_value=5900

while : 
do
	next=$(unzip -l "$initial_value" | awk 'FNR == 4{split($4, a, ".") ;print a[1]}')
	
	echo $next

	result=$(unzip -P "$next" "$initial_value")
	
	initial_value=$next	
done
```

4. The last archived contains a file named DoNotTouch, but trying to unarchive it using the name of the inner file as password didn't work

5. Brute-forced the password using fcrackzip & rockyou.txt and obtained the 'letmeinplease' password

```
fcrackzip -u -D -p 'rockyou.txt' 6969.zip
```

6. The obtained file, DoNotTouch, is binary, but I used strings to grep something that has the flag format and obtained the flag

```
streams DoNotTouch | grep HTB{
```

7. I later found out that DoNotTouch is actually a sqlite database