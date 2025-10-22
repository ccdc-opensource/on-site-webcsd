# On-Site Lattice and WebCSD

## FIXME general notes

This is a draft - I am trying to make this file more user friendly and update it for the
new Qt installer but I will need input from On-Site WebCSD experts. All notes to be deleted
before publication will be clearly marked with FIXME. The label BUG indicates that I have
verified that the documentation describes current behaviour but it is clearly a bug.

BUG: The Cloud Installation folder has two readme files in its subfolders, both with broken links
to the wiki.

## Introduction

On-Site Lattice and WebCSD is currently only available to CCDC industrial customers and other invited groups.

This readme contains information required for installation and updates of On-Site Lattice and WebCSD.

Terms and conditions can be found in the `license.md` file.

Additional information including user configuration and troubleshooting can be found in the wiki at <https://github.com/ccdc-opensource/on-site-webcsd/wiki>.

## Prerequisites and Setup

### System Requirements

A docker server is required to run On-Site WebCSD. In the standard configuration the
postgres server for CSD and Identity databases will also run in a docker container but there
is an option to host this separately.

FIXME: to calculate the disk space requirements I added the old requirements for the postgres
server to those for the docker server without postgres, is that right?

Recommended requirements for docker server hosting postgres container with Macromolecule Hub (PDB protein data):

- 230GB free HD space, 16GB RAM, 8 core CPU.

Recommended requirements for docker server hosting postgres container without Macromolecule Hub:

- 130GB free HD space, 16GB RAM, 8 core CPU.

Recommended requirements for Docker server hosting On-Site Lattice and WebCSD containers only:

- 30GB free HD space, 16GB RAM, 8 core CPU.

