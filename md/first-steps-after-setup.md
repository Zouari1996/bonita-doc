# Post-installation recommendations

This page describes the best practices and steps to follow in order to ensure the security of the platform after the initial installation.

## Check the installation

To verify that the installation was successful and the database is correctly configured, connect to Bonita Portal.

In the URL field of your web browser, enter the local host address, e.g. `http://localhost:8080/bonita`.

**Note:** If the Bonita Portal login page is not displayed, [empty your browser cache](http://www.wikihow.com/Clear-Your-Browser's-Cache) and [cookies](http://www.wikihow.com/Clear-Your-Browser%27s-Cookies) and then reload the page.

At this stage no organization information is loaded: only the technical user account exists.

## Security Best Practices

There are a number of ways you can improve the security of your platform. 

### When installing Bonita

#### Change default password for Technical User and System Administrator
System Administrator and Technical User accounts allow corresponding user(s) to completely configure Bonita. As for demo and ease of installation purposes, Bonita is delivered with default user and password for these two accounts. Such credentials are accessible in the documentation over the Internet. It's highly recommended to change such passwords and replace them with strong ones to better protect administration consoles. 

#### Use HTTPS for all communications
HTTPS helps with the protection of web application data from unauthorized disclosure and modification when it is transmitted between clients (web browsers) and the web application server. Session cookies, authentication credentials and any sensitive data  are considered as application data. Bonita already supports HTTPS functionality. To better protect your deployed application, it is highly recommend to enable HTTPS for all communication towards the web server. Please refer to [OWASP TLS Certificates](https://www.owasp.org/index.php/Transport_Layer_Protection_Cheat_Sheet#Server_Certificate) and [Bonita's SSL Documentation](ssl.md) to securely implement this functionality for your application.

#### Enforce strong secure password policies to Bonita users
A key security concern when using passwords for authentication is password strength. A "strong" password policy makes it difficult or even improbable for one to guess the password through either manual or automated means. So if you are using Bonita's default authentication mechanism, it is highly recommended that you apply the password policies and enforce the users to choose strong and secure passwords. Such functionality is already provided by Bonita (See [Strong Password Policies](enforce-password-policy.md)). It is recommended to activate this option in the deployed application to better protect your application and users.

#### Choose a strong password for your database
The database is at the heart of your application. It is where all entreprise data (sensitive or non-sensitive) is stored. As a result, the database username and password are of great value to the business and they need to be protected. It is highly recommended to choose strong, random and long passwords, since they make it difficult or even improbable for one to guess the password through either manual or automated means. 

#### Choose a strong password for your connectors
In Bonita, connectors enable connections to your local information system or online services. The authentication credentials used for these connections are of great value to the business and as a result, they need to be protected. It is highly recommended to choose strong, random and long passwords, as explained above, to makes it difficult or even improbable for one to guess the password. 

#### Limit the usage of Bonita REST API
If you want to restrict the authorization granted to the default profiles, it is highly recommended to activate the Dynamic authorization checking on the bonita REST APIs access (See [REST API Authorization](rest-api-authorization.md)). 
To do so: 
1. Use the setup tool to get the current configuration of Bonita Platform from the database: `setup.sh pull` (See [Setup tool](BonitaBPM_platform_setup.md))
2. Edit the file: `/setup/platform_conf/current/tenant_template_portal/security-config.properties`
3. Make sure this line is set to `true`: `security.rest.api.authorizations.check.enabled true`
4. Then edit the file: `/setup/platform_conf/current/tenants/<TENANT_ID>/tenant_portal/dynamic-permissions-checks-custom.properties`
5. Uncomment all the lines to restrict the authorization access on all the Bonita REST API (lines starting with `GET`, `PUT`, `POST`...).
i.e. to restrict the authorization on the instantiation and humanTask API, to only users that are allowed to them (meaning that they are: assigned or pending to, or processes that they deployed or that they supervised), uncomment:
```
GET|bpm/process/*/instantiation=[profile|Administrator, check|org.bonitasoft.permissions.ProcessInstantiationPermissionRule]
POST|bpm/process/*/instantiation=[profile|Administrator, check|org.bonitasoft.permissions.ProcessInstantiationPermissionRule]
```
And:
```
GET|bpm/humanTask=[profile|Administrator, check|org.bonitasoft.permissions.TaskPermissionRule]
PUT|bpm/humanTask=[profile|Administrator, check|org.bonitasoft.permissions.TaskPermissionRule]
```
6. Save the changes made in file  `/setup/platform_conf/current/tenants/<TENANT_ID>/tenant_portal/dynamic-permissions-checks-custom.properties`. 
7. Use the setup tool to push the current configuration of Bonita Platform from the database: `setup.sh push` (See [Setup tool](BonitaBPM_platform_setup.md))

For a maximum security, uncomment the entire file on production, so that the  REST API authorization are more restrictive. Then authorize the REST API access by commenting them out one by one as needed.

#### Deactivate or limit the usage of Bonita Engine API
If Bonita Engine API is of no use for your application, it is highly recommended to deactivate it in the configuration files in your deployment environment. Otherwise, its usage must be limited to administrators using a local connection or authorized IP addresses.

#### Protect your web server configuration file
The web server configuration (Tomcat, WildFly, etc) must be protected. This implies limiting access to the file so that it could be read only by the user that web server process runs as and root (or the administrator on Windows). It should be also noted that the file must be saved outside of web root directory (as an example, here are some security tips for Apache Tomcat: [Ref1](https://www.petefreitag.com/item/505.cfm), [Ref2](https://www.acunetix.com/blog/articles/10-tips-secure-apache-installation/))


### When developping using Bonita

#### Opt for a strong authentication mechanism
Authentication is the process of verification that an individual, entity or website is who it claims to be. Today authentication shall be one of the main functionalities of modern web applications. Bonita provides you with a wide range of authentication methods. However, for ease of use and deployment purposes, Bonita can be also configured with "No_Authentication" option. This is ONLY provided for users for whom Bonita does not support the authentication method in place. Other than this special case, "No_Authentication" option shall be always avoided and the users shall opt for a strong authentication option, e.g. SSO.

#### Perform input validation in your applications and extensions
Input validation is performed to ensure only properly formed data is entering the workflow in an information system, preventing malformed data from persisting in the database and triggering malfunction of various downstream components.If you use BonitaStudio for developing your forms, then define authorized min and max values, max length and type for input fields.

For more information, please take look at the [REST API authorization](rest-api-authorization.md) and the other features relating to [security and authentication](_security-and-authentication.md) that are available in Bonita and in your operating system, and update your platform as required for your production environment. 

## Create a Bonita Portal administrator

Create a user with the "administrator" profile:

Note: do not create a user or an administrator with the same login and password as the technical users (platform and tenant)

1. Log in to Bonita Portal as the technical user.  
**Note:** If your system is using single sign-on with CAS, you need to log in with the following URL: `http://`_`hostname:port`_`/bonita/login.jsp?redirectUrl=portal/homepage`.
2. Create a user with the standard profile.
3. Go to **Organization** \> **Profiles**. Select "Administrator" profile.
4. Click on the "More" button (in the top right corner).
5. Under "Users mapping", click on "Add a user".
6. Select your user and click on the "Add" button. Log out as the Technical user and log back in as the newly created user with administrative rights.
7. Create [users with the standard profile](manage-a-user.md).
8. You can add newly created users to the "User" (standard) profile or to a custom profile.

If you already have a system that stores information about end users, you can use it to create user accounts in Bonita.

If you use an LDAP or Active Directory system, you can use the [LDAP synchronizer](ldap-synchronizer.md) tool to keep the Bonita Portal organization synchronized with it.
