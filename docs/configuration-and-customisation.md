# WebCSD Configuration And Customisation

## Introduction

This section details a number of customisations you can make to WebCSD. In order to keep these settings from being accidentally over-written,
we recommend putting any customisations in a separate docker-compose file, e.g. `docker-compose.customisations.yml`. This acts as an [override file](https://docs.docker.com/compose/extends/)
which you will have to include in the startup command. Alternatively you could add these settings into the aforementioned db-config file to keep all of your configuration in one place.

## Formatting conventions

* Any file location beginning `/path/to` should be replaced by the file location on your docker host.
* Any filename or text in angle brackets should be replaced by your own content and the angle brackets removed.
* In lines with two pathnames separated by a colon, the first is generally the location on your docker host and needs to be customised. The second is the location in the container and should be left alone.

For example, you might change the line

```yml
- /path/to/logo/<CompanyLogo.png>:/app/wwwroot/images/OnSite-logo.png
```

to

```yml
- /usr/local/logos/MyCompanyLogo.png:/app/wwwroot/images/OnSite-logo.png
```

## Adding system administrators

WebCSD allows you to display local system administrators who can provide support.
To add one or more email addresses of system administrators, add them to the webcsd section of your own docker-compose file.

For example:

```yml
 - Customisations__SystemAdministrators__0=TestAdmin1@ccdc.cam.ac.uk
 - Customisations__SystemAdministrators__1=TestAdmin2@ccdc.cam.ac.uk
```

## Adding your own company logo

Replace the standard "CCDC On-Site WebCSD" logo at the top right of the page, with your own company logo, by adding the following to the webcsd volumes section of your own docker-compose file.

```yml
- /path/to/logo/<CompanyLogo.png>:/app/wwwroot/images/OnSite-logo.png
```

As there is no height or width specified when displaying this image, please ensure the replacement is of a reasonable size, to integrate with the layout of the page.
For example, the CCDC logo, at the top left of the page, is 142 x 50 pixels.

## Changing the Identifier text

Change the label used for entry identifiers in the search results list, by adding the following settings to the webcsd environment section of your own docker-compose file.

```yml
- Customisations__Identifier__Full=<Identifier text>
- Customisations__Identifier__Short=<Identifier text short version>
```

## Changing the non-CSD entry title

Change the text displayed before the identifier in the main title, when viewing a non-CSD entry, by adding the following setting to the webcsd environment section of your own docker-compose file.

```yml
- Customisations__NonCsdEntryTitle=<In-house entry text>
```

## Customised Header and Information pages

To add custom HTML above the search form on the home page, create a file containing a HTML snippet and add the following setting to the webcsd volumes section of your own docker-compose file.

```yml
- /path/to/<Custom_Header.html>:/app/wwwroot/static/HomeHeader.html
```

Clicking on About This Service in the footer or accessing /access-structures-information brings up the information page. To add custom HTML for this page, create a file containing a HTML snippet
and add the following setting to the webcsd volumes section of your own docker-compose file.

```yml
- /path/to/<Custom_Information.html>:/app/wwwroot/static/Information.html
```

**PLEASE NOTE:** The content of these pages is rendered by the site as is, without encoding or validation. Any embedded scripts in `HomeHeader.html` or `Information.html` will run.
It is the site administrator's responsibility to ensure that these pages are properly secured.

## Associated Structure Links Configuration

On-Site WebCSD can be configured to display links associated with a structure, e.g DOI links or File links. To do this you will need a CSV file containing structures with their associated data
and any files you may want to download, with a separate CSV file for each database hosted on the server.

The CSV file should have three headers - Identifier,Key,Value. The Identifier refers to the database identifier for the individual structure in the database.
The Key is `StructureLink` - other keys have been used in previous version of the On-Site platform but are no longer used. The value is the link required e.g. DOI links or File links.

Note: URL links require a full path including both a root domain and page to resolve correctly. For example, a link to `https://www.ccdc.cam.ac.uk/support-and-resources/` will resolve correctly,
whereas a link to just the root domain `https://www.ccdc.cam.ac.uk/` will not.

:information_source: **The following information refers to the 4.3.0 release**

Users with admin rights can upload the CSV file directly via the **Manage databases** plugin. Each database has a 'three dots' button that links to the **Import additional data** page.
From this page the CSV file can be uploaded.

:information_source: **The following information refers to the 4.2.1 release**

The CSV file is used by webcsdbackend and should configured in the environment section by adding the following line to the environment section under webcsdbackend in your in-house database yml file:

```yml
services:
  webcsdbackend:
    volumes:
      - /path/to/database-1:/csd-data/database-1
      - /path/to/database-2:/csd-data/database-2
      - /path/to/structure-links.csv:/csd-data/structure-links.csv:RO
    environment:
      - ServiceSettings__Databases__2__Name=database-1
      - ServiceSettings__Databases__2__ConnectionString=/csd-data/database-1
      - ServiceSettings__Databases__3__Name=database-2
      - ServiceSettings__Databases__3__ConnectionString=/csd-data/database-2
      - ServiceSettings__StructureInfoLocation=/csd-data/structure-links.csv
```

This mounts the `structure-links.csv` in the `csd-data` directory. Change the location to where the csv file you are using is located.

The related files themselves are used by webcsd and their location is configured in the volumes section of the in-house database yml file, i.e.

```yml
webcsd:
    volumes:
      - ./path/to/structure/files:/structure-files
```

Here the structure files folder `./path/to/structure/files` is mounted as `/structure-files`, which is where WebCSD will expect to find them.

The related files can be stored within a folder structure. It is important to note that any file links set up in the CSV file must point to the correct location of these files,
otherwise the file will not download when the link is clicked. For example, given the folder structure:

```sh
\structure-files
  \structure1
    \rawfiles
      myfile.raw
    \xyefiles
      myfile.xye
```

In the CSV file, the above would be referenced by:

```sh
Identifier,Key,Value
STRUCT01,StructureLink,/structure-files/structure1/rawfiles/myfile.raw
STRUCT01,StructureLink,/structure-files/structure1/xyefiles/myfile.raw
```

## Customising The Header

The structure links are displayed when viewing a structure. By default these are shown in a box labelled 'File Locations'. To change this for your installation,
add the following setting to the webcsd environment section of your own docker-compose file:

```yml
- Customisations__StructureLinksTitle=<title text>
```

## Only enable Structure Links for a specific role

:information_source: **The following information refers to the 4.2.1 release**

You can enable structure links downloads just for specific users, using a new Role that is automatically created.

When enabled, users will only be able to download structure links if they have been specifically assigned the StructureLinks role. If they have not been given access,
the buttons will be greyed out and a message appears on hover.

Structure links will be **enabled** in the following circumstances:

* Structure links role checking is disabled
* Structure links role checking is enabled and the users has the StructureLinks role

Structure links role checking is disabled by default. To enable this for your installation, add the following setting to the webcsd environment section of your own docker-compose file:

```yml
- Customisations__StructureLinksDisableByRole=true
```

## Change the message displayed if Structure Links are disabled for a user

If Structure links role checking is enabled, when a user does not have the required role, a message will display when attempting to click a download button.
This message is customisable, to change it, add the following setting to the webcsd environment section of your own docker-compose file:

```yml
- Customisations__StructureLinksDisabledText=<message text>
```

## Report Downloads - Configuration

:information_source: **The following information refers to the 4.2.1 release**

You can restrict the download of the reports generated by the PXRD Match and Report buttons to specific users by adding them to the reportDownload role (which is automatically created).

Users with the reportDownload role have both the PXRD Match and Report buttons on the landscape results page enabled and available for use.
For users without the reportDownload role, both buttons are disabled and greyed out with a message appearing on hover.

The reportDownload role checking is disabled by default. To enable this for your installation, add the following setting to the webcsd environment section of your own docker-compose file:

```yml
- Customisations__ReportDownloadDisableByRole=true
```

## Change the message displayed if report downloads are disabled for a user

If report download by role is enabled, a user without the required role sees the default message `This functionality has not been enabled.
Please contact the system administrator to add this role to your account.` when attempting to click any of the report download buttons. This message is customisable. To change it, add the following
setting to the webcsd environment section of your own docker-compose file:

```yml
- Customisations__ReportDownloadDisabledText=<message text>
```
