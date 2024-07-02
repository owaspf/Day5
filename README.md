# Day5 : Cryptography; Data Corruption; haching; Authentication
## Analyzing OpenSSL certificate
* Video 1 : 
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
openssl x509 -in certificat -text -subject -noout
```
See subject and issuer
```  
openssl x509 -in certificat -text -subject -issuer -noout
```
See subject, issuer and dates
```  
openssl x509 -in certificat -text -subject -issuer -dates -noout
```


