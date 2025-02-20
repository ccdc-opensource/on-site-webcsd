# On-Site Lattice and WebCSD

## Introduction

On-Site Lattice and WebCSD is currently only available to CCDC industrial customers and other invited groups.

This readme contains information required for installation and updates of On-Site Lattice and WebCSD.

Terms and conditions can be found in the `license.md` file.

Additional information including user configuration and troubleshooting can be found in the wiki at <https://github.com/ccdc-opensource/on-site-webcsd/wiki>.

## Prerequisites and Setup

### System Requirements

For version 4.0 onwards, a postgres server is required for CSD and Identity databases.
This will typically be installed on a separate machine from the one hosting the WebCSD
Docker containers.

Recommended requirements for postgres server without Macromolecule Hub (PDB protein data):

- Postgres version 14 or newer
- 100GB free hd space

Recommended requirements for postgres server including Macromolecule Hub:

- Postgres version 14 or newer
- 200GB free hd space

Recommended requirements for Docker server hosting On-Site Lattice and WebCSD containers:

- 30GB free hd space, 16GB RAM, 8 core CPU.

On-Site Lattice and WebCSD should work with any Linux OS that meets the requirements to run Docker, but official support is provided by CCDC on the following platforms.
Note that these match the 2024.2 Desktop release.

- RedHat Enterprise Linux 8 and 9
- Rocky Linux 8 and 9
- Ubuntu LTS 22 and 24

### Authentication

- A valid CCDC activation key will be required to use the software.
- Access to the CCDC docker container registry will require a username and password, to get them please contact CCDC Support.
- The postgres CSD database will be provided via a download link. To obtain this, please contact CCDC Support
  and specify whether you want the database with or without Macromolecule Hub.

### Docker Installation

