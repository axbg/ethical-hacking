# Longbottom Locker

## Keynotes

* binwalk
* python pickle format
* pickle format parsing

## Tracking

1. Unarchived and run a static server to check the `index.html` - and it was a login page with a password field only

2. After a few hints, I found out about binwalk - I executed it on socute.jpg, the image that was not used in `index.html`, and found a `donotshare` binary included

```
binwalk -e socute.jpg
```

3. The donotshare file contained a weird looking string: searched it on Google and found something related to `python pickle` and an example of how to load it

4. The printed result is even stranger - and here's when I relied on the Internet and I accidentaly found how the values in the loaded pickle should be concatenated

```python
with open('file','rb') as f:
    pck = pickle.load(f)

    outstr = ''

    for item in pck:
        for char, n in item:
            outstr += char
        outstr += '\n'

    print(outstr)
```

5. The print result this time is a graphic made of letters which was, actually, the password for the login page

6. Entering this string in the index.html password input printed the flag