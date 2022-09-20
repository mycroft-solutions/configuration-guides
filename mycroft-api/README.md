# Mycroft API and Mycroft Auth installation guide

The backend server create connection between scanner application and front end application.

## Prerequisites

To set up the _Mycroft API_ and _Mycroft Auth_, you will need the following:

- MySQL database 6+
- Java Temurin 17
- Keycloak 18

## Installation Package

The **API Installation Package** is also needed.
After acquiring an archive containing necessary files, unpack it in a chosen location.
Let's mark the absolute path of this location as **<resource_dir>**.

The **<resource_dir>** should contain:

- `keycloak-18.0.0/`
- `mycroft-api-pre-release/`
- `keycloakSchema.sql`
- `mycroftSchema.sql`

You will be using these files and directories during the installation process.

# MySQL

If you don't have a MySQL database installed, you can find installation instructions for your Linux distribution in [MySQL docs](https://dev.mysql.com/doc/refman/8.0/en/linux-installation.html).

# Java Temurin 17

You can find installation instructions for your Linux distribution in [Adoptium installation guide](https://adoptium.net/installation/linux/) or this [Adoptium blog post](https://blog.adoptium.net/2021/12/eclipse-temurin-linux-installers-available/).

# Database configuration

## Keycloak schema

Before installing the _Mycroft Auth_ service, the database structures for _Keycloak_ dependency need to be created.

Start with authenticating to your database:

```sh
mysql -u root -p
```

To initiate the _Mycroft Auth_ database schema run the following command:

```sql
CREATE SCHEMA keycloak DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

From now on you will be working on the newly created schema.
Use the `keycloakSchema.sql` script from the _Installation Package_ to automatically build the required tables.

```sql
USE keycloak;

SOURCE <resource_dir>/keycloakSchema.sql;
```

To verify that the process was completed you can view the database structure and look for some of the _Keycloak_ tables e.g. `ADMIN_EVENT_ENTITY`, `USER_ENTITY` or `CLIENT`.

```sql
SHOW TABLES;
```

## Mycroft API schema

Run the following command to initiate the _Mycroft API_ database schema:

```sql
CREATE SCHEMA mycroft_solutions DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

From now on you will be working on the newly created schema.
Use the `mycroftSchema.sql` script from the _Installation Package_ to automatically build the required tables.

```sql
USE mycroft_solutions;

SOURCE <resource_dir>/mycroftSchema.sql;
```

To verify that the process was completed you can view the database structure and look for some of the _Mycroft API_ tables e.g. `REPORT`, `ORGANIZATION` or `USER`.

```sql
SHOW TABLES;
```

# Keycloak

## Keycloak configuration file

The Keycloak configuration file is located at `<resource_dir>/keycloak-18.0.0/conf/keycloak.conf`.

You will need to configure the following settings:

- `db-username` - username for the database
- `db-password` - user password for the database
- `db-url` - complete database JDBC URL - usually you will need to adjust only the port number
- `http-port` - port number for the Keycloak service to be exposed on
- `hostname` - hostname for the Keycloak service to be exposed on

### Example

Suppose the Keycloak server will be available at `http://123.456.78.90:8081`.
The database is exposed at port `3306`, the username is `root` and the pasword is `roottoo` (never do that).

The contents of the configuration file should look as follows:

```properties
# Basic settings for running in production. Change accordingly before deploying the server.

# Database

# The database vendor.
db=mysql

# The username of the database user.
db-username=root

# The password of the database user.
db-password=roottoo

# The full database JDBC URL. If not provided, a default URL is set based on the selected database vendor.
db-url=jdbc:mysql://localhost:3306/keycloak?reconnect=true&useUnicode=true&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC&createDatabaseIfNotExist=true

# Observability

# If the server should expose healthcheck endpoints.
health-enabled=true

# If the server should expose metrics endpoints.
metrics-enabled=true

# HTTP

# The file path to a server certificate or certificate chain in PEM format.
#https-certificate-file=${kc.home.dir}conf/localCer.cer

# The file path to a private key in PEM format.
#https-certificate-key-file=${kc.home.dir}conf/localhost.key

# The proxy address forwarding mode if the server is behind a reverse proxy.
#proxy=reencrypt

# Do not attach route to cookies and rely on the session affinity capabilities from reverse proxy
#spi-sticky-session-encoder-infinispan-should-attach-route=false

# Hostname for the Keycloak server.
hostname=123.456.78.90

#http-realative-path = /auth
http-port=8081
```

## Run Keycloak service

To run Keycloak use the following command:

```sh
sh <resource_dir>/keycloak-18.0.0/bin/kc.sh start-dev & disown
```

## Sign in to the Keycloak Admin Panel

To open the _Keycloak Admin Panel_ navigate to your Keycloak service using a web browser. For example, if your hostname is set to `123.456.78.90` and Keycloak http port is set to `8081`, the URL will be `http://123.456.78.90:8081`.

Authenticate using default credentials:

- `Username` - superuser
- `Password` - superuser

## Keycloak account configuration

1. Make sure the active _Realm_ (top left) is `Master`
2. Click on username in the top right corner and click on the **Manage account** option
3. Navigate to the **Personal info** section
4. Enter admin account information and save
5. Click on **Back to security admin console** to return to admin console
6. Click on **Realm Settings** in the top left corner
7. Click on **Email Options** and add your email configuration
8. Click on the _Realm_ name in the top left corner and change it to `Mycroft`
9. Click on **Realm Settings** in the top left corner
10. Click on **Email** and add your email configuration

### Example:

- `host`: `smtp.office365.com` (Microsoft) or `smtp.gmail.com` (Gmail)
- `port`: 587
- `From`: username@company.com
- `Enable SSL`: OFF
- `Enable StartTLS`: ON
- `Enable Authentication`: ON
- `Username`: ON
- `Password`: ON

**Note:**
If you use different email provider from outlook and gmail, add your provider host and port

## Refresh Client Secret

1. Make sure the active _Realm_ (top left) is `Mycroft`
2. Navigate to **Clients** on the left panel,
3. Click on **mycroftSolution_client_id** option on the page,
4. Go to **Credentials** and click on **Regenerate Secret**,
5. Change the _Realm_ to `Master` and navigate to **Clients** on the left panel,
6. Click on **client_id** option on the page,
7. Go to **Credentials** and click on **Regenerate Secret**.

# Backend Application

## Configuration file

The Backend configuration file is located at `<resource_dir>/mycroft-api-pre-release/application-local.properties`.

You will need to configure the following settings:

- `keycloak.serverUrl` - Keycloak service URL
- `keycloak.workingClientSecret` - client secret for `Mycroft` Realm _(Clients > mycroftSolution_client_id > Credentials)_
- `keycloak.clientSecret` - client secret for `Master` Realm _(Clients > client_id > Credentials)_
- `spring.security.oauth2.resourceserver.jwt.jwk-set-uri` - Keycloak authentication URL - usually you will need to adjust only the port number
- `spring.datasource.url` - database URL - usually you will need to adjust only the port number
- `spring.datasource.username` - username for the database
- `spring.datasource.password` - user password for the database
- `server.port` - port number for the Backend service to be exposed on
- `logging.file.name` - error log file location - useful for tech support
- `LOGIN_PAGE_URL` - redirection link to login page on the corresponding website
- `WEB_URL` - Website URL

### Example

An example configuration for Mycroft Backend server running at `http://123.456.78.90:8089` with a corresponding Mycroft Website available at `https://123.456.78.90` could look as follows:

```properties
#LocalServer

# Add your keycloak ip address
keycloak.serverUrl=http://123.456.78.90:8081

#Add your Working Realm
keycloak.workingRealm=mycroft

#Add your working realm client id
keycloak.workingClientId=mycroftSolution_client_id

# Add your wokring realm Client Secret
keycloak.workingClientSecret=6261cf8a-8984-462e-af99-0bf611270431

# Add your master realm clientid
keycloak.clientId=client_id

#Add you master realm clientSecert
keycloak.clientSecret=42ce9d23-797b-4c1b-9c32-6a2753b576b6

# Add you your master realm name
keycloak.realm=master

#Replace portNumber  wiht your keyclaok portnumber
spring.security.oauth2.resourceserver.jwt.jwk-set-uri=http://123.456.78.90:8081/realms/mycroft/protocol/openid-connect/certs

#Replace port number with your database  portnumber
spring.datasource.url=jdbc:mysql://123.456.78.90:3306/mycroft_solutions?reconnect=true&useSSL=true&useUnicode=true&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC&createDatabaseIfNotExist=false

organization.UserPrivileges=4

spring.datasource.driverClassName=com.mysql.jdbc.Driver

# Add your database username
spring.datasource.username=root

# Add your mysql password
spring.datasource.password=roottoo

# Define your backend port number  to run application
server.port=8089

# Define Folder to store log information
logging.file.name=/var/log/mycroftsolutions.log

# Define your backend running Address
WEB_URL=https://123.456.78.90

# Define your backend running Address
link.forgotPassword=http://localhost:8089

# Define your front end login page address
LOGIN_PAGE_URL=https://123.456.78.90/login

ORGANIZATION_TYPE=SINGLE
```

## Running the backend

You can run the application using the following command:

```sh
java -jar <resource_dir>/mycroft-api-pre-release/mycroftsolutionswebapp-0.0.2-SNAPSHOT.war --spring.profiles.active=local --spring.config.location=<resource_dir>/mycroft-api-pre-release/application.properties
```

**Note:**
First Starting up will take some time
