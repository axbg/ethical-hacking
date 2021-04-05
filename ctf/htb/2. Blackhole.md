# Blackhole

## Keynotes

* file (command)
* steghide
* Caesar Cipher

## Tracking
1. Unarchived and obtained a file called `hawkins` with no extension

2. Executed file `hawkins` to identify its type - and identified it as .jpg

3. The `hawkins.jpg` contains a picture of Stephen Hawkings - it should be something related to steganography

4. Played around with a bunch of libraries - then found out about steghide
`steghide extract -sf hawking.jpg`

5. The extraction requires a password - tried multiple combinations and successfuly extracted flag.txt using 'hawking' password

6. The result in the file looks like an encoded string, so I started decoding it using base64

7. The first decoding doesn't do much, but if the result of the first decoding is decoded once again with base64, the result looks like this:

```
Efqbtqz Iuxxumy Tmiwuzs ime mz Qzsxuet ftqadqfuomx btkeuouef, oaeyaxasuef, mzp mgftad, ita ime pudqofad ar dqeqmdot mf ftq Oqzfdq rad Ftqadqfuomx Oaeyaxask mf ftq Gzuhqdeufk ar Omyndupsq mf ftq fuyq ar tue pqmft. Tq ime ftq Xgomeumz Bdarqeead ar Ymftqymfuoe mf ftq Gzuhqdeufk ar Omyndupsq nqfiqqz 1979 mzp 2009. Tmiwuzs motuqhqp oayyqdoumx egooqee iuft eqhqdmx iadwe ar babgxmd eouqzoq uz ituot tq pueogeeqe tue aiz ftqaduqe mzp oaeyaxask uz sqzqdmx. Tue naaw M Nduqr Tuefadk ar Fuyq mbbqmdqp az ftq Ndufuet Egzpmk Fuyqe nqef-eqxxqd xuef rad m dqoadp-ndqmwuzs 237 iqqwe. Tmiwuzs ime m rqxxai ar ftq Dakmx Eaouqfk, m xurqfuyq yqynqd ar ftq Bazfuruomx Mompqyk ar Eouqzoqe, mzp m dqoubuqzf ar ftq Bdqeupqzfumx Yqpmx ar Rdqqpay, ftq tustqef ouhuxumz mimdp uz ftq Gzufqp Efmfqe. Uz 2002, Tmiwuzs ime dmzwqp zgynqd 25 uz ftq NNO\â��e baxx ar ftq 100 Sdqmfqef Ndufaze.
TFN{Z3hqD_x3F_fT3_n4eFmDp5_S3f_K0g_p0iZ} 
```

8. The last line has the good format, but another kind of encoding was done so I started looking for means to decode it - and I found https://www.dcode.fr/en and played with multiple text ciphers 

9. In the end, I successfully decoded the flag with the Caesar's Cipher 