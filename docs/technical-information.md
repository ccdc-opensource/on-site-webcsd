# Technical Information

## WebCSD configuration files

### Introduction

WebCSD configuration is handled via a number of `.yml` files which can be included as part of the `docker compose` command when bringing up the stack e.g.

`docker compose -f docker-compose.yml -f file1.yml -f file2.yml up -d`

`docker-compose.yml` is the main configuration file. We do not recommend editing it - instead put your changes in a separate "override file" and add it into the `docker compose` command. Example changes are listed at [WebCSD Configuration And Customisation](https://github.com/ccdc-opensource/on-site-webcsd/wiki/WebCSD-Configuration-&-Customisation).

Configuration files other than the main file may be renamed, particularly those with "sample" in the name. Which of these files you need to use depends on your WebCSD configuration. The installer should take care of including the correct files but the information below is helpful if you need to restart the stack.

### SSL

To secure connections via SSL which is now compulsory:

* Set up a pfx SSL certificate
* Ensure that `CERTIFICATE_PASSWORD` and `SSL_CERT_LOC` are set correctly in `.env` (the installer should take care of this)
* Include `docker-compose.ssl.yml` in the `docker compose` command
* If your certificate is self-signed, also include `docker-compose.insecure-configuration.yml`

### Databases

In version 4.3.0:

* Include `docker-compose.postgres.yml` if running the postgreSQL server in a docker container (the default configuration)

In version 4.2.1 and earlier:

* If using in-house databases without CSP, edit and include `docker-compose.sample-onsite-only-db-config.yml`
* If using in-house databases with CSP, edit and include `docker-compose.sample-csd-theory-db-config.yml`

### User access control

* For SSO (single sign-on), edit and include `docker-compose.sample-enable-sso.yml`.
* For LDAP, also edit and include `docker-compose.sample.ldap.yml`.

For earlier versions of the On-Site platform it was possible to disable all user access control. This ability has now been removed due to additional
functionality granted to users with administrator rights.

### Troubleshooting

CCDC support staff may ask you to include `docker-compose.debug.yml` to obtain more informative logs.