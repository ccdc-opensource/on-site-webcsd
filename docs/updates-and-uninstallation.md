# Updates and uninstallation

## Using the maintenance tool

The maintenance tool can be run non-interactively from the command line too.

```sh
# To uninstall, this will give a Yes/No prompt to confirm the uninstall
./maintenancetool purge

# To upgrade (ensure in-house database have been exported and if using the installer setup postgres server that the database dump file has been replaced with the update in the same location originally specified - can be found in docker-compose.postgres.yml)
./maintenancetool update dbDumpFile="<path to database dump file>" overWriteDb="True"
```
