# Setting up CSD-Theory Web

## Introduction

⚠️ **Note: The 4.3.0 release does not support CSD-Theory Web**

⚠️ **Note: Restricting access to the CSD-Theory database is not possible with the 4.3.1 release. All users with an account will have the option to search the CSP landscapes.**

ℹ️ Ensure that your licence has the CSD-Theory feature enabled. If this is not the case, please contact our support team at [support@ccdc.cam.ac.uk](mailto:support@ccdc.cam.ac.uk)
who will be happy to assist you. Once an updated licence is available,
if necessary update the licence in the installation using the instructions on [changing licence keys](updates-and-uninstallation.md#updating-licence-keys).

This section of the documentation contains information on the setup options for CSD-Theory Web. CSD-Theory Web requires two databases:

1. A CSD-Theory metadata database containing additional theoretical data. This must be named `CSPDatabase.db`. This is populated during the ingestion script.

2. A CSD-Theory landscape database for initial searching of landscape structures, e.g. `CSPLandscape.csdsqlx`. This is populated during the ingestion script.

These two databases are used in conjunction to create the CSD-Theory charts and reports.

We have provided both a `CSPDatabase.db` and `CSPLandscape.csdsqlx` with an example landscape. You can use this to set up CSD-Theory before adding your own landscapes.
Once you have ingested your landscapes you will need to remove the example landscape following the instructions further down the page.

## Adding the CSD-Theory Landscape Database

### Adding an example CSD-Theory database to the Server

* Ensure you have copied the sample [CSPDatabase.db](https://github.com/ccdc-opensource/on-site-webcsd/blob/main/sample-data/CSPDatabase.db) and [CSPLandscape.csdsqlx](https://github.com/ccdc-opensource/on-site-webcsd/blob/main/sample-data/CSPLandscape.csdsqlx) from the `/sample-data/` to your `/csd-theory/` folder.
* Containers run as the ccdc user, so ensure the folder and associated files are owned by the ccdc user. Run e.g. `sudo chown -R ccdc:ccdc csd-theory` on the folder, and also database files which the server will edit when the ingestion script is run.
* The WebCSD server should be restarted for the new database to become searchable by users.

### Creating or Updating a CSD-Theory Landscape Database

To create or update a CSD-Theory Landscape Database, you will need the Python Utilities. These can be downloaded from the [CCDC Downloads page](https://www.ccdc.cam.ac.uk/support-and-resources/downloads/)
under CSD Python API > Python CSD Python API Utilities, or by filling in the form to [request download links](https://www.ccdc.cam.ac.uk/support-and-resources/csdsdownloads/).

⚠️ Note it is not currently possible to run the scripts if using a self-signed SSL certificate.

Relevant scripts can be found under `ccdc > utilities > csp`.

Use the `import_csp_landscape.py` script to import the CIFs. This script can build a new database or update an existing one. Run the script as a user who  can write to the `csd-data` directory.

| argument  | description  |  examples |
|---|---|---|
| landscape_database  | URL of the landscape database server (the same as the URL you use in your web browser)  | https://csd-theory.ccdc.cam.ac.uk <br /> https://csd-theory.ccdc.cam.ac.uk:8080  |
| structure_database  | crystal structure database (.csdsqlx). <br /> Either point to an existing database via the filepath, or give it a name to build it directly.  | E:\CSP_Upload\existingLandscapeDatabaseName.csdsqlx <br /> newLandscapeDatabaseName.csdsqlx  |
| cif_files           | CIF file(s) for structure(s) to be imported to the landscape and structure databases (.cif)  | E:\CSP_Upload\CSP_enriched_CIFs\singlestructurelandscape.cif <br /> E:\CSP_Upload\CSP_enriched_CIFs\*.cif  |
| -n LANDSCAPE_NAME   | The name of the CSP landscape. <br /> If not included, landscape name will be generated from the CIFs  |   |
| -v, --validate      | Validate, rather than add, that the CIF files have been added to a CSP landscape  |   |

Some sample commands include:

Validate importing a single CIF into an existing database at <https://csd-theory.ccdc.cam.ac.uk> :

`import_csp_landscape.py https://csd-theory.ccdc.cam.ac.uk E:\CSP_Upload\existingLandscapeDatabaseName.csdsqlx E:\CSP_Upload\CSP_enriched_CIFs\singlestructurelandscape.cif -v`

Import a folder of CIFs into a new database called "NewDatabase.csdsqlx" at <https://csd-theory.ccdc.cam.ac.uk:8080/>, with landscape name “NewLandscape”:

`import_csp_landscape.py https://csd-theory.ccdc.cam.ac.uk:8080 NewDatabase.csdsqlx E:\CSP_Upload\CSP_enriched_CIFs\*.cif -n NewLandscape`

### Adding the CSD-Theory Landscape Database to the Server

The CSD-Theory Landscape database needs to be added to the `/csd-theory/` folder.

If you are copying the database via an SCP command to transfer the file between a local host and a remote host, you may need to run chown on the `/csd-theory/` folder to change it to the owner who is running SCP, such as onsiteadmin. 

Ensure once the SCP command or ingestion script run is complete, you change the owner of `/csd-theory/` and all databases inside back to ccdc. The user "ccdc" must have read access to the CSP databases, as indicated in [link](https://github.com/ccdc-opensource/on-site-webcsd#installation).

ℹ️ The following section applies only to versions 4.2.1 and earlier:

### Mapping to the databases in docker-compose.database.yml

Start by taking down the instance of the WebCSD docker container and update the docker-compose.database.yml file. You can start from docker-compose.sample-csd-theory-db-config.yml. Updates should include:

Update locations and names in the the webcsdbackend volumes and environment sections, e.g.

```services:
  webcsdbackend:
    volumes:
      - ./csd-data/LandscapeDatabaseName.csdsqlx:/csd-data
    environment:
      - ServiceSettings__Databases__2__Name=LandscapeDatabaseName
      - ServiceSettings__Databases__2__ConnectionString=/csd-data/LandscapeDatabaseName.csdsqlx
      - ServiceSettings__Databases__2__Speciality__0=CSP
```

the volumes of the webcsd-theory section and optional settings in the environment section (described below), e.g.

```webcsd-theory:
    volumes:
      - ./csd-data/CSPDatabase.db:/app/CSPDatabase.db
      - ./csd-data/LandscapeDatabaseName.csdsqlx:/app/databases/CSPLandscape.csdsqlx

    environment:

       - ReportSettings__CspStructureDatabaseConnectionString=/app/databases/CSPLandscape.csdsqlx

       - ReverseProxy__Clusters__csdtheory__Destinations__csdtheory/destination1__Address=http://webcsd-theory:${WEBCSD_PORT}
```

Once you have edited and saved the docker-compose.database.yml file, restart the docker container. You should now be able to access all the basic CSD-Theory functionality.

#### Reports

If you wish to run reports, the Landscape Database needs to be mapped to CSPLandscape.csdsqlx and you need to include the ReportSettings environment information.

#### Ports other than 80

If you are running on a port other than 80, ensure the ConnectionString environment detail (ReverseProxy) includes the port information. Do not include a backslash on the CSP url.

## Additional landscape analysis

Once a CSP landscape has been ingested into CSD-Theory there are additional steps that can be taken to add extra information.

### Similarity heatplot

To compare structures within a landscape, the script csp_similar_structures.py will produce a heatplot showing the similarity between (up to) the 25 lowest energy structures. The script has a syntax 

```python csp_similar_structures.py http://my.landscape.server:12345 wonder_drug.csdsqlx wonder_drug --pxrd_threshold 0.98```

This will run similar structures calculations in the CSP landscape wonder_drug. The optional argument --pxrd_threshold sets the PXRD similarity threshold.

### Cross reference to experimental structures

Structures within a landscape can also be compared to experimental data from an in-house database. This requires the script csp_cross_reference.py with the syntax

```python csp_cross_reference.py http://my.landscape.server:12345 wonder_drug.csdsqlx my_in-house_database.csdsql```

This will cross reference all structures in the CSP database "wonder_drug" with observed structures in the my_inhouse_database of experimental structures. If no in-house database is referenced the script will run a comparison against the CSD.

## Remove a landscape

You can remove a landscape by running the following Python script.

```python delete_csp_landscape.py http://my.landscape.server:12345/ wonder_drug.csdsqlx wonder_drug```

More information on this script can be found in the CSD Python API documentation.

## Structure links

Additionally, it is possible to add links from theoretical structures to another web server.
To do this create a CSV file named `StructureLinks.csv` with the following column headings, and as many rows as you have links.

| Identifier | Key |  Value |
|---|---|---|
| structure identifier of a CSP landscape structure in the CSPLandscape.csdsqlx file | StructureLink | Any URL or file path |

Copy `StructureLinks.csv` into the `csd-theory` folder and restart the server.
