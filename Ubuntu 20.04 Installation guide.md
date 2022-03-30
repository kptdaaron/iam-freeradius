This document will serve as a guide on how to setup and configure a basic funtioning Freeradius with google secure LDAP as backend authenticator supporting multiple baseDN. 

### PREREQUISITE
- Google superadmin account
> An admin account with enough privilege is needed otherwise some functionalities will not be available e.g. adding/creating an application.

### PREPARING THE GOOGLE SIDE

- Login to https://admin.google.com
- Navigate to Apps > LDAP > Add Client
- Fill out client details
  ![image](https://user-images.githubusercontent.com/29798188/160749498-ff9c13ba-294f-4ef1-8eb6-0d8b2ede8fc0.png)
- Setup access permissions
  ![image](https://user-images.githubusercontent.com/29798188/160750734-af6b6f16-f86d-4019-9847-2ba9f7f81d7d.png)
  > In this section you can opt to use your entire domain or particular group/s within your organization. For the simplicity of this guide we will choose the entire       domain and allow access to all system attributes and group information.
- Once the client has been created, it will generate a certificate. Download and unzip the certificate (.crt and .key) which will be needed to connect freeradius with google later on.
  ![image](https://user-images.githubusercontent.com/29798188/160751354-9c228b82-bca7-4ee4-8cbb-62403170f73a.png)
- Next is to generate a new credentials (username and password) for the application. 
  ![image](https://user-images.githubusercontent.com/29798188/160752098-ef4956e6-e40e-4a08-b167-d03dd3f21019.png)
> ##### IMPORTANT: Copy and save the password as it will be presented to you only once, otherwise you will have to create a new credentials again.
- Now that the application is all set, you would have to turn the service status **ON**
  ![image](https://user-images.githubusercontent.com/29798188/160752414-b20e0d66-51ee-4697-b40c-8230c1fede34.png)


### PREPARING THE MACHINE
  ##### **INSTALLING FREERADIUS**
  1. On an existing Ubuntu 20.04 machine, download and install the necessary packages
     ```
     sudo su
     apt update && apt upgrade -y
     apt install freeradius freeradius-utils freeradius-ldap -y
     ```
  2. Secure copy the downloaded certificate to freeradius directory using SCP by issuing this command
     ```
     scp {<file1>, <file2>} <user>@<ip>:/etc/freeradius/3.0/certs
     ```
     ![image](https://user-images.githubusercontent.com/29798188/160761130-6f8b0a45-36b8-4d7d-a112-44b6aa2b5303.png)
   
     Rename the certificate and key file to ldap-client.crt and ldap-client.key respectively    
     ```
     mv /etc/freeradius/3.0/certs/<certificate>.crt /etc/freeradius/3.0/certs/ldap-client.crt
     mv /etc/freeradius/3.0/certs/<certificate>.key /etc/freeradius/3.0/certs/ldap-client.key
     ```
  ##### **CONFIGURING FREERADIUS**
  Here are the freeradius files that needs to be configured
  ```
  /etc/freeradius/3.0/mods-available/ldap
  /etc/freeradius/3.0/mods-available/eap
  /etc/freeradius/3.0/sites-available/default
  /etc/freeradius/3.0/sites-available/inner-tunnel
  /etc/freeradius/3.0/clients.conf
  /etc/freeradius/3.0/proxy.conf
  ```
     
  1. Configuring /mods-available/ldap
     ```
     cd /etc/freeradius/3.0/
     vim mods-available/ldap
     ```
     In the ldap section, modify these lines: 20, 29, 30, and 33 _**(line numbers may change depending on the content of the file)**_
     ```
     server = 'ldaps://ldap.google.com:636'
     identity = '<username>'
     password = '<password>'
     base_dn = 'dc=<domain>,dc=<top-level-domain>' e.g. dc=paradox,dc=edu,dc=ph
     ```
     ![image](https://user-images.githubusercontent.com/29798188/160765574-87156d69-3649-401e-89c9-ed36a4a81e24.png)
     
     In the tls section, modify these lines: 555, 560, 561, and 575 _**(line numbers may change depending on the content of the file)**_
     ```
     start_tls = no
     certificate_file = /etc/freeradius/3.0/certs/ldap-client.crt
     certificate_file = /etc/freeradius/3.0/certs/ldap-client.key
     require_cert = 'allow'
     ```
     ![image](https://user-images.githubusercontent.com/29798188/160769693-560d2b46-e8ef-435c-8469-5706b132b887.png)

  > **IF YOU HAVE MULTIPLE BASEDN, YOU WILL HAVE TO CREATE ANOTHER SECTION FOR IT:** Just simply copy the whole ldap section and repeat step number 1. 
  ``` 
  ldap domain1 { 
       ... 
  }
  ldap domain2 { 
       ... 
  } 
  ```
