# Known Issues

## Restart the message queue

With 4.3.0 and 4.3.1 rabbitmq needs to be rerun after installation. If you are installing manually please run the following:

```sh
# Replace `INSTALLDIR` by your WebCSD installation directory
cd INSTALLDIR
docker compose -f docker-compose.yml up -d
```

## I get an 'Upload failed' message when trying to upload a CIF

When the On-Site server is first installed there can be situations where there is a name mismatch between a self-signed SSL certificate and the server ID.
This can be resolved by restarting the Docker stack e.g.

```sh
# Replace `INSTALLDIR` by your WebCSD installation directory
# Include docker-compose.insecure-configuration.yml if using a self-signed certificate
cd INSTALLDIR
docker compose -f docker-compose.yml -f docker-compose.ssl.yml docker-compose.insecure-configuration.yml down
docker compose -f docker-compose.yml -f docker-compose.ssl.yml docker-compose.insecure-configuration.yml up -d
```

## Viewing charts in CSD-Theory gives a 'WebGL is not supported by your browser' message

This issue can be resolved by changing browser settings.

For Chrome browsers:

* Go to `chrome://flags`
* Search for "Override software rendering list", set it to **Enabled** and relaunch

For Firefox:

* Go to `about:config`
* Search for `webgl.force-enabled`, and set it to **true**
* Ensure webgl.disabled is set to **false**
