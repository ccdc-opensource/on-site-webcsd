# On-Site WebCSD

On-Site WebCSD is currently only available to CCDC Research Partners and other invited groups.

This readme contains information required for installation and updates of On-Site WebCSD.

Additional information including user configuration and troubleshooting can be found in the wiki at https://github.com/ccdc-opensource/on-site-webcsd/wiki.

This release is an Alpha release aimed at getting feedback on the installation and update process.

This alpha release does not contain all the functionality currently provided in On-Site WebCSD. All functionality will be provided in the full release.

## Prerequisites

A standard [Docker Server](https://docs.docker.com/engine/install/#server) and [docker-compose](https://docs.docker.com/compose/install/) installation is required for installation. This will run on [Docker Desktop](https://docs.docker.com/engine/install/#desktop), however this is not recommended and may [require a license](https://www.docker.com/legal/docker-subscription-service-agreement/).

Access to the CCDC container registry will require a username and password that will be provided by CCDC Support.

A valid license key will be required to use the software.

You will need either:

A download of the desired [release](https://github.com/ccdc-opensource/on-site-webcsd/releases).
Click on the release you want to use, and download the source code zip and unpack.

or

A clone of this repository (this will require a git installation).
git clone https://github.com/ccdc-opensource/on-site-webcsd.git

## Initial recommended specification

This is our initial estimate of what will be required to run WebCSD well.
170GB free hd space, 32GB ram, 8 core cpu, Linux OS

## In-house Database Configuration

On-Site WebCSD can be configured to read from in-house databases. To enable these databases, copy and rename the file `docker-compose.sample-db-config.yml` to `docker-compose.db-config.yml` and edit the `volumes` section to point to any in-house databases and edit the `environment` section to configure the application to recognise these databases. More information is given in the notes & example sections of the sample file. This acts as an [override file](https://docs.docker.com/compose/extends/) which you will have to include in the startup command.

## Installation

After either unpacking the release source code onto the server on which the software will be installed or cloning the git repo and pulling it you will need to go into the on-site-webcsd directory and copy the environment file `sample.env` as `.env` then populate with suitable values.

```
cd on-site-webcsd
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

#Use one of the following two commands depending on if you have in-house database configuration
#Use this command if you have no in-house database configuration
docker-compose up -d

#Use this command if you have in-house database configuration
docker-compose -f docker-compose.yml -f docker-compose.db-config.yml up -d
```

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
#Use one of the following two commands depending on if you have in-house database configuration
#Use this command if you have no in-house database configuration
docker-compose up -d

#Use this command if you have in-house database configuration
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
on-site-webcsd_ccdc-csd-searchservice_1
on-site-webcsd_ccdc-csd-resultstore_1
```

For more information see the [Docker volumes documentation](https://docs.docker.com/compose/compose-file/#volumes).

## Usage

To access the WebCSD service locally go to http://localhost in a browser.

## Contact support

If you experience any difficulties with installing or using On-Site WebCSD, please contact our support team at support@ccdc.cam.ac.uk who will be happy to assist you.

## Kubernetes support

If you wish to run WebCSD in a Kubernetes environment you can use the `Kompose` tool. Kompose takes Docker Compose files and translates them into Kubernetes resources. More information can be found here:

Github: https://github.com/kubernetes/kompose

Website: https://kompose.io/

There are a few steps that you will need to follow, to complete your implementation.

#### 1. Create a pull secret

```
kubectl create secret docker-registry pullsecret --docker-server=ccdcrepository.azurecr.io --docker-username=<your-token-name> --docker-password=<token-password> --docker-email=<your-email>
```

Where `pullsecret` is the name given to the pull secret referenced in the docker-compose file. If you choose a different name, you will need to update the `kompose.image-pull-secret` label in the `x-labels` section at the top of the docker-compose file.

#### 2. Replace all ${XXX_YYY} variables in the docker-compose file with their actual values

- ${CSD_DB_PASSWORD}
- ${CSD_CACHE_PASSWORD}
- ${CCDC_LICENSING_CONFIGURATION}

#### 3. Run the `Kompose` tool to convert docker-compose files

```
kompose -f docker-compose.yml [-f docker-mycustomisations.yml] convert
```

This should output a bunch of yaml files defining all the kubernetes resources.

#### 4. Start everything up

```
kubectl apply -f .
```

To check everything is working:

```
kubectl get all
```

At this point you should be able to navigate to the site's home page and searching the CSD should return results. To get all your in-house and supporting data into the pods, carry on to the next step.

#### 5. Copy all your required files to the relevant pods.

To get a list the pods, as you will need their names:

```
kubectl get pods
```

1. Copy OnSite database files to the webcsdbackend pod

```
kubectl cp .\csd-data\. <webcsdbackend-POD>:/csd-data/
```

Note: If you are using WebCSD-Theory, include your CSD Landscape database.

2. If you are using WebCSD-Theory, copy the database files to the webcsd-theory pod

```
kubectl cp .\csd-data\CSPLandscape.csdsqlx [webcsd-theory-POD]:/app/databases/
kubectl cp .\csp-data\CSPDatabase.db [webcsd-theory-POD]:/app/
```

3. If you are using structure link files
   Copy the structure links (CSV) file to the webcsdbackend pod (if not already done as part of copying database files in 5.1)

```
kubectl cp .\csd-data\structure-links.csv [webcsdbackend-POD]:/csd-data/
```

Copy all structure link files to the webcsd pod

```
kubectl cp .\structure-files\. <webcsd-POD>:/structure-files
```
