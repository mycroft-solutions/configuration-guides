# Backend Server

The backend server create connection between scanner application and front end application

## Prerequisites

To set up the backend server, you will need Mysql server and Java temurin 17

##  Download and Installation of Mysql

MySQL is an open-source database management system, commonly installed as part of the popular LAMP (Linux, Apache, MySQL, PHP/Python/Perl) stack. It uses a relational database and SQL (Structured Query Language) to manage its data.

### Prerequisites

  To follow this tutorial, you will need:

  * On Debian server set up by following this initial server setup guide
    [Debain Guide](https://www.cloudbooklet.com/how-to-install-mysql-on-debian-11/)

  * On Ubuntu  server set up by following this initial server setup guide, including a non-root user with sudo privileges and a firewall.    

### Step 1 — Installing MySQL  

  On Ubuntu , only the latest version of MySQL is included in the APT package repository by default

  To install it, update the package index on your server with apt:

  ```
  sudo apt update
  ```

  Then install the default package:

   ```
  sudo apt install mysql-server
  ```

  Ensure that the server is running using the systemctl start command:

  ```
  sudo systemctl start mysql.service

  ```

  These commands will install and start MySQL, but will not prompt you to set a password or make any other configuration changes.
  Because this leaves your installation of MySQL insecure, we will address this next.

### Step 2 — Configuring MySQL


  For fresh installations, you’ll want to run the included security script. This changes some of the less secure default options for things like remote root logins and sample users. On older versions of MySQL, you needed to initialize the data directory manually as well, but this is done automatically now.

  Run the security script:

  ```
  sudo mysql_secure_installation

  ```
  This will take you through a series of prompts where you can make some changes to your MySQL installation’s security options. The first prompt will ask whether you’d like to set up the Validate Password Plugin, which can be used to test the strength of your MySQL password. Regardless of your choice, the next prompt will be to set a password for the MySQL root user. Enter and then confirm a secure password of your choice.

  From there, you can press Y and then ENTER to accept the defaults for all the subsequent questions. This will remove some anonymous users and the test database, disable remote root logins, and load these new rules so that MySQL immediately respects the changes you have made.


  Run mysql service:

  ```
  sudo systemctl enable --now mysql.service

  ```

  Check status of mysql service : 
   ```

  sudo  systemctl status mysql.service
  ```


  Allow mysql from firewall: 

  ```
  sudo ufw allow mysql
  ```


  Run this command to login mysql server

  ```
  mysql -u root -p
  ```

### Conclusion


  You now have a basic MySQL setup installed on your server


##  Download and Installation of Java Temurin 17

Java™ is the world's leading programming language and platform. The Adoptium Working Group promotes and supports high-quality, TCK certified runtimes and associated technology for use across the Java ecosystem. Eclipse Temurin is the name of the OpenJDK distribution from Adoptium.


### Download and Installation Steps
  To follow this tutorial, you will need:
  * Go to [Java Installation Guide 1](https://blog.adoptium.net/2021/12/eclipse-temurin-linux-installers-available/)  or [Java Installation Guide 2](https://adoptium.net/installation/linux/)  
  * Find your linux operating system
  * Follow given steps


## Create Mysql Schema for  Keycloak

   Firstly,You need login to mysql 
   
   Run this command to login mysql server

   ```
    mysql -u root -p

   ```
### Step 1 — Create Keyclaok schema

Run this command to create keycloak schema:

```
CREATE SCHEMA keycloak DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

```

Run this command to select keycloak schema:

```
use  keycloak;

```

Get Keycloak sql  script from given zip file 


Run this command to create schema table:

 Replace  variables marked with diamond brackets (<>) to your file path

```
 source <filePath>/keycloak.sql;

```

Run this command to check keycloak schema table is created

```
SHOW TABLES;
```

### Step 2 — Create schema for Backend


Run this command to create mycroftSolution schema:

```
CREATE SCHEMA mycroftSolution DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

```

Run this command to select mycroftSolution schema:

```
use  mycroftSolution;

```

Get mycroftSolution sql  script from given zip folder 


Run this command to create schema table:

 Replace  variables marked with diamond brackets (<>) to your file path

```
 source <filePath>/mycrofotSolution.sql;

```


Run this command to check mycroftSolution schema table is created

```
SHOW TABLES;
```

 ## Configure and Run Keycloak
 The purpose of this guide is to walk through the steps that need to be completed  the Keycloak server configuration  for your local server

### Step 1— Download Keylcoak

 Get  Keyclaok application from given zip file [Keyclaok Application](https://www.example.com)


### Step 2  -Unpacking 
*  Unpack folder
*  Go to "conf" folder
*  Open  keycloak.conf file 


### Step 3- Adding initial configuration

* Add database vender

```
Example: db==mysql
```

* Add the username of the database.

```
Example: db-username==root

```

* Add the password of the database.

```
Example: db-password==12345678
```

* Edit the full database JDBC URL. If not provided, a default URL is set based on the selected database vendor.Replace  variables marked with diamond brackets (<>) to running port of mysql server
```
Example: db-url=jdbc:mysql://localhost:3306/keycloak?reconnect=true&useUnicode=true&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC&createDatabaseIfNotExist=true
```

* Add port number for the Keycloak server.

```
Example: http-port = 8081

```

* Add host name for the Keycloak server.

```
Example: hostname=localhost

```
### STEP 4 - Run Keycloak
Keycloak can be started in two operating modes, development mode and production mode. Both modes offer a different set of defaults for the environment they are intended to be used.
* Development Mode

* Production Mode

#### Step 4.1 Development Mode
 The development mode is targeted for people trying out Keycloak the first time and get it up and running quickly. It also offers convenient defaults for developers, for example to develop a new Keycloak theme.

```
sh bin/kc.sh start-dev & disown
```
Defaults

The development mode sets the following default configuration:

HTTP is enabled

Strict hostname resolution is disabled

Cache is set to local (No distributed cache mechanism used for high availability)

Theme- and Template-caching is disabled

### Step 4.2 Production Mode
The production mode is targeted for deployments of Keycloak into production environments and follows a "secure by default" principle.

#### Create Your Own SSL Certificate Authority for Local HTTPS Development
* Go to [Certificate Create Guide](https://github.com/mycroft-solutions/configuration-guides/blob/master/mycroft-web-server/README.md#csr)  

### Run Keyclaok in Production Mode


  Replace  variables marked with diamond brackets (<>) to your file path,file name , port number to run keycloak

```
sh bin/kc.sh  start --https-certificate-file=<Certificate_Path>/<certificate_file_name>.crt --https-certificate-key-file=<key_file_path>/<key_file_name>.key --https-port=<portNumber> & disown
```
### Step 5 -  Login 

* Open to any web-browser and Replace  variables marked with diamond brackets (<>) to define port number of keyclaok

```
http://localhost:<port-number>/

```
* Login Admin(Username:superuser,Password:superuser)
### Step 6-Account and Mail Configration

#### 1) Login Keycloak

#### 2) Click username at the right up corner and open Manage account 

#### 3) Add you admin account information and save

#### 4) Click "Back to security admin console" to go back admin console

#### 5) Click "Realm Setting" at the left corner

#### 6) Click  "Email Optioan and Add your own email configuration

#### 7) Change Realm at the right up corner to master(mycroft) and do 5,6 again

#### Example:

* `host`: smtp.office365.com for Microsoft or smtp.gmail.com for Gmail
* `port`: 587
* `From`: your company email
* `Enable SSL`: OFF
* `Enable StartTLS`: ON
* `Enable Authentication`: ON
* `Username`: ON
* `Password`: ON

**Note:**
If you use different email provider from outlook and gmail, add your provider host and port



### Step 7-Refresh Client Secret

* Click "Clients" at the left side
* Click "client-id"(For Master Realm) or "client_id"(For mycroft Realm) option on the page
* Go to "Credentials" Page and Regenerate new Secret
* Change Realm at the right up corner to master(mycroft) and generate new secret for master(mycroft) realm


## Configure  and Run Backend Application


### Step 1— Download Backend Application

Get  Backend Application from given zip folder


### Step 2  -Unpacking 
*  Unpack folder
*  Open Folder
*  Open  application-local.propeties file 

### Step 3- Adding local configuration

* Replace  variables marked with diamond brackets (<>) to "mycroft" realm secret
```
keycloak.workingClientSecret = <"local" realm secret>

```

* Replace  variables marked with diamond brackets (<>) to "master" realm secret

```
keycloak.clientSecret= = <"master" realm secret>

```
* Replace  variables marked with diamond brackets (<>) to keycloak server port

```
spring.security.oauth2.resourceserver.jwt.jwk-set-uri=http://localhost:<portNumber>/realms/mycroft/protocol/openid-connect/certs

```


* Replace  variables marked with diamond brackets (<>) to database server port


```
spring.datasource.url=jdbc:mysql://localhost:<portNumber>/mycroftSolution?reconnect=true&useSSL=true&useUnicode=true&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC&createDatabaseIfNotExist=false
```
 

* Replace  variables marked with diamond brackets (<>) to database username


```
spring.datasource.username=<username>

```

* Replace  variables marked with diamond brackets (<>) to database password


```
spring.datasource.password=<password>

```

* Replace  variables marked with diamond brackets (<>) to backend port number 

```
server.port=<server port>

```

* Replace  variables marked with diamond brackets (<>) to folder path to store log information

```
logging.file.name=<file path>

```

**Note:**
You should make sure that a certain Port is not occupied by any other process

### Step 4- Run Application

* Open terminal and  go to  folder where "mycroft solutions webapp-0.0.2-SNAPSHOT.war" file located

* Replace  variables marked with diamond brackets (<>) to "application.properties" file absolute path and Run application

```
java  -jar mycroftsolutionswebapp-0.0.2-SNAPSHOT.war  --spring.profiles.active=local --spring.config.location=<absolute path >/mycrfotSolutionBackEnd/ & disown
```

**Note:**
First Starting up will take some time 

