# Alternative ways to setup On-Site WebCSD

## Running the On-Site WebCSD installer from the command line

If installing On-Site WebCSD on a headless system you can run the installer non-interactively from the command line. There are two approaches to this.

1. Specify parameters on the command line. Everything in angle brackets must be replaced by your own details (and the angle brackets removed), and publicURI must resolve to your Docker host.

   ```sh
   # Install with automatic database server configuration (recommended)
   ./OnSiteInstallerExecutable --accept-licenses --confirm-command install --root "<installation directory>" licenseKey="<activation key>" username="<username for CCDC docker container registry>" password="<password for CCDC docker container registry>" publicURI="https://<hostname>:<port>" port="<port>" dbDumpFile="<path to database dump file>" sslCertLoc="<path to pfx cert file>" certPw="<pfx file password>"

   # Install and connect to a pre-existing database server
   ./OnSiteInstallerExecutable --accept-licenses --confirm-command install --root "<installation directory>" licenseKey="<activation key>" publicURI="https://<hostname>:<port>" port="<port>" csdDatabase="csd-database-only-development" identityDatabase="csd-identity-onsite-release-2" sslCertLoc="<path to pfx cert file>" certPw="<pfx file password>" dbConnString="Server:<database server name>;Port:<database server port e.g 5432>;User Id:<database user>;Password:<database password>;"
   ```

2. Create a `.env` file and copy it into your installation directory. As above, replace everything in angle brackets. The `.env` file below is for automatic database server configuration. If self-hosting the postgres server, remove the `POSTGRES_DUMP_LOC` line and change `Server=csd-database` in `DB_CONNECTIONSTRING` to `Server=<postgres_server_name>`.

```sh
SSL_CERT_LOC=<path to pfx cert file>                   
CCDC_LICENSING_CONFIGURATION=la-code;<activation key>;                   
PUBLIC_URI=https://<hostname>                   
PLATFORM_PORT=<port>                 
POSTGRES_DUMP_LOC=<path to database dump file>             
POSTGRES_DB=csd-database            
POSTGRES_PORT=5432           
POSTGRES_USER=postgres          
POSTGRES_PASSWORD=<test123>         
DB_CONNECTIONSTRING=Server=csd-database;Port=5432;User Id=postgres;Password=<test123>;        
CSD_DATABASE=csd-database                 
IDENTITY_DATABASE=csd-identity  
```

Now you can use a shorter command (answer Yes to the initial “Do you want to continue?” question).

```sh
./OnSiteInstallerExecutable --accept-licenses --confirm-command install --root <installation directory>
```

## Storing the docker images in your local repository

If you want to store the docker images in your own repository follow the below steps:

1. Download and unzip a copy of the source code of the latest release from [the Releases page](https://github.com/ccdc-opensource/on-site-webcsd/releases). Change to the `on-site-webcsd-VERSION` folder containing the docker compose files.

2. Run the below command to download the docker images

   ```sh
   docker compose pull
   ```

3. For each docker image run "docker tag <oldrepo/service:version> <newrepo/service:version>" e.g.

   ```sh
   docker tag ccdcrepository.azurecr.io/webcsd:0.1.6  my.internal.registry/webcsd:0.1.6
   ```

4. Run "docker push <newrepo/service:version>" e.g.

   ```sh
   docker push my.internal.registry/webcsd:0.1.6
   ```

5. Create a new docker-compose file to keep your image overrides separate and avoid them being reverted in updates e.g. "docker-compose.service-config.yml".
   The file will need to contain the new image location for each service copied into a new location. e.g.

   ```yml
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

   ```sh
   docker compose -f docker-compose.yml -f docker-compose.db-config.yml -f docker-compose.service-config.yml up -d
   ```

Please note that process will need to be repeated to copy further updates to your local repository.

## Kubernetes support

We do not currently offer Kubernetes support; please contact us to discuss your preferred installation options.
