# xorxorxor

## Keynotes

* XOR
* bytes flipping

## Tracking

1. Downloaded the source code and obtained two files, one with what seemed like the encrypted flag and one with the source code used to achieve the encryption

2. The code looks like this
    ```python
    import os
    flag = open('flag.txt', 'r').read().strip().encode()

    class XOR:
        def __init__(self):
            self.key = os.urandom(4)
        def encrypt(self, data: bytes) -> bytes:
            xored = b''
            for i in range(len(data)):
                xored += bytes([data[i] ^ self.key[i % len(self.key)]])
            return xored
        def decrypt(self, data: bytes) -> bytes:
            return self.encrypt(data)

    def main():
        global flag
        crypto = XOR()
        print ('Flag:', crypto.encrypt(flag).hex())

    if __name__ == '__main__':
        main()
    ```

3. Being an XOR, an operation which applied two times result in the same output, it means that we can use the code to decrypt the flag

4. I started playing around with the implementation and observed that the key is a string of only 4 bytes - we know for sure that our flag should start with the `HTB{` - Bingo

5. Started bruteforcing the program by running it multiple times - a new key will be generated, thus a new decrypted variant will be outputed each time

6. My solution was to observe the values in the key when the first 4 characters are decrypted corectly - not all at once, of course, because that would mean to actually guess the password, but one by one

7. First time I did it manually and I printed out the keys used to decrypt each letter and I obtained
    - H - `[`
    - T - `\x1e`
    - B - `\xb4`
    - { - `\x9a`

8. This means that key used to encrypt the flag is `b'[\x1e\xb4\x9a` and, because the encryption algorithm used is XOR, we can decrypt the flag by running the encryption operation once again (I also removed the decryption method because it was useless)

9. I did just that and I obtained the flag, then, because I like automating things, I modified the original program to bruteforce the key and decrypt the message at once
    ```python
    import os
    flag = open('output.txt', 'r').read().strip()

    class XOR:
        def __init__(self):
            self.key = os.urandom(4)
        
        def encrypt(self, data: bytes) -> bytes:
            xored = b''
            
            for i in range(len(data)):
                xored += bytes([data[i] ^ self.key[i % len(self.key)]])
            return xored
        
    def main():
        global flag

        key = [0, 0, 0, 0]
        
        while True:
            try:
                crypto = XOR()
                result = crypto.encrypt(bytes.fromhex(flag)).decode('utf-8')

                if(result[0] == 'H'):
                    key[0] = crypto.key[0:1]
                if(result[1] == 'T'):
                    key[1] = crypto.key[1:2]
                if(result[2] == 'B'):
                    key[2] = crypto.key[2:3]
                if(result[3] == '{'):
                    key[3] = crypto.key[3:4]

                if key[0] != 0 and key[1] != 0 and key[2] != 0 and key[3] != 0:
                    actual_key = key[0] + key[1] + key[2] + key[3]
                    crypto.key = actual_key

                    print(crypto.encrypt(bytes.fromhex(flag)).decode('utf-8'))
                    break               
            except:
                pass

    if __name__ == '__main__':
        main()
    ```