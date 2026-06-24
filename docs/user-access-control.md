# Access Control To In-House Databases

## Overview

:information_source: **These instructions relate to the current 4.3.0 release**

To create accounts users have two options:

1. Using single sign-on.
2. Using form based registration.

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

5. You can click on the Edit button to the right of each role and tick those databases which you want to make available for the users with this role. Click on Submit when all the relevant databases are selected.

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

1. Select Bulk Change CSV File to upload the populated CSV file and hit the Upload Bulk Change CSV File button to upload and validate the provided user details.

2. Validation results will be displayed. If any errors are found, you'll be alerted to make the necessary corrections and re-upload the file.

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

## Setting up single sign-on (Admin)

:information_source: **These instructions apply to both the 4.3.0 and 4.2.1 releases**

### Prerequisites

To Enable SSO for On-Site users, site admin must first register an application with a preferred OpenID Connect identity provider, and obtain these open id access credentials:
Client id, Client secret, Authority and CallbackPath. The following sub-sections provide guidelines on how to register an application with major identity providers
such as Microsoft - Azure Active Directory (AAD), Google and LDAP providers such as Windows server Active Directory.

**To use OpenID Connect (SSO), SSL must be enabled. please see [Updating the SSL certificate](updates-and-uninstallation.md#updating-the-ssl-certificate) for how to set that up.**

### Azure Active Directory (AAD)

* Sign in to the [Azure portal](https://portal.azure.com/).

* If you have access to multiple tenants, use the Directories + subscriptions filter in the top menu to switch to the tenant in which you want to register the application.

* Search for and select Azure Active Directory.

* Under Manage, select App registrations > New registration.

* Enter a Name for your application, for example CCDC-WebCSD. Users of your app might see this name, and you can change it later.

* Add the URL `https://{webcsd-url}/identity/signin-oidc` in Redirect URI as a Web platform.

* Select Register.

* Under Manage, select Authentication.

* In the implicit grant and hybrid flows section, select ID tokens, and then select save.

* Under Manager, select Certificates & Secrets.

* Select +New client secret, enter a description, select secret expiry date and select Add to generate a client secret. Copy the generate Client secret value as it can't be retrieved after this point.

* On the Overview page, also copy the client id (Application (client) ID) and tenant id (Directory (tenant) ID).

* **Replace in this endpoint `https://login.microsoftonline.com/{tenant id}/v2.0` to form the Authority credential.**

### LDAP

A sample LDAP yml configuration file which should allow for use of LDAP for user authentication can be found in the top level of the github repository.
However as there are various types of LDAP service available this may require some modification based on the type in use. For additional help with configuration,
please contact [support@ccdc.cam.ac.uk](mailto:support@ccdc.cam.ac.uk) for assistance but please note that due to the breadth of potential systems that could be used,
the help we can offer for setup may be limited.

### Google

Sign in as a [Google developer](http://console.developers.google.com/).

* From the Credentials tab, select Create Project, provide a project name (could be name of your organization), select a parent organization, and click Create.

* Select Configure Consent Screen to configure the OAuth consent screen with information about the application to be registered.

* On the OAuth Consent Screen, select the Internal user type and click Create.

* Complete the customization options as desired and select Back to Dashboard on the summary page.

* Return to the Credentials tab and select +Create Credentials. Choose OAuth client ID.

* Select Web Application for the Application Type.

* Enter WebCSD as the client name and select Create.

* Copy the generated Credentials and/or download the generated JSON file which includes all the necessary credentials.

* **Use `https://accounts.google.com/` as the Authority and `/signin-google` as CallbackPath**

## Enabling single sign-on

Before proceeding with this section, you must have obtained the required credentials: Authority, ClientId and ClientSecret.

Update the environment section of the **docker-compose.enable-sso.yml** file with the credentials.

```sh
services:
  ccdc-identity:
    environment:
      - OnSiteLoginSetup__EnableSingleSignOn=true
      - OnSiteLoginSetup__EnableFormBasedLogin=true
      - ThirdPartyCredentials__CallbackPath=/Identity/signin-oidc
      
      # Single Sign On Configuration
      # The following configuration is required to enable single sign on

      # The authority URL of the identity provider
      - ThirdPartyCredentials__Authority=https://login.microsoftonline.com/{Directory (tenant) ID}/v2.0
      # The client id of the application registered with the identity provider
      - ThirdPartyCredentials__ClientId={Application (client) ID}
      # The client secret of the application registered with the identity provider
      - ThirdPartyCredentials__ClientSecret={Client Secret Value}
      # The display name of the identity provider
      - ThirdPartyCredentials__DisplayName={Name of SSO provider. Eg. Microsoft}
```

**Note: Admin can disable local authentication and require users to strictly sign in with only SSO by setting the OnSiteLoginSetup__EnableFormBasedLogin variable to false**

Finally, include the docker-compose.enable-sso.yml in the start-up command:

```sh
docker-compose -f docker-compose.yml -f docker-compose.enable-sso.yml up -d
```

## Using single sign-on (All users)

### For Existing Users

Users who already have a local account would need to first link their local account to their third party account (Microsoft, Google, etc.) in their profile
before using the sign-in button on the login page. This is necessary to avoid changes in user roles and permissions or loss of user’s data.

First, sign in with your local account.

* Navigate to the My Profile area

* Select External Logins

* Select the configured SSO provider (Microsoft, Google, etc.) to link your local account. You’ll be redirected to the provider’s page for sign in and authorisation.

* If login succeeds, you’ll be redirected back to the application and be notified that the accounts have been linked successfully.

![image](https://github.com/ccdc-opensource/on-site-webcsd/assets/84066349/974d6d58-ead0-4be9-a63f-d89a9718d744)

**Info: This process is only required on first login with SSO. Subsequent logins can be done straight away from the login page by clicking on the Continue with**

### For New Users

From the Log in page, click Continue with you’ll be redirected to the SSO provider to sign in (if you’re not already signed in).

On successful authentication, The Identity Gateway will initiate an account registration process which will create a local profile and link it with the third party account you just signed into.

You will be presented with the Register page as shown below. Enter your email in the space provided if it was not pre-populated, and click Register.

![image](https://github.com/ccdc-opensource/on-site-webcsd/assets/84066349/c2eaed5c-c5f2-48fd-af01-f0cf5f6f80d5)

You can now proceed to login.

### Disabled Local Authentication

Your site admin, based on company policy, may decide to restrict users to sign in to the application using only SSO, and hence can disable the option to sign in locally with a username and password.

## Changes from the 4.3.0 release

For earlier versions of the On-Site platform it was possible to disable all user access control. This ability has now been removed due to additional functionality granted to users with administrator rights.
