# Updates and uninstallation

## Software updates

### File ownerships

For updating from pre-4.3.0 versions, the `ccdc` account needs access to the necessary
docker volumes.

```sh
sudo chown -R ccdc:ccdc lic
sudo chown -R ccdc:ccdc userdata
```

### User management database

For updating from pre-4.0.0 versions, please see [Identity Gateway](identity-gateway.md) if you wish to keep
your user management database.

### Using the GUI maintenance tool

To update your installation, run the maintenance tool and select "Update components". Click Ok when a warning message pops up.
This will automatically pull the latest versions of all containers and restart the stack.

For major releases a new database dump file is shipped. Contact CCDC Support for the database download link. The maintenance tool will prompt for the location.
It will then overwrite the csd database volume with the update,
please export any in-house databases beforehand. Once the update has completed they will need to be reimported within lattice -> database management.

### Using the maintenance tool from the command line

The maintenance tool can be run non-interactively from the command line too.

```sh
# To upgrade (ensure in-house database have been exported and if using the installer setup postgres server that the database dump file has been replaced with the update in the same location originally specified - can be found in docker-compose.postgres.yml)
./maintenancetool update dbDumpFile="<path to database dump file>" overWriteDb="True"
```

### Manual Update With Docker

For major releases export any in-house databases, recreate the csd-database and [restore](self-hosted-postgresql-server.md)
it with a new database dump provided by Support.

```sh
# Replace `INSTALLDIR` by your WebCSD installation directory
# Include docker-compose.insecure-configuration.yml if using a self-signed certificate
cd INSTALLDIR
docker compose -f docker-compose.yml -f docker-compose.ssl.yml down
docker compose pull
docker compose -f docker-compose.yml -f docker-compose.ssl.yml up -d
```

Now from lattice -> database management, reimport your in-house databases.

## Updating the SSL certificate

SSL is required for your On-Site server, if it is not configured the server will use a built-in self-signed certificate which is insecure.
Normally, SSL will be configured as part of the installation process. To reconfigure it manually, you will need to do the following:

1. Obtain a .pfx certificate.

2. Update the SSL_CERT_LOC variable in your .env file to point at this file then restart the stack e.g.

```sh
SSL_CERT_LOC=/ccdc/on-site-webcsd/ssl_cert.pfx
```

## Updating licence keys

To change your licence key you must update licence information in the `.env` file,
delete the licence file with the current licence details, and restart the stack:

An example of the process:

- Bring down the server e.g.

```sh
# Replace `INSTALLDIR` by your WebCSD installation directory
# Include docker-compose.insecure-configuration.yml if using a self-signed certificate
cd INSTALLDIR
docker compose -f docker-compose.yml -f docker-compose.ssl.yml down
```

- Edit the `.env` file to enter the new license key e.g.

`vi .env`

Change the content after `CCDC_LICENSING_CONFIGURATION=` to include the new key
(press `i` to insert new text, `Esc` to leave editing mode and `:wq` to save edits)

- Remove the current licence information e.g.

`cd lic` followed by `rm data.conf` to remove the file

- Finally, bring up the server to use the new licence information

```sh
# Replace `INSTALLDIR` by your WebCSD installation directory
# Include docker-compose.insecure-configuration.yml if using a self-signed certificate
cd INSTALLDIR
docker compose -f docker-compose.yml -f docker-compose.ssl.yml up -d
```

To use a local licence server, the server URL should have the prefix `lf-server;`
in the `CCDC_LICENSING_CONFIGURATION=` field, as opposed to `la-code;` as used for a licence key.

## Uninstallation

To uninstall On-Site WebCSD run the GUI maintenance tool and select "Remove all components". Or run it from the command line with the `purge` option.

```sh
# To uninstall, this will give a Yes/No prompt to confirm the uninstall
./maintenancetool purge
```
