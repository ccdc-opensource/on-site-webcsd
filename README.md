# On-Site WebCSD

On-Site WebCSD is currently only available to CCDC Research Partners and other invited groups.

This release is an Alpha release aimed at getting feedback on the installation and update process.

This alpha release does not contain all the functionality currently provided in On-Site WebCSD. All functionality will be provided in the full release.

## Prerequisites

A standard [Docker Server](https://docs.docker.com/engine/install/#server) and [docker-compose](https://docs.docker.com/compose/install/) installation is required for installation. This will run on [Docker Desktop](https://docs.docker.com/engine/install/#desktop), however this is not recommended and may [require a license](https://www.docker.com/legal/docker-subscription-service-agreement/).

Access to the CCDC container registry will require a username and password that will be provided by CCDC Support.

A valid license key will be required to use the software.

You will need either:

A download of the desired [release](https://github.com/ccdc-opensource/onsite-webcsd/releases).
Click on the release you want to use, and download the source code zip and unpack.

or

A clone of this repository (this will require a git installation).
git clone https://github.com/ccdc-opensource/onsite-webcsd.git

## Initial recommended specification

This is our initial estimate of what will be required to run WebCSD well.
170GB free hd space, 32GB ram, 8 core cpu, Linux OS

## Local Database Configuration

On-Site WebCSD can be configured to read from local databases. To enable these databases, copy and rename the file `docker-compose.sample-db-config.yml` to `docker-compose.db-config.yml` and edit the `volumes` section to point to any local databases and edit the `environment` section to configure the application to recognise these databases. More information is given in the notes & example sections of the sample file. This acts as an [override file](https://docs.docker.com/compose/extends/) which you will have to include in the startup command.

## Installation

After either unpacking the release source code onto the server on which the software will be installed or cloning the git repo and pulling it you will need to go into the onsite-webcsd directory and copy the environment file `sample.env` as `.env` then populate with suitable values.

```
cd onsite-webcsd
cp sample.env .env
```

You will need to update the .env file with your licence key and the two passwords you would like to use.
Here is an example of the .env file:

```
CCDC_LICENSING_CONFIGURATION='la-code;123456-123456-123456-123456-123456-123456;'
CSD_DB_PASSWORD='A password of your choosing'
CSD_CACHE_PASSWORD='A password of your choosing'
```

Where stated, some of these variables will be provided by CCDC; all other variables are for the user to generate and set. Once the variables file has been populated, login to the CCDC container registry and bring up the stack:

```
docker login -u <user> -p <password> ccdcrepository.azurecr.io

# or to be prompted for the password
docker login -u <user> --password-stdin ccdcrepository.azurecr.io

#Use one of the following two commands depending on if you have local database configuration
#Use this command if you have no local database configuration
docker-compose up -d

#Use this command if you have local database configuration
docker-compose -f docker-compose.yml -f docker-compose.db-config.yml up -d
```

### Customisation of the User Interface

This section details a number of customisations you can make to WebCSD. In order to keep these settings from being accidentally over-written, we recommend putting any customisations in a separate docker-compose file, e.g. docker-compose.customisations.yml. This acts as an [override file](https://docs.docker.com/compose/extends/) which you will have to include in the startup command. Alternatively you could add these settings into the aforementioned db-config file to keep all of your configuration in one place.

#### Adding your own company logo

Replace the standard "CCDC On-Site WebCSD" logo at the top right of the page, with your own company logo, by adding the following to the `webcsd` volumes section of your own docker-compose file.

```
- /path/to/logo/<CompanyLogo.png>:/app/wwwroot/images/OnSite-logo.png
```

As there is no height or width specified when displaying this image, please ensure the replacement is of a reasonable size, to integrate with the layout of the page. For example, the CCDC logo, at the top left of the page, is 142 x 50 pixels.

#### Changing the Identifier text

Change the label used for entry identifiers in the search results list, by adding the following settings to the `webcsd` enironment section of your own docker-compose file.

```
- Customisations__Identifier__Full=<Identifier text>
- Customisations__Identifier__Short=<Identifier text short version>
```

#### Changing the non-CSD entry title

Change the text displayed before the identifier in the main title, when viewing a non-CSD entry, by adding the following setting to the `webcsd` environment section of your own docker-compose file.

```
- Customisations__NonCsdEntryTitle=<In-house entry text>
```

#### Customised Header and Information pages

To add custom HTML above the search form on the home page, create a file containing a HTML snippet and add the following setting to the `webcsd` volumes section of your own docker-compose file.

```
- /path/to/<Custom_Header.html>:/app/wwwroot/static/HomeHeader.html
```

Clicking on `About This Service` in the footer or accessing `/access-structures-information` brings up the information page. To add custom HTML for this page, create a file containing a HTML snippet and add the following setting to the `webcsd` volumes section of your own docker-compose file.

```
- /path/to/<Custom_Information.html>:/app/wwwroot/static/Information.html
```

**PLEASE NOTE:** The content of these pages is rendered by the site as is, without encoding or validation. Any embedded scripts in HomeHeader.html or Information.html will run. It is the site administrator's responsibility to ensure that these pages are properly secured.

### Running WebCSD under SSL

As this is optional, we recommend following a similar process to that described in the database configuration and user interface customisation sections. Which is to store these settings in a separate docker-compose file. You can then include this file in your startup command.

To run WebCSD using HTTPS, you will need to do the following:

1. Obtain a certificate and store it in a folder that can be referenced in the volumes section (path/to/certificate).

2. Update the following sections of the `webcsd` service in your docker-compose file to add HTTPS support. An example of what to add and in which section, is shown below:

```
  webcsd:
    environment:
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=<password used to create certificate>
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/container/path/certificate.pfx

    ports:
      - 443:443

    volumes:
      - /path/to/certificate:/container/path:ro   # with read-only attributes (:ro)
```

### Offline Installation

This release will be only available online, if this is a problem please contact us. In the future we plan on supporting offline installs.

## Updates

When you have been notified when there is an update available you can either

Checkout the tag for the release.

```
git checkout tags/<specific release tag>
cd <on-site installation directory>
git pull
```

Or you can download the newest release from the github repository
If the installation files have been downloaded from the release page you will need to download the latest version again from there and ensure the default old files have been removed and any custom configuration files have been moved to the new release directory.

Once the latest installation files have been obtained, to update the software, pull the latest images, and restart the stack. The latest images can be pulled whilst the stack is running and changes will only come into effect upon restarting the stack.

```sh
docker-compose pull

docker-compose down
#Use one of the following two commands depending on if you have local database configuration
#Use this command if you have no local database configuration
docker-compose up -d

#Use this command if you have local database configuration
docker-compose -f docker-compose.yml -f docker-compose.db-config.yml up -d
```

## Verifying the Installation/Update

To check that the install or update has completed and that all the services are running, run:

```sh
docker-compose ps
```

which should show the state of the services to all be `Up`. If any services have the state `Up (unhealthy)` or `Exit` then restart the stack. If the issues persist then please contact CCDC Support.

During the Alpha release some of these services will be unhealthy - this is expected and will be fixed before the final release.
The services which will be unhealthy are:

```
onsite-webcsd_ccdc-csd-searchservice_1
onsite-webcsd_ccdc-csd-resultstore_1
```

For more information see the [Docker volumes documentation](https://docs.docker.com/compose/compose-file/#volumes).

## Rollback in case of Update failure

If an update fails and you need to revert to the previous working version, follow the instructions in the [Updates](#updates) section with the `<specific release tag>` set to the previous version.

## Data Updates

To update an existing database, first check the location of the database that you want to update. This will be specified in the `webcsdbackend` volumes section of your docker-compose or local database configuration file. Copy the new version of the database to the required location, then do the following:

```sh
docker-compose down

#Use one of the following two commands depending on if you have local database configuration
#Use this command if you have no local database configuration
docker-compose up -d

#Use this command if you have local database configuration
docker-compose -f docker-compose.yml -f docker-compose.db-config.yml up -d
```

## Associated Structure Links Configuration

On-Site WebCSD can be configured to display links associated with a structure, e.g DOI links or File links. To do this you will need a CSV file containing structures with their associated data and any files you may want to download. The CSV file belongs in `webcsdbackend` and is configured in the `environment` section. Any related files belong to `webcsd` where the location is configured in the `volumes` section. It is important to note that any file links set up in the CSV file, must point to the correct location of these files, otherwise the file will not download when the link is clicked.

## Storing the docker images in your local repository

If you want to store the docker images in your own repository follow the below steps:

1. Get a copy of this git repository as in the Prerequisites section above.

2. Run the below command to download the docker images

```
"Docker-compose pull"
```

3. For each docker image run "docker tag <oldrepo/service:version> <newrepo/service:version>"
   e.g.

```
"Docker tag ccdcrepository.azurecr.io/webcsd:0.1.6  my.internal.registry/webcsd:0.1.6"
```

4. Run "docker push <newrepo/service:version>"
   e.g.

```
"docker push my.internal.registry/webcsd:0.1.6"
```

5. Create a new docker-compose file to keep your image overrides seperate and avoid them being reverted in updates e.g. "docker-compose.service-config.yml".
   The file will need to contain the new image location for each service copied into a new location.
   e.g.

```
version: '3.6'

services:
  webcsdbackend:
    image: my.internal.registry/webcsdbackend:0.1.6

  webcsd:
    image: my.internal.registry/webcsd:0.1.6

  database-server:
    image: my.internal.registry/csd-database:2022.1.0.alpha1

...
etc
```

6. Include the new file in the startup command, so if you are also using local database configurations the command will be:

```
docker-compose -f docker-compose.yml -f docker-compose.db-config.yml -f docker-compose.service-config.yml up -d
```

Please note that process will need to be repeated to copy further updates to your local repository.

## Usage

To access the WebCSD service locally go to http://localhost in a browser.

## Troubleshooting

To view the container logs, use the following command:

```sh
docker logs [OPTIONS] <CONTAINER>
```

For a list of containers, use the following command:

```sh
docker-compose ps
```

For more information see the Docker documentation: https://docs.docker.com/engine/reference/commandline/logs/

## Contact support

If you experience any difficulties with installing or using On-Site WebCSD, please contact our support team at support@ccdc.cam.ac.uk who will be happy to assist you.

## How can I check which version of the images I am currently running?

```sh
docker-compose images
```