A standard [Docker Server](https://docs.docker.com/engine/install/#server) and [Docker Compose](https://docs.docker.com/compose/install/) installation is required for installation of WebCSD.
We recommend installing the latest version of Docker from these links.
WebCSD will run on [Docker Desktop](https://docs.docker.com/engine/install/#desktop), however this is not recommended and may [require a license](https://www.docker.com/legal/docker-subscription-service-agreement/).

CHECK: Test your Docker installation as described at the above links. Also verify that you can log into the CCDC
container registry.

```sh
docker login -u <user> -p <password> ccdcrepository.azurecr.io

# or to be prompted for the password
docker login -u <user> --password-stdin ccdcrepository.azurecr.io
```

### PostgreSQL Server Installation

Once you have downloaded the postgres CSD database, please follow the instructions at
[Setting Up Your PostgreSQL Server](https://github.com/ccdc-opensource/on-site-webcsd/wiki/Setting-up-your-PostgreSQL-server) to restore
the database into your environment.

CHECK: Can you connect to the database from Docker containers? These commands should run without errors.

```sh
# Pull postgres image
docker pull postgres:14.13 
# Run container
docker run --name debug-postgres -e POSTGRES_PASSWORD=passwordhere -d postgres:14.13 
# Shell into container
docker exec -it debug-postgres sh 
# Attempt to establish a connection to the database server
psql -h database.server.name -p 5432 -U postgres -d csd_database
```

## Basic Configuration And Installation

### SSL Configuration

Connecting to your WebCSD server via https is now required.
To configure this you will need an SSL certificate and private key.
Ask your local IT staff to set these up for you.

Please follow the instructions at [Configuring SSL](https://github.com/ccdc-opensource/on-site-webcsd/wiki/Configuring-SSL).
If the WebCSD server is run without configuring SSL a built-in self-signed certificate will be used.
This is not recommended as it is insecure.

### WebCSD Basic Installation

Please download and unpack the latest version from the release tab <https://github.com/ccdc-opensource/on-site-webcsd/releases>.

After unpacking the release source code onto the server on which the software will be installed you will need to go into the on-site-webcsd directory and copy the environment file `sample.env` to `.env`.
Then open the `.env` file and populate with suitable values.

```console
cd on-site-webcsd
cp sample.env .env
```

You will need to update the `.env` file with your licence key, your database server details and
the URL you will use for your WebCSD server.
Here is an example of the `.env` file with comments removed:

```console
CCDC_LICENSING_CONFIGURATION=la-code;123456-123456-123456-123456-123456-123456;
DB_CONNECTIONSTRING=Server=database-server;Port=5432;User Id=postgres;Password=passwordhere
PUBLIC_URI=https://csd-software.local
PLATFORM_PORT=443
CSD_DATABASE=csd-database
IDENTITY_DATABASE=csd-identity
```

Where stated, some of these variables will be provided by CCDC; all other variables are for the user to generate and set.
Once the variables file has been populated, login to the CCDC container registry and bring up the stack:

```console
docker login -u <user> -p <password> ccdcrepository.azurecr.io

# or to be prompted for the password
docker login -u <user> --password-stdin ccdcrepository.azurecr.io

# As of v2.0.0, containers run as non root users.
# Because of this you will need to create the CCDC user if it does not already exist.
sudo adduser ccdc --uid=1397

# If you are upgrading from an older version to v4.0.0, ensure the user id is set to 1397
sudo usermod -u 1397 ccdc

# You will also need to ensure the user "ccdc" has read access to any in-house or CSP databases.
# E.g. if these are in the csd-data directory:
sudo chown -R ccdc:ccdc csd-data/ 

# For testing use one of the following commands.
# These do not load in-house databases and the user access control is disabled.
# These features will be configured later.

# If you are not using Macromolecule Hub
docker compose -f docker-compose.yml -f docker-compose.ssl.yml -f docker-compose.disable-user-access.yml up -d

# If you are using Macromolecule Hub
docker compose -f docker-compose.yml -f docker-compose.ssl.yml -f docker-compose.disable-user-access.yml -f docker-compose.macromolecule-hub.yml up -d
```

CHECK: To check that the install has completed and that all the services are running, run:

```sh
docker compose ps
```

which should show the state of the services to all be `Up`. If any services have the state `Up (unhealthy)` or `Exit` then restart the stack.

```sh
# Include all config files from the "docker compose ... up -d" run earlier
docker compose -f docker-compose.yml -f ... down
# Same command to bring up the stack as before
docker compose -f docker-compose.yml -f ... up -d
```

If the issues persist then please contact CCDC Support.

For more information see the [Docker volumes documentation](https://docs.docker.com/compose/compose-file/#volumes).

To access the On-Site Lattice and WebCSD service locally go to <https://full.server.hostname> in a browser,
replacing `full.server.hostname` by the full hostname of your Docker host. Check that you can search the CSD.
If you have problems check that `PUBLIC_URI` has been set to <https://full.server.hostname> in `.env`.

## Further Configuration

Your On-Site WebCSD server is now up and running but you will probably need to configure user access control
and in-house databases. This is done via docker compose files
(see [WebCSD Configuration Files](https://github.com/ccdc-opensource/on-site-webcsd/wiki/WebCSD-configuration-files) in the wiki).
After making any changes to these files, restart the stack as described above, adding any docker compose files
which you have customised into the `docker compose ... up -d` command.

### User Access Control

User access control can be managed via local accounts or Single Sign-On (SSO).
Unless it is explicitly disabled via `docker-compose.disable-user-access.yml`, you must configure roles to allow
access to in-house databases.

For details please see [Access Control To In-House Databases](https://github.com/ccdc-opensource/on-site-webcsd/wiki/Access-control-to-in-house-databases).

### In-house Database Configuration

:warning: **OnSite WebCSD assumes structure identifiers are unique across all registered in-house databases.
You will likely encounter unexpected behaviour if this is not the case.**

On-Site Lattice and WebCSD can be configured to read from in-house databases.

Provided within the installation is a sample database `teaching-subset.csdsql` which can be found in the `sample-data` folder in the root of the installation.

To enable in-house databases:

1. Copy and rename the file `docker-compose.sample-On-Site-only-db-config.yml` to `docker-compose.db-config.yml`
2. Edit the `volumes` section of that file to point to any in-house databases and edit the `environment` section to configure the application to recognise these databases.
Ensure that all instances of 'database-1' or 'database-2' are replaced with the actual database name.

For example, to use the 'teaching_subset.csdsql' database and have it appear as a database named 'Example', your docker-compose.db-config.yml should look like:

```yml
volumes:
    - /path/to/webcsd/sample-data/teaching_subset.csdsql:/csd-data/teaching_subset.csdsql
environment:
    - ServiceSettings__Databases__2__Name=Example
    - ServiceSettings__Databases__2__ConnectionString=/csd-data/teaching_subset.csdsql
```

More information is given in the notes & example sections of the sample file. This acts as an [override file](https://docs.docker.com/compose/extends/) which you will have to include in the startup command.

### In-house Protein Database Creation and Configuration

If you have a Macromolecule Hub licence, you can also create and register in-house protein databases for use within OnSite WebCSD.

To create an In-house Protein Database, you will need the Python Utilities. These can be downloaded from the [CCDC Downloads page](https://www.ccdc.cam.ac.uk/support-and-resources/csdsdownloads/)
under `CSD Python API > Python CSD Python API Utilities`. If you do not see the CSD Python API listed in the available downloads, you may need to sign in.
The relevant script can be found under `ccdc > utitities > create_protein_database`.
You can run the script using the CSD Python API version 3.3.0 or later.

Once you have created your protein database, you can register it by adding to your `docker-compose.db-config.yml` file following the instructions above.
You must then mark the database as a protein database. Your database config should look something like this:

```yml
volumes:
    - /path/to/ExampleProteinDb.csdsqlx:/csd-data/ExampleProteinDb.csdsqlx
environment:
    - ServiceSettings__Databases__2__Name=Example Protein DB
    - ServiceSettings__Databases__2__ConnectionString=/csd-data/ExampleProteinDb.csdsqlx
    - ServiceSettings__Databases__2__Speciality__0=Protein
```

### CSD-Theory Web Database Configuration

Instructions on setting up CSD-Theory Web can be found in the [wiki](https://github.com/ccdc-opensource/on-site-webcsd/wiki/Setting-up-CSD%E2%80%90Theory-Web).

Provided within the installation is a sample CSD-Theory landscape database `CSPLandscape.csdsqlx` which can be found in the `sample-data` folder in the root of the installation,
along with a blank `CSPDatabase.db` CSD-Theory metadata database.

### Other customisations

For other optional customisations to your WebCSD server please see [WebCSD Configuration and Customisation](https://github.com/ccdc-opensource/on-site-webcsd/wiki/WebCSD-Configuration-&-Customisation).

## Updates

When you have been notified that there is an update available you can [download the newest release](https://github.com/ccdc-opensource/on-site-webcsd/releases) from the github repository.
Once you have downloaded the new release, ensure any customisations have been copied to the new release directory.
You may need to customise the new versions of the config files (particularly `.env`) rather than copying the old versions - check the website above for breaking changes.

You may also need to contact support for a new postgresql database download link - see the release notes.

Once the latest installation files have been obtained, to update the software, pull the latest images, and restart the stack.
The latest images can be pulled whilst the stack is running and changes will only come into effect upon restarting the stack.

```sh
docker compose pull

#Include all files you included when bringing up the stack
docker compose -f docker-compose.yml -f ... down
#Use one of the following commands: 

#Use this command if you have no in-house databases and don't want to use macromolecule hub
docker compose -f docker-compose.yml -f docker-compose.ssl.yml up -d

#Use this command if you have in-house databases and don't want to use macromolecule hub 
docker compose -f docker-compose.yml -f docker-compose.db-config.yml -f docker-compose.ssl.yml up -d

#Use this command if you have in-house databases and want macromolecule hub 
docker compose -f docker-compose.yml -f docker-compose.macromolecule-hub.yml -f docker-compose.db-config.yml -f docker-compose.ssl.yml up -d

#Use this command if you have no in-house databases and want macromolecule hub
docker compose -f docker-compose.yml -f docker-compose.macromolecule-hub.yml -f docker-compose.ssl.yml up -d
```

To update the data if needed, repeat the process described at [Setting Up Your PostgreSQL Server](https://github.com/ccdc-opensource/on-site-webcsd/wiki/Setting-up-your-PostgreSQL-server#restore-database).

## Contacting support

If you experience any difficulties with installing or using On-Site Lattice and WebCSD, please contact our support team at <support@ccdc.cam.ac.uk> who will be happy to assist you.
