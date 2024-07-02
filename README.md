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
openssl dgst -sha256 -sign clef1 -out test.hash.sign test 
```
```
openssl dgst -sha256 -verify clef1.pub  -signature test.hash.sign test
```
* Video5 : Création de notre propre PKI (create  auto sign certificate)
Double terminal one by the CA and another one by the server
At CA : 
```
mkdir openssl_cert
```
```
cd openssl_cert
```
```
openssl genrsa  -out autorite.key  -des3 4092
```
```
ls
```
```
cat autorite.ke
```
Let's create a certificate
```
openssl req -new x509 -days 3650  -key autorite.key certif.aut
```
eg
[AU] : TN </br>
[some-state] : TUNISIE  </br>
(city) : Centre urbain  </br>
(company) : ORG_autority  </br>
(section) : technology  </br>
[FQDN] : www.autorite.tn  </br>
email : autorite@gmail.com  </br>
```
cat certif.aut
```
```
openssl x509 -in certif.aut -text -noout
```
```
openssl x509 -in certif.aut -subject -issuer -dates -noout
```
AT SERVER : </br>
Another linux for serveur </br>
Generate private key for the server
```
openssl genrsa -out serveur.key -des3 2048
```
Demandig certificate without x509
```
openssl req -new   -key autorite.key serveur.key -out demande.serv
```
eg
[AU] : TN </br>
[some-state] : TUNISIE  </br>
(city) : Manar </br>
(company) : ORG_server  </br>
(section) : tech  </br>
[FQDN] : www.serveur.tn </br>
email : serveur@gmail.com  </br>
```
Avoid challange password
```
cat serveur.key
```
Not a certificate, verify it's can't be load there
```
openssl x509 -in serveur.serv -text -noout
```
make a pwd on terminal of autority certificate CA 
```
```
scp demande.serv kali@<IP of CA>:<path of CA>
```
if connection refused restart, at the CA
```
sysemctl restart ssh
```
At CA : let's sign the demand
```
openssl x509 -req -in demande.serv -out certif.serv -CA certif.aut -CAkey autorite.key -CAcreateserial serial.ser
```
```
ls
```
```
cat certif.serv 
```
```
openssl verify -CAfile certif.aut certif.serv
```
AT SERVER get the path by pwd </br>
Sending certificate at server 
```
scp certif.* kali@<IP Addres of >:<path server>
```
if connection refuse , make as same
AT server , verify by ls
```
ls
```
```
openssl pkcs12 -export -out enveloppe_vers.pfx -in certif.serv -inkey serveur.key -name "le certif du serveur" 
```
```
ls
```
you could export now the certificate
Can't verify because we don't hava autority and it's not root certificate
* Video 6 : Chiffrement asymétrique d'un large fichier (too large to encrypt the file , key size)
```
mkdir openssl6
```
```
cd openssl6
```
```
man dd
```
```
dd if=/dev/zero of=LargeFile bs=1024 count=0 seek=$[300*2]
```
```
ll
```
or using fallocate
```
fallocate -l 600M LargeFile2
```
```
ll
```
```
rm -rf LargeFile
```
Method1 : Generate certificate
```
openssl req -x509 -nodes -days 100000 -newkey rsa:2048 -keyout Private.key -out cert
```
eg
[AU] : TN </br>
[some-state] : TUNISIE  </br>
(city) : Centre urbail  </br>
(company) : ORG_cert  </br>
(section) : techwall  </br>
[FQDN] : www.cert.tn  </br>
email : cert@gmail.com  </br>
```
ls
```
```
file Private.key
```
```
cat Private.key
```
```
file cert
```
```
cat cert
```
Extract or parse public by certificate
```
openssl x509 -in cert -pubkey -noout > Public.key 
```
```
ls Public.key
```
ERROR
```
openssl rsautl -encrypt -pubin -inkey Public.key -in LargeFile2 -out LargeFile2.enc
```
```
touch test
```
```
openssl rsautl -encrypt -pubin -inkey Public.key -in test -out test.enc
```
if file big than the lenght of key , not possible
NOT POSSIBLE
```
openssl rsautl -encrypt -pubin -inke cert -in LargeFile2 -out LargeFile2.enc
```
METHOD 1 : USING AES by smime
```
openssl smime -encrypt -aes256 -in LargeFile2 -binary -outform PEM -out LargeFile2.enc cert
```
```
cat LargeFile2.enc
```
```
openssl smime -decrypt -aes256 -in LargeFile2.enc -binary -inform PEM -inkey Private.key -out LargeFile3
```
```
diff LargeFile2 LargeFile3
```
METHODE 2 : USING HYBRID SOLUTION
```
openssl rand -base64 32 > KEY 
```
```
openssl enc -aes-256-cbc -salt -in LargeFile2 -out LargeFile2.enc2 -pass file:./KEY -iter 2 
```
```
openssl enc -aes-256-cbc -salt -in LargeFile2.enc2 -out LargeFile4 -pass file:./KEY -iter 2 -d 
```
```
diff LargeFile2 LargeFile4
```
* Video 7 : ECC
```
mkdir Openssl_EC
```
```
cd Openssl_EC
```
```
openssl ecparam -list_curves
```
```
openssl ecparam -genkey -name prime256v1 -out Private.key
```
```
cat Private.key
```
```
ll
```
Protect by key
```
openssl ec -in Private.key -des3 -out Private.key
```
seeying information 
```
openssl ec -in Private.key -text -noout
```
Calculate the key public by the private
```
openssl ec -in Private.key -pubout -out Public.key
```
```
ll
```
```
cat Public.key
```
```
openssl req -x509 -days 365 -key Private.key -out cert
```
eg
[AU] : TN </br>
[some-state] : TUNISIE  </br>
(city) : Centre urbain  </br>
(company) : ORG_cert  </br>
(section) : techwall  </br>
[FQDN] : www.cert.tn  </br>
email : cert@gmail.com  </br>
```
cat cert
```
```
openssl x509 -in cert -text -noout
```
```
openssl x509 -in cert -subject -issuer -dates -fingerprint -noout
```
```
echo "bonjour" > test
```
Scellement
```
openssl dgst -sha256 -out test.sign -sign Private.key test
```
```
file test.sign
```
```
cat test.sign
```
all entities having public key could verify 
```
openssl dgst -sha256 -verify Public.key -signature test.sign test
```
* Video 8 : ECDH
AT SERVER
```
openssl ecparam -name secp256k1 -genkey -out Private_serv.key
```
```
openssl ec -in Private_serv.key -pubout -out Public_serv.key 
```
AT CLIENT
```
openssl ecparam -name secp256k1 -genkey -out Private_client.key
```
```
openssl ec -in Private_client.key -pubout -out Public_client.key 
```
AT SERVER
OTHER METHODE
```
python3 -m http.server 9000
```
```
hostname -I
```
AT CLIENT 
```
wget root@<IP SERVER>:9000/Public_serv.key
```
```
ls
```
Doying the same at the client
AT CLIENT 
```
python3 -m http.server 8000
```
```
hostname -I
```
AT SERVER
```
wget root@<IP CLIENT>:8000/Public_client.key
```
```
ls
```
DERIVATION KEY AT  SERVER
```
openssl pkeyutil -derive -inkey Private_serv.key -peerkey Public_client.key -out Serv_client.key
```
```
base64 Serv_client.key
```
DERIVATION KEY AT  CLIENT
```
openssl pkeyutil -derive -inkey Private_client.key -peerkey Public_serv.key -out Client_serv.key
```
```
base64  Client_serv.key
```
AT CLIENT
```
echo "test test" > test
```
```
openssl enc -aes256 -k $(base64 Client_serv.key) -e -in test -out test.enc -iter 2
```
```
cat test.enc
```
Send by scp
```
scp test.enc root@<IP ADDR>:/<path of server>
```
AT SERVER 
```
openssl enc -aes256 -k $(base64 Serv_client.key) -d -in test.enc -out test -iter 2
```
* Video 9, 10, 11 : Secure HTTPS
```
apt update
```
```
apt install apache2
```
configure host
```
getent hosts
```
At broswer : 127.0.0.1
```
sudo nano var/www/html/index.html
```
Personalized data
```
sudo nano /etc/hosts/
```
change 127.0.0.1  localhost </br>
by  127.0.0.1  localhost www.monsite.tn </br>
For HTTP at : 
```
sudo nano /etc/apache2/sites-available/000-default.conf
```
For HTTPS at : 
```
sudo nano /etc/apache2/sites-available/default-ssl.conf
```
**************************************************
AT CA
```
openssl genrsa -des3 -out Autorite.key 2048
```
```
openssl req -new -x509 -dayls 3650 -key Autorite.key -out Autorite.crt
```
[AU] : TN </br>
[some-state] : TUNISIE  </br>
(city) : Centre urbain  </br>
(company) : ORG_cert  </br>
(section) : Section_Autorite </br>
[FQDN] : www.autorite.tn  </br>
email : autorite@gmail.com  </br>

AT SERVER
```
openssl -des3 -out Serveur.key 2048
```
ls
```
Create a demand of certificate (no days and no x509)
```
openssl req -new -key Serveur.key -out Serveur.demande
```
```
[AU] : TN </br>
[some-state] : TUNISIE  </br>
(city) : Centre urbain  </br>
(company) : ORG_serveur  </br>
(section) : Section_Aserveure </br>
[FQDN] : www.monsite.tn  </br>
email : monsite@gmail.com  </br>
</br>
Copy at CA
```
scp Serveur.demande root@<IP of CA>:<Path of CA>
```
if connection refuse redo at CA systemctl restart ssh
AT CA : 
```
openssl x509 -req -in Serveur.demande -out Serveur.cert -CA Aoutorite.cert -CAkey Autorite.key -CAcreateserial Serveur.cert
```
```
openssl x509 -in Serveur.cert -subject -issuer -fingerprint -dates -noout
```
```
scp Autorite.cert root@<IP of server):<Path of server>
```
AT SERVER
```
ls
```
Translate to PKCS12
```
openssl pkcs12 -export -in Serveur.cert -inkey Serveur.key -out Serveur.pfx -name " Le certificat du serveur"
```









