# TwoForOne

## Keynotes

* RSA 
* [PyCryptodome](https://pycryptodome.readthedocs.io/en/latest/src/public_key/rsa.html)
* [Common Modulus Attack](https://github.com/HexPandaa/RSA-Common-Modulus-Attack)

## Tracking

1. Downloaded the files and started looking through them

2. From the start I assumed that both key1 and key2 are equivalent in a way, being related to the same private key which was used to encrypt both messages

3. I decoded the keys and extract their components using some web tools and I've obtained the same `n (modulus)` for both of them and different `e (public exponent)`

4. Started to look-up how RSA actually works and, after multiple searches, I found an explanation of the [Common Modulus Attack](https://crypto.stackexchange.com/questions/81807/how-to-recover-a-message-with-shared-modulus-for-textbook-rsa)

5. Following the steps explained there and `PyCryptodome` I tried to compute the unencrypted message, but, due to large numbers being used, I was not able to finish the computation using the naive approach
    ```python
    import base64
    from Crypto.PublicKey import RSA

    with open('key1.pem', 'r') as key1_file:
        key1 = RSA.import_key(key1_file.read())
        e1 = key1.e
        n1 = key1.n

    with open('key2.pem', 'r') as key2_file:
        key2 = RSA.import_key(key2_file.read())
        e2 = key2.e
        n2 = key2.n

    if n1 != n2:
        print('n1 is not the same as n2')
        exit(1)

    n = n1

    # if gcd(e1, e2) = 1 we go on and we compute
    # a * e1 + b * e2 = 1
    # because gcd(e1, e2) = 1 
    #   => e1 * a % e2 = 1
    #   => e2 * b % e1 = 1
    # computed them using an online calculator
    a = 133132
    b = -25421

    with open('message1', 'r') as m1_file:
        m1_string = m1_file.read()
        m1 = int.from_bytes(base64.b64decode(m1_string), byteorder='big', signed=False)

    with open('message2','r') as m2_file:
        m2_string = m2_file.read()
        m2 = int.from_bytes(base64.b64decode(m2_string), byteorder='big', signed=False)

    # the result will be the decrypted message as a number
    # the classical computation approach is pretty intense and will error out in the end so I decided to try another way
    decrypted_message = pow(m1, a) * pow(m2, b) % n

    print(decrypted_message)
    ```

6. I started looking for tools to help me compute the equation, but instead I found a script which does more than that - [Common Modulus Attack](https://github.com/HexPandaa/RSA-Common-Modulus-Attack)

7. Executed the script with proper input and *obtained the flag!*
    ```bash
    python3 rsa-cm.py -c1 message1 -c2 message2 -k1 key1.pem -k2 key2.pem
    ```