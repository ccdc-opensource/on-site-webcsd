# Updating Your In-House Databases

## In-house database updates from version 4.3.0

Updates to in-house databases are performed via the 'Manage databases' plugin accessible to all users with administrator rights.

### Initial database setup

* From a new installation, a new, empty database must be created by clicking the 'Add new database` button from within the plugin, this will automatically be configured for searching with WebCSD.
* You will be prompted to add a name for the database, and optionally a description and colour.
* If you have an existing in-house database, the .csdsqlx database file created by Decifer or CSD-Editor can be imported directly by clicking on the upload icon next to the database.
This navigates to an `Add structures` page where the existing database can be uploaded.

### Adding new structures

* Additional `.csdsqlx` databases can be added to an existing database following the same procedure as above, therefore any existing database creation workflows can be maintained.
* New CIF files can be added to the database in a similar method, however multiple CIFs can be added simultaneously. An important difference to note is that
new structures in CIF format will be processed using the Decifer algorithm that assigns chemistry, resolves any disorder modelled in the structure and adds metadata from the CIF
into the database entry. Additions from an existing database are _not_ altered from the original database file.

### Editing existing structures

* By selecting the `Review structures` icon next to the database, a list of all entries in the database is shown on a new `Review structures` page. Individual entries can be selected from this list;
clicking on the `Edit structures` button will then create a `.csde2` file which can be downloaded from the browser. This file should be automatically opened by the CSD-Editor software
available from the CSD Portfolio (versions from CSD Portfolio release 2025.3 onwards) to allow edits to the database entries. Once edits have been completed,
clicking the Save button within the editor will upload the entries back into the database hosted on your On-Site server.

## Database updates for versions prior to 4.3.0

To update an existing database, first check the location of the database that you want to update. This will be specified in the `webcsdbackend` volumes section
of your docker-compose or in-house database configuration file. Copy the new version of the database to the required location, then do the following:

```sh
docker-compose down

docker-compose -f docker-compose.yml -f docker-compose.db-config.yml up -d
```
