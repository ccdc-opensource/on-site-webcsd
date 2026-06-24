# Identity Gateway (User Account Database)

## Introduction

The identity gateway has been implemented since version 4.0. It provides a way to authenticate and manage users signing into On-Site Lattice and WebCSD. It holds the following responsibilities:

* Manage my user profile - Allow users to manage their passwords, and change aspects of their profile.

* User Administration - Administrators have access to an area to manage users, update passwords and set group permissions.

* Authentication endpoints - Users in On-Site Lattice and WebCSD will be redirected to the identity gateway to sign in.

## User Databases

The identity gateway needs a postgres database to manage user data. This is similar to the userdata sqlite database used in previous versions.

The identity gateway will create this database on initial startup if one does not exist.

To configure database connections, update the following section of the docker-compose file:

```yml
 ccdc-identity:
    environment:
      - "ConnectionStrings__DefaultConnection=Server=database-server;Port=5432;Database=identity;User Id=postgres;Password=${CSD_DB_PASSWORD};"
      - "ConnectionStrings__SqlitePath=userdata/UserLogin.db"
```

The sqlitepath setting is required if you have a previous userdata sqlite database that you would like to retain user data from. See below the section on Upgrading Identity for more information.

## Upgrading User Account Database From Previous Versions

### Version 4.0 Changes

The system from version 4.0 onward has introduced significant changes in how users are managed. A new Identity gateway service manages users for all services. This means:

* The new Identity gateway has its own new postgres user database

* Users are now administered via the Identity Gateway

* Users sign in and out via the Identity Gateway

* Users can manage their own profile (change password, etc) via Identity Gateway

### User Database Migration

Up to version 3.0, users were held in a Sqlite database managed by WebCSD, users were also managed via WebCSD.

Version 4.0 includes an automatic migration mechanism that undertakes the following functions:

* Creates a new postgres database

* Generates a schema for user management

* Reads the previous Sqlite database (if one exists) and migrates all users and roles to the new database

### FAQs

#### Will users need to re-register?

No. Any users that had registered (or been registered) for version 3.0 or previous will be automatically migrated.

#### Will users need a new password?

No. Passwords will also be migrated as part of the upgrade process.

#### What will happen to the old Sqlite database?

This will be preserved. The database will not be deleted, but it will no longer be used.

#### When will migration run?

When the identity gateway starts up for the first time after the upgrade, it will create the database and then attempt a migration if it can find a previous user database.

#### How does this work with Single Sign On?

Single sign on user data will be migrated and users should be able to sign on as they did before.

#### Can I re-run the migration at a later date?

Yes. Just remove the identity postgres database and restart the service. On startup, it will attempt another migration.

#### How does the migrator know where my Sqlite database is?

The internal configuration for the identity gateway contains a connection string, this can be configured as detailed above.

#### Can I start afresh with a blank user database?

Yes, either remove or rename the Sqlite database, or change the identity gateway connection string to point elsewhere.
