# Access Control To In-House Databases

## Overview

:information_source: **These instructions relate to the current 4.3.0 release**

To create accounts users have two options:

1. Using single sign-on - see [Single Sign On (SSO)](single-sign-on.md).
2. Using form based registration - documented below.

The first user to register will be set as the admin and they have the ability to grant admin rights to other users; there is no limit on the number of admin accounts allowed.

Users with administrator rights will have the ability to see functionality in the **Settings** section: Manage users, Manage groups and Manage databases.
Users without these rights will only see options for searching databases in the **WebCSD** section: Simple Search and CSD-Theory Web (if applicable).

By default all non-admin users start with _no_ access to any in-house databases but will be able to see and search the CSD; by contrast users with admin rights are able to see all databases.
Users who are not logged in cannot access any databases.

Note: Users will be required to log in to WebCSD by clicking the **Sign In** button at the top-right of the WebCSD page after they have logged in to the initial Lattice home page.
This second log in will not require the user to re-add their username and password, but by clicking the Sign In button, the user's credentials are passed to the WebCSD search engine.

## Use of the Manage users plugin

The Manage Users plugin gives the ability to reset a password, grant administrator rights or delete the user from the system

## Use of the Manage groups plugin

To make it easier to grant access rights to users, the Manage groups plugin allows an administrator to add many individual users to a single group.
Access to in-house databases is then granted to this group of users, rather than having to assign permissions individually. A new group can be created by selecting the **Add new group** button,
at which point there are options to give a group name and description. Users can then be added or removed from this group by selecting it in the Manage groups plugin in future.

## Access control to in-house databases

Having admin rights gives the ability to add, update or delete any in-house databases from the Manage databases plugin.
To grant access to other users, the Database management plugin contains the option to add groups to each database (using the padlock icon next to the database).
All users are automatically included in an 'Everyone' group; should user-level access control not be required, ensure each database has this group selected.
Alternatively, different user groups can be selected on a per-database level, allowing the ability to control access to all structures.

:information_source: **The User management instructions below refer to the earlier 4.2.1 release**

## User management (Admin)

1. Navigate to the User Admin section via WebCSD or the New Lattice Platform.

   ![image](https://github.com/user-attachments/assets/1b75c2af-4daf-4832-8b32-459de9d2450f)

2. Go to Databases tab. Here you can see an index assigned to each database which is taken from the container configuration.

   ![image](https://github.com/user-attachments/assets/6c303687-8ec1-46a9-993d-b6b6f9f0d6da)

3. Go to Roles tab. Here you can see the name of available roles and indices of the databases accessible for the users with this role.

   ![image](https://github.com/user-attachments/assets/be3fab60-458b-4895-bb58-dc5e9663dadf)

4. You can add a new role by entering its name into the relevant field and clicking Add New Role.

   ![image](https://github.com/user-attachments/assets/1203218b-bb26-49c5-99e2-a9c0309d812a)

5. You can click on the Edit button to the right of each role and tick those databases which you want to make available for the users with this role.
   Click on Submit when all the relevant databases are selected.

   ![image](https://github.com/user-attachments/assets/a691132f-06fc-41a6-a016-95bbe808a344)

6. You can assign roles separately for each user or in a bulk manner (described in the next section). To assign role(s) to one user, go to Users and click Manage roles to the right of their email.

   ![image](https://github.com/user-attachments/assets/4380b95f-5ff9-4a1d-84eb-5b47fb543b54)

### Bulk user management (Admin)

1. Navigate to the User Admin section via WebCSD or the New Lattice Platform.

   ![image](https://github.com/ccdc-opensource/on-site-webcsd/assets/84066349/9f95d3f8-7839-4adf-abcc-70dbb1c95069)

   ![image](https://github.com/ccdc-opensource/on-site-webcsd/assets/84066349/f47d846f-0146-4645-a249-0813aad7115f)

2. Select Bulk Change Users.

   ![image](https://github.com/ccdc-opensource/on-site-webcsd/assets/84066349/29d6e760-93c4-4bd3-ae01-ef1e47055200)

3. Create a CSV file with user data in the following format.

   ```sh
   FirstName,LastName,EmailAddress,Roles
   Joe,Bloggs,joe@joe.com,"Basic"
   ```

   You should then have a file that looks something like this:

   ![image](https://github.com/ccdc-opensource/on-site-webcsd/assets/84066349/0121f31a-4e3c-42cc-a526-a224880ea573)

4. Select Bulk Change CSV File to upload the populated CSV file and hit the Upload Bulk Change CSV File button to upload and validate the provided user details.

5. Validation results will be displayed. If any errors are found, you'll be alerted to make the necessary corrections and re-upload the file.

   File with failed validation:

   ![image](https://github.com/ccdc-opensource/on-site-webcsd/assets/84066349/c9be5dc9-2fff-4dc4-a9b8-02096084e069)

   File with successful validation:

   ![image](https://github.com/ccdc-opensource/on-site-webcsd/assets/84066349/720a8c71-414b-4339-99f8-929978bca8df)

6. After carefully reviewing the user data, click Submit User Changes to finalize the bulk registration/change process.

7. Download the generated report by clicking on the Export User List to Excel button. **This contains the initial passwords for the created accounts. You will not be able to access this later.**

### FAQs

**What fields are required on the CSV template file?**
Only the Email address and Roles are required.

**What happens when a user already exists?**
The User’s role(s) would be updated (see below)

![image](https://github.com/ccdc-opensource/on-site-webcsd/assets/84066349/0284e6ec-a68f-40dd-932b-b07656c0111a)

**I forgot to download the final report containing the user passwords, what do I do?**
Admin would have to reset/change each user's password manually.

**Can I update existing users' roles in bulk as well?**
Yes, populate the CSV file with the users Email address and their respective updated Roles, upload and the system will automatically update their roles.

### Disabled Local Authentication

Your site admin, based on company policy, may decide to restrict users to sign in to the application using only SSO, and hence can disable the option to sign in locally with a username and password.

## Changes from the 4.3.0 release

For earlier versions of the On-Site platform it was possible to disable all user access control. This ability has now been removed due to additional functionality granted to users with administrator rights.
