# Support And Troubleshooting

## Contacting CCDC Support

If you experience any difficulties with installing or using On-Site WebCSD, please contact our support team at
[support@ccdc.cam.ac.uk](mailto:support@ccdc.cam.ac.uk) who will be happy to assist you.

## Providing feedback

If you would like to provide some feedback our support team would be happy to hear from you at the above email address.

## Licensing Options

The environment variable `CCDC_LICENSING_CONFIGURATION` needs to be set in the `.env file`. This will normally happen automatically when you enter your licence key at install time.
You may also need to edit it after installation, for example if your licence key has changed.

For best performance we recommend that this variable is set to use our standard node-locked licensing using this format
(where 123456-123456-123456-123456-123456-123456 should be your node-locked licence activation key):

```sh
CCDC_LICENSING_CONFIGURATION=la-code;123456-123456-123456-123456-123456-123456;
```

It is also possible to connect to your own local licence server:

```sh
CCDC_LICENSING_CONFIGURATION=lf-server;http://myserver:1234
```

More details on the options available with the `CCDC_LICENSING_CONFIGURATION` environment variable can be found in the Installation Notes for the latest CSD Portfolio release in the
[Documentation section of the CCDC website](https://www.ccdc.cam.ac.uk/support-and-resources/documentation-and-resources/).

## Troubleshooting

### How can I check which version of the images I am currently running?

```sh
docker compose images
```

### Viewing logs

To view the container logs, use the following command:

```sh
docker logs [OPTIONS] <CONTAINER>
```

For a list of containers, use the following command:

```sh
docker compose ps
```

Support may ask for the full set of logs to be sent when attending a ticket, use the following command:

```sh
# collect logs creates tar.gz file which can be sent to support.
cd <installation directory>
./collect_logs.sh
```

For more information see the Docker documentation: <https://docs.docker.com/engine/reference/commandline/logs/>

### Testing connectivity to a self-hosted Postgres server

To test whether your WebCSD server can connect to your Postgres server, run the following on the docker host.

```sh
# Pull postgres image
docker pull postgres:14.13
# Run container
docker run --name debug-postgres -e POSTGRES_PASSWORD=password -d postgres:14.13
# Shell into container
docker exec -it debug-postgres sh
# Attempt to establish a connection to the database server
# Replace my.postgres.server by the hostname of your postgres server
psql -h my.postgres.server -p 5432 -U postgres -d csd-database
```

If all is well the response to the final command will be similar to

```sh
psql (14.13 (Ubuntu 14.13-1.pgdg20.04+1))
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
Type "help" for help.
```

### While the docker login command works I am unable to pull docker images and receive an error

If you receive an error like

`ERROR: pull access denied for //ccdcrepository.azurecr.io/onsite/csd-database, repository does not exist or may require 'docker login': denied: requested access to the resource is denied"`

but the docker login command was successful then it is likely that you are using the _main_ branch rather than a _release_. Please ensure you use the latest official release from <https://github.com/ccdc-opensource/on-site-webcsd/releases>.

If you are unable to pull the public RabbitMQ image, you may be using an old version of Docker.

* To check your docker version type `docker version`
* To install the latest version of docker see [the Docker documentation](https://docs.docker.com/engine/install/#server)

### I am prompted to enter a licence key when accessing the Lattice GUI

The licence key should now only need to be entered via the installer or edits to the server configuration -
see [changing licence keys](https://github.com/ccdc-opensource/on-site-webcsd#changing-license-keys). If the front-end GUI is prompting for a licence key, this means the details
entered in the back-end configuration are invalid and must be updated as described in the linked page.

## I see an 'error:invalid_request' page when trying to log in to Lattice for the first time

Logging in to the On-Site installation for the first time may result in an error page displaying a message similar to:

```sh
error:invalid_request
error_description:The specified 'redirect_uri' is not valid for this client application.
error_uri:https://documentation.openiddict.com/errors/ID2043
```

This is likely to indicate a mismatch between the `PUBLIC_URI` field set in the .env file and the server URL specified in the browser.
A common cause is specifying the default port `:443` in the PUBLIC_URI field; the port should only be specified if it is non-standard (not the default 443)

To resolve the issue, first edit the .env file to ensure the PUBLIC_URI is the same as the URL given in the browser.
Once the .env file has been updated, restart the docker containers for the change to take effect.
