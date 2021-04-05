# Flippin Bank

## Keynotes

* Padding Oracle attacks
* AES
* PKCS7

## Tracking

1. Fixed the values for key and IV on a local copy of the program, to have consistent results

2. Played around with different values in a local example, but didn't observe anything helpful

3. Read about `bit flipping` and `Padding Oracle attacks` on when searching for AES/CBC vulnerabilities

4. Everywhere in the program, the string `'logged_username=' + user +'&password=' + passwd` is used instead of the raw credentials, so this is the actual value we should analyze

5. Split the evaluated value into 16-bytes blocks
```
logged_username=
admin&password=g
0ld3n_b0y
```

6. Observed that the padding will be composed of 7 bytes with value 0x07 (according to the PKCS7 standard)

7. Tried to play around with something similar, like 'admin/g0ld3n_b0i' and obtained the following result, which I split to be able to map the result to the input
```
32b074567245b47b3a9fa99269901d6a
68c7666708449f9708c9487a59b125e1
20f522d6a71a7dff145853933d8d002e
```

8. Started playing around with the values placed in the same position as the letter I replaced, 'i', but observed that padding gets broken if I try to modify directly the last byte

9. So, I started flipping bits in the second byte, in the same position, but ended up messing the padding as well, or relevant values in the evaluated string if I tried to modify something else

10. Naturally, went to the first byte and started playing around, on a local example, with different bytes and, in the end, I observed that modifying the last bit of the first byte changes the value of the last bit in the second byte, so I've changed the initial input to 'admin/f0ld3n_b0y' and obtained the following result
```
32b074567245b47b3a9fa99269901d6a
68c7666708449f9708c9487a59b125e1
22fd79a867b2da6094d621b2669fb9f9
```

11. On this result, I increased the last bit of the first byte with 1, to b, and it worked - the flag was printed
``` 
32b074567245b47b3a9fa99269901d6b
68c7666708449f9708c9487a59b125e1
22fd79a867b2da6094d621b2669fb9f9
```