# Setting Up A Self-Hosted PostgreSQL Server

## Install PostgreSQL server

With the release of version 4.3 of the On-Site platform, by default the database server runs in a docker container.
However, there is also the option to use your own instance of Postgres to host the database.

Recommended requirements for a Postgres server:

* Postgres version 14 or newer
* 100GB free hd space

For supported operating systems the native version of Postgres should be sufficient. To install on RHEL / Rocky Linux:

```sh
sudo yum install postgresql-server
```

To install on Ubuntu:

```sh
sudo apt-get install postgresql
```

N.B. The PostgreSQL server must also be configured to allow network connections from the WebCSD server.

## Getting the latest version of the CSD Database

Contact [support@ccdc.cam.ac.uk](mailto:support@ccdc.cam.ac.uk) for assistance in getting the postgres database / databases you need.
They will be able to provide you with a link to download the databases to your infrastructure.

## Restore database

Restore the database you have downloaded into your new environment. If you are replacing an existing database first delete it:

```sh
psql -c "DROP DATABASE \"csd-database\""
```

Then install the new database:

```sh
psql -c "CREATE DATABASE \"csd-database\""
pg_restore --no-owner -d csd-database < downloaded_database_name.dump >
```

If you are unable to perform a database backup as described above, please contact [support@ccdc.cam.ac.uk](mailto:support@ccdc.cam.ac.uk) for assistance.

## Point services to use the Postgres database

The best way to do this is at install time.

* If running the GUI installer, select the option “Configure custom database server” on the Database Config screen.
* If running the CLI installer, set the databaseConnectionString parameter.
```./OnSiteInstaller databaseConnectionString="DB_CONNECTIONSTRING=Server=database-server;Port=5432;User Id=postgres;Password=< passwordhere >"```

Alternatively, after installation you can set the DB_CONNECTIONSTRING variable in the `.env` file as above.
