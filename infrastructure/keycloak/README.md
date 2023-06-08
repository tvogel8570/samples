# Docker configuration for local keycloak instance
This directory contains a docker compose (V3) file that will start keycloak without installing it locally.

This sample uses the **secrets** of Docker compose to install a self-signed certificate in the keycloak container at *runtime*, i.e. the certificate is not part of the image.  See docker compose [documentation](https://docs.docker.com/compose/compose-file/05-services/#secrets) for secrets for more information.

### !!! NOT FOR PRODUCTION USE !!!

## Outcome
An instance of keycloak running as a docker service (named `local-keycloak`) on port 8443 using a self-signed certificate for SSL.  The admin user is `admin` with a password of `admin1`

## Prerequisites
1. A recent version of docker that supports the new compose file format (V3)
2. A self-signed SSL certificate.  See [this gitHub repository](https://github.com/ch4mpy/self-signed-certificate-generation) for tutorial on creating one for your development environment.

## Changes you have to make locally
1. Create a subdirectory `./infrastructure/keycloak/SSL`
2. Add the following files to this directory
   1. The self-signed SSL certificate for your development environment re-named to `self_signed.crt`
   2. The private key associated with the certificate for your development environment re-named to `self_signed_key.pem`

The files in this directory (`./infrastructure/keycloak/SSL`) are needed to make keycloak work from docker compose.  However, they contain sensitive information that should never be committed to a repository.
Therefore, the project's `.gitignore` should contain the following line:
```
/path_to_your_project_files/infrastructure/keycloak/SSL
```

## Optional changes
- different SSL port by changing the ***published:*** value
```
    ports:
      - target: 8443 # Keycloak HTTPS port
        published: 8443
```
- change / remove the admin password.  See the [official keycloak documentation](https://www.keycloak.org/docs/latest/server_admin/#creating-the-account-on-the-local-host) for procedure to follow if admin password is not specified at start up.
```
    environment:
      - KEYCLOAK_ADMIN=admin
      - KEYCLOAK_ADMIN_PASSWORD=admin1
```
- change the location / name of the SSL files
```
secrets:
  server_crt:
    file: ./SSL/self_signed.crt
  server_key:
    file: ./SSL/self_signed_key.pem
```
- change the name used for the running compose environment
```
name: local-keycloak
```

## Starting keycloak
1. Open a terminal
2. Change directory to `samples/infrastructure/keycloack`
3. Enter the command `docker compose up --detach`
4. Open a browser and navigate to the [running keycloak instance](https://localhost:8443/admin)
5. Enter user `admin` with password `admin1`
6. Configure keycloak as needed for your application

## Stopping keycloak
1. Open a terminal (or use an already open one)
2. Enter the command `docker compose -p local-keycloak down`

  
## Errors you may encounter
- The keycloak container fails to start with the error `is a directory`.  This is usually a result of the certificate and / or key file(s) not being correctly named and in the proper directory.  Docker "helpfully" creates a folder for any missing secrets, hence the error from keycloak.