If self-hosting the postgres server, see [Setting up your Postgresql server](https://github.com/ccdc-opensource/on-site-webcsd/wiki/Setting-up-your-PostgreSQL-server)
for system requirements.

FIXME: we're planning on supporting Windows as well as Linux, what about macOS?

FIXME: Eva to confirm supported platforms for 2025.3 release

On-Site Lattice and WebCSD should work with any OS that meets the requirements to run Docker, but official support is provided by CCDC on the following platforms.
Note that these match the 2025.3 CSD Portfolio Desktop release.

- Windows 10 and 11
- RedHat Enterprise Linux 8, 9 and 10
- Rocky Linux 8, 9 and 10
- Ubuntu LTS 22 and 24
- macOS 14, 15 and 26

### Authentication

- A valid CCDC activation key will be required to use the software.
- Access to the CCDC docker container registry will require a username and password, to get them please contact CCDC Support.
- The postgres CSD database will be provided via a download link. To obtain this, please contact CCDC Support
  and specify whether you want the database with or without Macromolecule Hub.

### Docker Installation

FIXME: Can we recommend Docker Desktop now?

A standard Docker Server and Docker Compose installation is required for installation of WebCSD.
If you are new to Docker we recommend installing the latest version of [Docker Desktop](https://docs.docker.com/get-started/introduction/get-docker-desktop/).
Note that this may [require a license](https://www.docker.com/legal/docker-subscription-service-agreement/)
for commercial use in larger enterprises.

Linux users also have the option to install [Docker Engine](https://docs.docker.com/engine/install/#server) and the
[Docker Compose plugin](https://docs.docker.com/compose/install/).

CHECK: Test your Docker installation as described at the above links. Also verify that you can log into the CCDC
container registry.

```sh
docker login -u <user> -p <password> ccdcrepository.azurecr.io

# or to be prompted for the password
docker login -u <user> --password-stdin ccdcrepository.azurecr.io
```

FIXME: FYI I have taken out the "PostgreSQL Server Installation" section as this server will normally
run in a container.

## Basic Configuration And Installation

### SSL Configuration

Connecting to your WebCSD server via https is now required.
To configure this you will need an SSL certificate and private key.
Ask your local IT staff to set these up for you. The installer will ask for your SSL certificate
directory and set up SSL in the docker containers automatically.

If the WebCSD server is run without configuring SSL a built-in self-signed certificate will be used.
This is not recommended as it is insecure.

### WebCSD Basic Installation

FIXME: Most of this section was deleted as the installer hopefully handles it all now, I will comment
on specific points

FIXME: Is the below still necessary?

Containers run as the `ccdc` user and you will need to create this account and set its numeric
uid to 1397. On Linux or Mac these are the commands. FIXME: What about Windows?

```console
# As of v2.0.0, containers run as non root users.
# Because of this you will need to create the CCDC user if it does not already exist.
sudo adduser ccdc --uid=1397

# If you are upgrading from an older version to v4.0.0, ensure the user id is set to 1397
sudo usermod -u 1397 ccdc

# You will also need to ensure the user "ccdc" has read access to any in-house or CSP databases.
# E.g. if these are in the csd-data directory:
sudo chown -R ccdc:ccdc csd-data/ 
```

Please download the latest installer from LINK HERE and ask CCDC support for a download link for the
postgres CSD database.

If self-hosting the postgres server please follow the instructions at [Setting up your Postgresql server](https://github.com/ccdc-opensource/on-site-webcsd/wiki/Setting-up-your-PostgreSQL-server)

Ensure that your Docker installation is up and running, then run the WebCSD installer and follow its instructions.

![Onsite configuration dialog](onsite-webcsd-media/onsite%20configuration.png)

- SSL certificates: The directory containing your SSL certificate and private key
- Licence key: BUG: `la-code;ACTIVATION_KEY;` replacing `ACTIVATION_KEY` by your 36-character node-locked key for CCDC desktop software
- Public URI: The URL where the On-Site WebCSD server will be available (BUG: without the port number). This must resolve to your Docker host
- Platform port: The port for connections to the On-Site WebCSD server (default is 443)

![Database configuration dialog](onsite-webcsd-media/database%20configuration.png)

Browse to the database dump file which CCDC Support sent you.

During the installation a `.env` file and some `.yml` files will be created in the installation directory.
These are docker compose config files and can be edited to customise your installation.

It is also possible to run the WebCSD installer from the command line. This process is documented on the
wiki under [Alternative ways to setup On-Site WebCSD](https://github.com/ccdc-opensource/on-site-webcsd/wiki/Alternative-ways-to-setup-On-Site-WebCSD)

CHECK: To check that the install has completed and that all the services are running,
either view the containers in the Docker Desktop GUI or run:

```sh
docker compose ps
```

which should show the state of the services to all be `Up`. If any services have the state `Up (unhealthy)` or `Exit` then restart the stack.
Take care to include the right config files as in the commands below.

If you are not using Macromolecule Hub:

```sh
docker compose -f docker-compose.yml -f docker-compose.ssl.yml down
docker compose -f docker-compose.yml -f docker-compose.ssl.yml up -d
```

If you are using Macromolecule Hub:

```sh
docker compose -f docker-compose.yml -f docker-compose.ssl.yml -f docker-compose.macromolecule-hub.yml down
docker compose -f docker-compose.yml -f docker-compose.ssl.yml -f docker-compose.macromolecule-hub.yml up -d
```

If the issues persist then please contact CCDC Support.

FIXME: The below link redirects to a Compose Files Reference page and I am unsure what it was meant
to inform the customer about.

For more information see the [Docker volumes documentation](https://docs.docker.com/compose/compose-file/#volumes).

To access the On-Site Lattice and WebCSD service locally go to the WebCSD server URL you entered
at install time.

To check that you can search the CSD you must either create an account (BUG: this does not work) or
restart the stack with the user access control disabled. For more information please see
[Access Control To In-House Databases](https://github.com/ccdc-opensource/on-site-webcsd/wiki/Access-control-to-in-house-databases).

If you have problems check your `.env` file.

- The correct format for the `CCDC_LICENSING_CONFIGURATION` line is
  `CCDC_LICENSING_CONFIGURATION=la-code;ACTIVATION_KEY;`.
- `PUBLIC_URI` should be set to the full URL of your WebCSD server including the port number.
  This must resolve to your docker host.
- `PLATFORM_PORT` must match the port number in `PUBLIC_URI` (default is 443).

If you have problems check that `PUBLIC_URI` has been set to the correct URL in `.env` and that this
URL points to your Docker host.

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

FIXME: can we take out this warning now?

:warning: **OnSite WebCSD assumes structure identifiers are unique across all registered in-house databases.
You will likely encounter unexpected behaviour if this is not the case.**

On-Site Lattice and WebCSD can be configured to read from in-house databases. These must have the latest schema to work correctly. To convert an
in-house database to the correct format, use babel which is installed as part of CSD-Editor. For example, on Windows:

```sh
CCDC\ccdc-software\csd-editor\ccdc_babel.exe -auto path\to\old_db.csdsqlx -csdsql path\to\new_db.csdsql
```

FIXME: the `sample-data` folder is no longer provided with the installation, is this intentional?

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

To create an In-house Protein Database, you will need the CSD Python API Utilities. These can be downloaded from the [CCDC Downloads page](https://www.ccdc.cam.ac.uk/support-and-resources/csdsdownloads/).
If you are a CCDC Research Partner and you are logged into the CCDC website, the above link will take you to
a list of downloads. You'll find the CSD Python API utilities under `CSD Portfolio > CSD Python API`.
Otherwise, the link will take you to a download form requesting your customer number and activation key.
On completion of this form you will be sent a list of download links including the CSD Python API Utilities.

After unpacking the Python utilities, the relevant script can be found under `ccdc > utilities > create_protein_database`.
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

FIXME: as above, `sample-data` isn't included with the installation.

Provided within the installation is a sample CSD-Theory landscape database `CSPLandscape.csdsqlx` which can be found in the `sample-data` folder in the root of the installation,
along with a blank `CSPDatabase.db` CSD-Theory metadata database.

### Other customisations

For other optional customisations to your WebCSD server please see [WebCSD Configuration and Customisation](https://github.com/ccdc-opensource/on-site-webcsd/wiki/WebCSD-Configuration-&-Customisation).

## Updates

FIXME: Verify with an expert, and what about updating the postgres CSD database?

To update your installation, run the maintenance tool and select "Update Components" on the first page.
This will automatically pull the latest versions of all containers and restart the stack.

## Contacting support

If you experience any difficulties with installing or using On-Site Lattice and WebCSD, please contact our support team at <support@ccdc.cam.ac.uk> who will be happy to assist you.
