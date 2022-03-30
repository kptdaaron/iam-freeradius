This document will serve as a guide on how to setup and configure a basic funtioning Freeradius with google secure LDAP as backend authenticator. 

#### PREREQUISITE
- Google superadmin account
> An admin account with enough privilege is needed otherwise some functionalities will not be available e.g. adding/creating an application.

#### PREPARING THE GOOGLE SIDE
- Login to https://admin.google.com
- Navigate to Apps > LDAP > Add Client
- Fill out client details
![image](https://user-images.githubusercontent.com/29798188/160749498-ff9c13ba-294f-4ef1-8eb6-0d8b2ede8fc0.png)
- Setup access permissions
  ![image](https://user-images.githubusercontent.com/29798188/160750734-af6b6f16-f86d-4019-9847-2ba9f7f81d7d.png)
  > In this section you can opt to use your entire domain or particular group/s within your organization. For the simplicity of this guide we will choose the entire       domain and allow access to all system attributes and group information.
- Once the client has been created, it will generate a certificate. Download the certificate which will be needed to connect freeradius with google later on.
![image](https://user-images.githubusercontent.com/29798188/160751354-9c228b82-bca7-4ee4-8cbb-62403170f73a.png)
- Next is to generate a new credentials (username and password) for the application. 
![image](https://user-images.githubusercontent.com/29798188/160752098-ef4956e6-e40e-4a08-b167-d03dd3f21019.png)
> ##### IMPORTANT: Copy and save the password as it will be presented to you only once, otherwise you will have to create a new credentials again.
- Now that your application is all set, you would have to turn ON the service status
![image](https://user-images.githubusercontent.com/29798188/160752414-b20e0d66-51ee-4697-b40c-8230c1fede34.png)
