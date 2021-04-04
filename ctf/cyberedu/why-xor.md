# why-xor

## Keynotes

* XOR

## Tracking

1. Received a python script with the following content

```python
xored = ['\x00', '\x00', '\x00', '\x18', 'C', '_', '\x05', 'E', 'V', 'T', 'F', 'U', 'R', 'B', '_', 'U', 'G', '_', 'V', '\x17', 'V', 'S', '@', '\x03', '[', 'C', '\x02', '\x07', 'C', 'Q', 'S', 'M', '\x02', 'P', 'M', '_', 'S', '\x12', 'V', '\x07', 'B', 'V', 'Q', '\x15', 'S', 'T', '\x11', '_', '\x05', 'A', 'P', '\x02', '\x17', 'R', 'Q', 'L', '\x04', 'P', 'E', 'W', 'P', 'L', '\x04', '\x07', '\x15', 'T', 'V', 'L', '\x1b']

s1 = ""
s2 = ""

# ['\x00', '\x00', '\x00'] at start of xored is the best hint you get
a_list = [chr(ord(a) ^ ord(b)) for a,b in zip(s1, s2)]
print(a_list)
print("".join(a_list))
```

2. Knowing that the flag starts with `ctf`, it's easy to deduce from the hint that the first 3 values that the `xored` array should be XORed against should be identical - thus `ctf`

3. Modify the code to test against the `xored` array

```python
xored = ['\x00', '\x00', '\x00', '\x18', 'C', '_', '\x05', 'E', 'V', 'T', 'F', 'U', 'R', 'B', '_', 'U', 'G', '_', 'V', '\x17', 'V', 'S', '@', '\x03', '[', 'C', '\x02', '\x07', 'C', 'Q', 'S', 'M', '\x02', 'P', 'M', '_', 'S', '\x12', 'V', '\x07', 'B', 'V', 'Q', '\x15', 'S', 'T', '\x11', '_', '\x05', 'A', 'P', '\x02', '\x17', 'R', 'Q', 'L', '\x04', 'P', 'E', 'W', 'P', 'L', '\x04', '\x07', '\x15', 'T', 'V', 'L', '\x1b']
s2 = "ctf"

# ['\x00', '\x00', '\x00'] at start of xored is the best hint you get

a_list = [chr(ord(a) ^ ord(b)) for a,b in zip(xored, s2)]

print("".join(a_list))
```

4. By doing so, we know that the next value in the `s2` string XORed against the `xored` array should result in the `{` character, due to the format of the string

5. Trying to fill with another `c` will result in the correct value - thus an idea would be to `XOR` the `ctf` word multiple times against the original array - and it worked

```python
xored = ['\x00', '\x00', '\x00', '\x18', 'C', '_', '\x05', 'E', 'V', 'T', 'F', 'U', 'R', 'B', '_', 'U', 'G', '_', 'V', '\x17', 'V', 'S', '@', '\x03', '[', 'C', '\x02', '\x07', 'C', 'Q', 'S', 'M', '\x02', 'P', 'M', '_', 'S', '\x12', 'V', '\x07', 'B', 'V', 'Q', '\x15', 'S', 'T', '\x11', '_', '\x05', 'A', 'P', '\x02', '\x17', 'R', 'Q', 'L', '\x04', 'P', 'E', 'W', 'P', 'L', '\x04', '\x07', '\x15', 'T', 'V', 'L', '\x1b']
s2 = "ctf"

result = []

for x in range(0, len(xored), 3):
	for j in range(0, len(s2)):
		result.append(chr(ord(xored[x + j]) ^ ord(s2[j])))

print("".join(result))
```
*Disclaimer: of course, the first time I found the answer by copying `ctf` multiple times in the `s2` array and, afterwards, I beautified the code*