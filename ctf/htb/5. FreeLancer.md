# FreeLancer

## Keynotes

* dirb
* sqlmap
* dowloading files with sqlmap

## Tracking

1. Executed dirb on the target URL and found the */administrat* path

2. Found something, but decided to continue with sqlmap and started looking into the database where I was able to obtain the hash of safeadm's user password: `$2y$10$s2ZCi/tHICnA97uf4MfbZuhmOZQXdCnrM9VM9LBMHPp68vAXNRf4K`

3. I tried to look it up, but I couldn't find anything to help me crack it, so I continued looking into the code

4. Using sqlmap, I've started downloading files - started with `mail/contact_me.php`, then `index.php` and, in the end, `/administrat/index.php`

5. Here I observed that, if the login in the session cookie is ok, it would actually redirect to `/panel.php`, which I successfully found under `/administrat/panel.php`

6. Downloaded it using sqlmap, and found the flag