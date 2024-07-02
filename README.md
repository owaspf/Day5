# Day5 : Cryptography; Data Corruption; haching; Authentication
## Analyzing OpenSSL certificate
* Video 1 : Introduction OpenSSL
```  
man openssl
```
```  
openssl s_client www.google.fr:443
```  
```  
ping 8.8.8.8
```  
```  
dhcp client eth0
```  
```  
openssl s_client www.google.fr:443
```  
Copy the certificate 
```  
nano certificate
```  
Paste the certificate </br>
See the certificate
```  
openssl x509 -in certificat -text -noout
```
See only subject
```  
openssl x509 -in certificat -subject -noout
```
See subject and issuer
```  
openssl x509 -in certificat -subject -issuer -noout
```
See subject, issuer and dates
```  
openssl x509 -in certificat -subject -issuer -dates -noout
```
* Video 2 : Chiffrement symétrique 
```
man  openssl | grep base64
```
coding "Bonjour" in base64
```
echo "Bonjour" | openssl base64
```
Decoding in base 64
```
echo "Ym9uam91cgo="  | openssl base64 -d
```
Create file test using : 
```
cat <<h>> test
```
Tape ante terminate by h </br>
```
Bonjour
```
```
h
```
Create file test  second method using echo and >
```
echo "Bonjour" > test2
```
Coding in base 64
```
openssl enc -base64 -in test
```
or
```
openssl enc -base64 -in test2
```
```
man openssl | grep cipher
```
```
openssl list -cipher-algorithms
```
```
rm test.cipher
```
```
cat test
```
```
openssl enc -AES-256-CBC -in test -out test.enc
```
```
openssl enc -AES-256-CBC -in test -out test.enc -iter 2
```
```
ls
```
```
cat test.enc
```
Erron on deciphering, binary not base64
```
openssl enc -AES-256-CBC -in test.enc -iter 2 -d
```
```
openssl enc -AES-256-CBC -a -in test -iter 2 -out test1.enc
```
```
cat test1.enc
```
```
openssl enc -AES-256-CBC -a -in test1.enc -iter 2  -d
```
```
openssl enc -AES-256-CBC -a -p -in test -iter 2 
```
* Video3 : Hachage du mot de passe avec SHA512
```
openssl help
```
```
openssl dgst -sha512 test
```
```
openssl dgst -sha1 test
```
```
openssl dgst -sha256 test
```
```
openssl dgst -sha256 test > test.hash
```
```
cat test.hash
```
```
nano test
```
add space after Bonjour
```
openssl dgst -sha256 test 
```
Compute hash of password
```
openssl passwd -6
```
```
openssl passwd -6 -salt <random>
```
* Video 4 : Chiffrement Asymétrique avec RSA
```
openssl genrsa -out clef 4096
```
```
ls
```
```
file clef
```
```
cat clef
```
let's cipher the key
```
openssl rsa -in clef -des3 -out clef1
```
```
ls
```
```
file clef1
```
```
cat clef1
```
```
ls
```
```
openssl genrsa -des3 -out cleftest 512
```
```
cat cletest
```
```
ls
```
```
openssl rsa -in clef -pubout -out clef.pub
```
```
ls
```
```
openssl rsa -in clef1 -pubout -out clef1.pub
```
```
diff clef.pub clef1.pub
```
```
cat test
```
```
openssl rsa -in clef -text  -noout
```
```
openssl rsautl -encrypt -pubin -inkey clef1.pub -in test -out test.enc
```
```
ls
```
```
openssl rsautl -decrypt -inkey clef1 -in test.enc
```
```
openssl rsautl -encrypt -pubin -inkey clef1.pub -in clef1 -out test3
```
Signing and verifying
```
openssl rsautl -sign -inkey clef1 -in test -out test.sign
```
```
cat test.sign
```
```
openssl rsautl -verify -pubin -inkey clef1.pub -in test.sign
```
Scellement operation
```
openssl digest -sha256 -sign clef1 -out test.hash.sign test 
```
```
openssl digest -sha256 -verify clef1.pub  -signature test.hash.sign 
```














