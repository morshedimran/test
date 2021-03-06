◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆
# Self-signed unit certificate creation procedure
◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆

## Introduction

This it the procedure manual to generate the Self-signed unit certificate. which are required for the ansible execution to build personium.

Followings will be created after Performing the procedure below by openssl

```
1.unit.key             ：This is the unit secret key. Created by RSA secret key of more than 2048bit in DER format. Managing this unit secret key strictly is highly recomended.
2.unit.csr             ：Request for X.509 certificate. As this file will be required to create the certificate, we are not deploying the file on the server.
3.unit-self-sign.crt   ：In the Unit Key supported DER format certificate, the value of CN should be the domain name. Though there are several option to generate the certificate. At this time we generated the certificate by ourselves and documented as well how to do the same.
```

Below is the procedure to generate the Self-signed unit certificate.

---------------------------------------
:sparkles: GETTING STARTED :sparkles:

### Part 1. Create personium.io unit secret key (unit.key) on Bastion server

1. Change directory to the certificate deployment directory of Bastion server.
```
    # cd /root/ansible/resource/ap/opt/x509/
```
2. Create secret key

```
    # openssl genrsa -out unit.key 2048 -outform DER
```  
Execution Ex:)
	-----------------------------------------------------------------------------------
```
    # openssl genrsa -out unit.key 2048 -outform DER
    Generating RSA private key, 2048 bit long modulus
    ............................................................+++
    ...................................................+++
    e is 65537 (0x10001)
    -----------------------------------------------------------------------------------
```
Confirm that the unit.key is created
```
    # ls -l
```
Execution Ex:)-----------------------------------------------------------------------------------
```
    # ls
    total 4
    -rw-r--r--. 1 root root 1675 Sep  1 20:27 unit.key
```	

### Part 2. Create self-signed unit certificate

1. Create the CSR
```
  # openssl req -new -key unit.key -out unit.csr
    → enter the required information interactively.
      ※　Common Name value should be the unit domain name (required)
```
Execution Ex:)-----------------------------------------------------------------------------------
```
    [root@VVC7W51NTM0001 test_self_crt]# openssl req -new -key unit.key -out unit.csr
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [XX]:
    State or Province Name (full name) []:
    Locality Name (eg, city) [Default City]:
    Organization Name (eg, company) [Default Company Ltd]:
    Organizational Unit Name (eg, section) []:
    Common Name (eg, your name or your server's hostname) []:example.com            ←★ Enter the unit domain name (required)
    Email Address []:
    Please enter the following 'extra' attributes
    to be sent with your certificate request
    A challenge password []:
    An optional company name []:

```
2. Self-sign the CSR. valid for 3650 days (approximately 10 years)

```
    # openssl x509 -req -days 3650 -signkey unit.key -out unit-self-sign.crt <unit.csr
```
Execution Ex:)---------------------------------------------------------------------------------
```
    # openssl x509 -req -days 3650 -signkey unit.key -out unit-self-sign.crt <unit.csr
    Signature ok
    subject=/C=XX/L=Default City/O=Default Company Ltd/CN=example.com
    Getting Private key

```

3. Check the contents of the generated certificate

```
    # openssl x509 -in unit-self-sign.crt -text
```  
  Also check with the following command
```
    # ls -l
```   
Execution Ex:)-----------------------------------------------------------------------------------
```
    # ls -l
    total 12
    -rw-r--r--. 1 root root 1041 Sep  1 20:29 unit.csr
    -rw-r--r--. 1 root root 1675 Sep  1 20:27 unit.key
    -rw-r--r--. 1 root root 1277 Sep  1 20:29 unit-self-sign.crt
```
### Summary

Though there are several option to generate the certificate. At present we generated the certificate by ourselves and documented as well how to do the same for better understanding.
