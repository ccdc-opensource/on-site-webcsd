# Single Sign-On (SSO)

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

### Enabling single sign-on

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

**Note: Admin can disable local authentication and require users to strictly sign in with only SSO by setting the OnSiteLoginSetup__EnableFormBasedLogin variable to false.**

Finally, include the docker-compose.enable-sso.yml in the start-up command:

```sh
docker-compose -f docker-compose.yml -f docker-compose.enable-sso.yml up -d
```

## Using single sign-on (All users)

### For Existing Users

Users who already have a local account would need to first link their local account to their third party account (Microsoft, Google, etc.) in their profile
before using the sign-in button on the login page. This is necessary to avoid changes in user roles and permissions or loss of user’s data.

First, sign in with your local account.

* Navigate to the My Profile area.

* Select External Logins.

* Select the configured SSO provider (Microsoft, Google, etc.) to link your local account. You’ll be redirected to the provider’s page for sign in and authorisation.

* If login succeeds, you’ll be redirected back to the application and be notified that the accounts have been linked successfully.

![image](https://github.com/ccdc-opensource/on-site-webcsd/assets/84066349/974d6d58-ead0-4be9-a63f-d89a9718d744)

**Info: This process is only required on first login with SSO. Subsequent logins can be done straight away from the login page by clicking on the Continue with.**

### For New Users

From the Log in page, click Continue with you’ll be redirected to the SSO provider to sign in (if you’re not already signed in).

On successful authentication, the Identity Gateway will initiate an account registration process which will create a local profile and link it with the third party account you just signed into.

You will be presented with the Register page as shown below. Enter your email in the space provided if it was not pre-populated, and click Register.

![image](https://github.com/ccdc-opensource/on-site-webcsd/assets/84066349/c2eaed5c-c5f2-48fd-af01-f0cf5f6f80d5)

You can now proceed to login.
