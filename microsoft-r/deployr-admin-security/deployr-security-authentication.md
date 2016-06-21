---

# required metadata
title: "Authentication and Authorization for DeployR"
description: "Security in DeployR: Authentication, HTTPS, SSL, and access controls for server, Project file and Repository File, and more."
keywords: ""
author: "j-martens"
manager: "Paulette.McKay"
ms.date: "05/16/2016"
ms.topic: "article"
ms.prod: "microsoft-r"
ms.service: ""
ms.assetid: ""

# optional metadata
ROBOTS: ""
audience: ""
ms.devlang: ""
ms.reviewer: ""
ms.suite: ""
ms.tgt_pltfrm: ""
ms.technology: "deployr"
ms.custom: ""

---

# Authentication and Authorization

DeployR ships with security providers for the following enterprise security solutions:

-   [Basic Authentication](#basic-authentication)
-   [LDAP Authentication](#ldap-authentication)
-   [Active Directory Services](#active-directory-authentication)
-   [PAM Authentication Services](#pam-authentication)
-   [CA Single Sign-On](#ca-single-sign-on-siteminder-pre-authentication)
-   [R Session Process Controls](#r-session-process-controls)

The DeployR security model is sufficiently flexible that it can work with multiple enterprise security solutions at the same time. If two or more enterprise security solutions are active, then user credentials are evaluated by each of the DeployR security providers in the order indicated in preceding list. If a security provider, at any depth in the provider-chain, establishes that the credentials are valid, then the login call succeeds. If the user credentials are not validated by any of the security providers in the provider-chain, then the login call fails.

When DeployR processes a user login, there are two key steps involved:

1.  Credentials must be authenticated
2.  Access privileges must be determined

DeployR access privileges are determined by the roles assigned to a user. In the case of basic authentication, an administrator simply assigns roles to a user within the DeployR Administration Console.

>**Learn More!** For information on how to manage user accounts as well as how to use roles as a means to assign access privileges to a user or to restrict access to individual R scripts, refer to the [Administration Console Help](../deployr-admin-console/deployr-admin-console-about.md).

When you integrate with an external enterprise security solution, you want access privileges to be inherited from the external system. This is achieved with simple mappings in the DeployR configuration properties, which link external groups to internal roles.

## Basic Authentication

By default, the Basic Authentication security provider is enabled. The Basic Authentication provider is always enabled and there are no additional security configuration properties for this provider.

    /*
     * DeployR Basic Authentication Policy Properties
     */

## LDAP Authentication

By default, the **LDAP** security provider is disabled. To enable LDAP authentication support, you must update the relevant properties in your DeployR external configuration file. The values you assign to these properties should match the configuration of your LDAP Directory Information Tree (DIT).

**To enable LDAP:** 

The LDAP and Active Directory security providers are one and the same, but their [configuration properties](#ldap-active-directory-configuration-properties) differ. As such, you may enable the LDAP provider or the Active Directory provider, but not both at the same time.
Accordingly, to enable do the following:

1. Set `grails.plugin.springsecurity.ldap.active=true`
2. Uncomment the LDAP properties  (make sure Active Directory  is commented out)
3. For each property, use the value matching your LDAP DIT:

```
    /*
     * DeployR LDAP Authentication Configuration Properties
     *
     * To enable LDAP authentication uncomment the following properties and
     * set grails.plugin.springsecurity.ldap.active=true and
     * provide property values matching your LDAP DIT:
     */
     grails.plugin.springsecurity.ldap.active=false
     /*
    grails.plugin.springsecurity.ldap.context.managerDn = 'dc=example,dc=com'
    grails.plugin.springsecurity.ldap.context.managerPassword = 'secret'
    grails.plugin.springsecurity.ldap.context.server = 'ldap://localhost:389/'
    grails.plugin.springsecurity.ldap.context.anonymousReadOnly = true
    grails.plugin.springsecurity.ldap.search.base = 'ou=people,dc=example,dc=com'
    grails.plugin.springsecurity.ldap.search.searchSubtree = true
    grails.plugin.springsecurity.ldap.authorities.groupSearchFilter = 'member={0}'
    grails.plugin.springsecurity.ldap.authorities.retrieveGroupRoles = true
    grails.plugin.springsecurity.ldap.authorities.groupSearchBase = 'ou=group,dc=example,dc=com'
    grails.plugin.springsecurity.ldap.authorities.defaultRole = "ROLE_BASIC_USER"

    // Optionally, specify LDAP password encryption algorithm: MD5, SHA-256
    // grails.plugin.springsecurity.password.algorithm = 'xxx'

    // deployr.security.ldap.user.properties.map
    // Allows you to map LDAP user property names to DeployR user property names:
    deployr.security.ldap.user.properties.map = ['displayName':'cn',
                                                 'email':'mail',
                                                 'uid' : 'uidNumber',
                                                 'gid' : 'gidNumber']

    // deployr.security.ldap.roles.map property
    // Allows you to map between LDAP group names to DeployR role names.
    // NOTE, while LDAP group names can be defined on the LDAP server using
    // any mix of upper and lower case, such as finance, Finance or FINANCE,
    // the LDAP group names that appear in the map must have ROLE_ appended
    // and be capitialized. For example, an LDAP group named "finance" should
    // appear in the map as ROLE_FINANCE. DeployR role names must
    // begin with ROLE_ and must always be upper case.
    deployr.security.ldap.roles.map = ['ROLE_FINANCE':'ROLE_BASIC_USER',
                                       'ROLE_ENGINEERING':'ROLE_POWER_USER']
    */
```

For more information, see the complete list of LDAP [configuration properties](#ldap-active-directory-configuration-properties).

>If you have enabled PAM authentication as part of the required steps for enabling R Session Process Controls, then please continue with your configuration using [these steps](#r-session-process-controls).

## Active Directory Authentication

By default, the Active Directory security provider is disabled. To enable Active Directory authentication support you must update the relevant properties in your DeployR external configuration file. The values you assign to these properties should match the configuration of your Active Directory Directory Information Tree (DIT).

**To enable Active Directory:** 

The LDAP and Active Directory security providers are one and the same, but their [configuration properties](#ldap-active-directory-configuration-properties) differ. As such, you may enable the LDAP provider or the Active Directory provider, but not both at the same time.
Accordingly, to enable do the following:

1. Set `grails.plugin.springsecurity.ldap.active=true`
2. Uncomment only the Active Directory properties (make sure LDAP is commented out)
3. For each property, use the value matching your configuration:

   + For DeployR for Microsoft R Server 2016:
     ```
     /*
      * DeployR Active Directory Configuration Properties
      *
      * To enable Active Directory uncomment the following
      * properties and provide property values matching your LDAP DIT:
      */

      grails.plugin.springsecurity.ldap.active=false
      /*
      grails.plugin.springsecurity.providerNames = ['ldapAuthProvider1']
      grails.plugin.springsecurity.ldap.server = 'ldap://localhost:389/'
      grails.plugin.springsecurity.ldap.domain = "mydomain.com"
 
      grails.plugin.springsecurity.ldap.authorities.groupSearchFilter = 'member={0}'
      grails.plugin.springsecurity.ldap.authorities.groupSearchBase = 'ou=group,dc=example,dc=com'
      grails.plugin.springsecurity.ldap.authorities.retrieveGroupRoles = true
  
      // Optionally, specify LDAP password encryption algorithm: MD5, SHA-256
      // grails.plugin.springsecurity.password.algorithm = 'xxx'
 
      // deployr.security.ldap.user.properties.map
      // Allows you to map LDAP user property names to DeployR user property names:
      deployr.security.ldap.user.properties.map = ['displayName':'cn',
                                                   'email':'mail',
                                                   'uid' : 'uidNumber',
                                                   'gid' : 'gidNumber']
  
      // deployr.security.ldap.roles.map property
      // Allows you to map between LDAP group names to DeployR role names.
      // NOTE, LDAP group names can be defined on the LDAP server using
      // any mix of upper and lower case, such as finance, Finance or FINANCE,
      // For example, an LDAP group named "finance" should
      // appear in the map fonance. DeployR role names must
      // begin with ROLE_ and must always be upper case.
      deployr.security.ldap.roles.map = ['FINANCE':'ROLE_BASIC_USER',
                                         'Engineering':'ROLE_POWER_USER']
 
      */
      ```

   + For DeployR 8.0.0
     ```
     /*
      * DeployR Active Directory Configuration Properties
      */
     
     grails.plugin.springsecurity.ldap.context.managerDn = 'dc=example,dc=com'
     grails.plugin.springsecurity.ldap.context.managerPassword = 'secret'
     grails.plugin.springsecurity.ldap.context.server = 'ldap://locahost:10389/'
     grails.plugin.springsecurity.ldap.authorities.ignorePartialResultException = true
     grails.plugin.springsecurity.ldap.search.base = 'ou=people,dc=example,dc=com'
     grails.plugin.springsecurity.ldap.search.searchSubtree = true
     grails.plugin.springsecurity.ldap.search.attributesToReturn = ['mail', 'displayName'] 
     grails.plugin.springsecurity.ldap.search.filter="sAMAccountName={0}"
     grails.plugin.springsecurity.ldap.auth.hideUserNotFoundExceptions = false
     grails.plugin.springsecurity.ldap.authorities.retrieveGroupRoles = true
     grails.plugin.springsecurity.ldap.authorities.groupSearchFilter = 'member={0}'
     grails.plugin.springsecurity.ldap.authorities.groupSearchBase = 'ou=group,dc=example,dc=com'
     
     // Optionally, specify LDAP password encryption algorithm: MD5, SHA-256
     // grails.plugin.springsecurity.password.algorithm = 'xxx'
     
     // deployr.security.ldap.user.properties.map
     // Allows you to map between LDAP user property names to DeployR user property names:
     deployr.security.ldap.user.properties.map = ['displayName':'cn',
                                                  'email':'mail',
                                                  'uid' : 'uidNumber',
                                                  'gid' : 'gidNumber']
    
     // deployr.security.ldap.roles.map property
     // Allows you to map between LDAP group names to DeployR role names.
     // NOTE, while LDAP group names can be defined on the LDAP server using
     // any mix of upper and lower case, such as finance, Finance or FINANCE,
     // the LDAP group names that appear in the map must have ROLE_ appended
     // and be capitialized. For example, an LDAP group named "finance" should
     // appear in the map as ROLE_FINANCE. DeployR role names must
     // begin with ROLE_ and must always be upper case.
     deployr.security.ldap.roles.map = ['ROLE_FINANCE':'ROLE_BASIC_USER',
                                        'ROLE_ENGINEERING':'ROLE_POWER_USER']
     ```

For more information, see the complete list of [configuration properties](#ldap-active-directory-configuration-properties).

## LDAP & Active Directory Configuration Properties

The following table presents the complete list of LDAP and Active Directory configuration properties.

>To use one of these configuration properties in the `deployr.groovy` external configuration file, you must prefix the property name with `grails.plugin.springsecurity`. For example, to use the `ldap.context.server='ldap://localhost:389'` property in `deployr.groovy`, you must write the property as such: `grails.plugin.springsecurity.ldap.context.server='ldap://localhost:389'`

<sup>\*</sup> These properties are for Active Directory.

**Context Properties**

| Property                                | Default Value                  | Description                                                                                                   |
|-----------------------------------------|--------------------------------|---------------------------------------------------------------------------------------------------------------|
| providerNames<sup>\*</sup>              | 'ldapAuthProvider1'            | Do not change or omit. Used for password management.                                                          |
| ldap.server<sup>\*</sup>                | 'ldap://localhost:389'         | Address of the Active Directory server.                                                                       |
| ldap.context.server                     | 'ldap://localhost:389'         | Address of the LDAP server.                                                                                   |
| ldap.context.managerDn                  | "'cn=admin,dc=example,dc=com'" | DN to authenticate with.                                                                                      |
| ldap.context.managerPassword            | secret'                        | Manager password to authenticate with.                                                                        |
| ldap.context.baseEnvironmentProperties  | None                           | Extra context properties.                                                                                     |
| ldap.context.cacheEnvironmentProperties | TRUE                           | Whether environment properties should be cached between requests.                                             |
| ldap.context.anonymousReadOnly          | FALSE                          | Whether an anonymous environment should be used for read-only operations.                                     |
| ldap.context.referral                   | null ('ignore')                | The method to handle referrals. Can be 'ignore' or 'follow' to enable referrals to be automatically followed. |

**Search Properties**

| Property                              | Default Value                  | Description                                                                                                                                         |
|---------------------------------------|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|
| ldap.search.base                      | "'ou=users,dc=example,dc=com'" | "Context name to search in, relative to the base of the configured ContextSource, e.g. 'ou=users,dc=example,dc=com'."                               |
| ldap.search.searchSubtree             | TRUE                           | "If true then searches the entire subtree as identified by context, if false (the default) then only searches the level identified by the context." |
| ldap.search.derefLink                 | FALSE                          | Enables/disables link dereferencing during the search.                                                                                              |
| ldap.search.timeLimit                 | 0 (unlimited)                  | The time to wait before the search fails.                                                                                                           |
| ldap.search.attributesToReturn        | null (all)                     | The attributes to return as part of the search.                                                                                                     |
| ldap.authenticator.dnPatterns         | null (none)                    | "Optional pattern(s) used to create DN search patterns, e.g. \[""cn={0},ou=people""\]."                                                             |
| ldap.authenticator.attributesToReturn | null (all)                     | Names of attribute ids to return; use null to return all and an empty list to return none.                                                          |

**Authorities Properties**

| Property                                         | Default Value                   | Description                                                                                  |
|--------------------------------------------------|---------------------------------|----------------------------------------------------------------------------------------------|
| ldap.authorities.retrieveGroupRoles<sup>\*</sup> | TRUE                            | Whether to infer roles based on group membership.                                            |
| ldap.authorities.retrieveDatabaseRoles           | FALSE                           | Whether to retrieve additional roles from the database using the User/Role many-to-many.     |
| ldap.authorities.groupRoleAttribute              | 'cn'                            | The ID of the attribute which contains the role name for a group.                            |
| ldap.authorities.groupSearchFilter<sup>\*</sup>  | 'uniquemember={0}'              | The pattern to be used for the user search. {0} is the user's DN.                            |
| ldap.authorities.searchSubtree<sup>\*</sup>      | TRUE                            | "If true a subtree scope search will be performed, otherwise a single-level search is used." |
| ldap.authorities.groupSearchBase<sup>\*</sup>    | "'ou=groups,dc=example,dc=com'" | The base DN from which the search for group membership should be performed.                  |
| ldap.authorities.defaultRole                     | None                            | An optional default role to be assigned to all users.                                        |
| ldap.mapper.roleAttributes                       | Null                            | Optional names of role attributes.                                                           |
| ldap.mapper.convertToUpperCase                   | TRUE                            | "Whether to uppercase retrieved role names (will also be prefixed with ""ROLE\_"")"          |


## PAM Authentication

By default, the **PAM** security provider is disabled. To enable PAM authentication support, you must:

1.  Update the relevant properties in your DeployR external configuration file, deployr.groovy.
2.  Follow the DeployR server system files configuration changes outlined below.

PAM is the Linux Pluggable Authentication Modules provided to support dynamic authorization for applications and services in a Linux system. If DeployR is installed on a Linux system, then the PAM security provider allows users to authenticate with DeployR using their existing Linux system username and password.

**Step 1: Update the DeployR external configuration file**

Update the following properties in your DeployR external configuration file, `deployr.groovy`:

    -   deployr.security.pam.authentication.enabled
    -   deployr.security.pam.groups.map
    -   deployr.security.pam.default.role

Relevant snippet from `deployr.groovy` file shown here:
```
/*
* DeployR PAM Authentication Policy Properties
*/

deployr.security.pam.authentication.enabled = false

// deployr.security.pam.groups.map
// Allows you to map PAM user group names to DeployR role names.
// NOTE: PAM group names are case sensitive. For example, your
// PAM group named "finance" must appear in the map as "finance".
// DeployR role names must begin with ROLE_ and must always be
// upper case.
deployr.security.pam.groups.map = [ 'finance' : 'ROLE_BASIC_USER',
                                   'engineering' : 'ROLE_POWER_USER' ]

// deployr.security.pam.default.role
// Optional, grant default DeployR Role to all PAM authenticated users:
deployr.security.pam.default.role = 'ROLE_BASIC_USER'
```

**Step 2: Update the DeployR server system files configuration changes**

1.  Before making any configuration changes to the server system files, stop the DeployR server:

    1. Launch the DeployR administrator utility script with administrator privileges as `root` or a user with `sudo` permissions:
       + On Windows:
          ```
          cd C:\Program Files\Microsoft\DeployR-8.0.5\deployr\tools\ 
          adminUtility.bat
          ```
    
       + On Linux:
          ```
          cd /home/deployr-user/deployr/8.0.5/deployr/tools/  
          ./adminUtility.sh
          ```

    1.  Choose option **Start/Stop Server**.

    1.  Enter `S` to stop the server. It may take some time for the Tomcat process to terminate.

1. Grant permissions to launch the Tomcat server. This is required so the DeployR server can avail of PAM authentication services.
    
   + For **non-root installs** of DeployR:  The following steps grant `deployr-user` permission to execute just one command as a `sudo` user, which launches the Tomcat server. 

     1. Log in as `root` your the DeployR server.

     1. In the file `/etc/sudoers`, find the following section:
        ```
        ## Command Aliases
        ```
        
     1. Add the following line to this section:
        ```
        Cmnd_Alias DEPLOYRTOMCAT = /home/deployr-user/deployr/8.0.5/tomcat/tomcat7.sh
        ```
	
     1. Find the following section:
        ```
        ## Allow root to run any commands anywhere
        ```

     1. Add the following line to this section:
        ```
        %deployr-user      ALL = DEPLOYRTOMCAT
        ```
        
        Your file should now look like this, where the order is important:

        ```
        root    ALL=(ALL)       ALL
        %deployr-user   ALL = DEPLOYRTOMCAT
        ```
        
     1. Save these changes and close the file in your editor.

     1. Log out `root` from your DeployR server.

     1. Log in as `deployr-user` to your DeployR server.
     
     1. Update the DeployR start shell script, `/home/deployr-user/deployr/8.0.5/startAll.sh`, to take advantage of the `sudo` command configured above.

        + Find the following line:

          ```
          /home/deployr-user/deployr/8.0.5/tomcat/tomcat7.sh start
          ```

        + Change it to the following:

          ```
          sudo /home/deployr-user/deployr/8.0.5/tomcat/tomcat7.sh start
          ```

        + Save this change and close the file in your editor.

     1. Update the DeployR `/home/deployr-user/deployr/8.0.5/stopAll.sh` shell script to take advantage of the `sudo` command configured above.
    
        + Find the following line:
          
          ```
          /home/deployr-user/deployr/8.0.5/tomcat/tomcat7.sh start
          ```
          
        + Change it to the following:

          ```
          sudo /home/deployr-user/deployr/8.0.5/tomcat/tomcat7.sh start
          ```
          
        + Save this change and close the file in your editor.

   + For **root installs** of DeployR:  The following steps grant `root` permission to launch the Tomcat server. 

     1.  Log in as `root` on your DeployR server.

     2.  Using your preferred editor, edit the file `/opt/deployr/8.0.5/tomcat/tomcat7.sh`,

     3. Find the following section:

        - On Redhat/CentOS platforms:
          ```
          daemon --user "apache" ${START_TOMCAT}
          ```

        - On SLES platforms:
          ```
          start_daemon -u r "apache" ${START_TOMCAT}
          ```

     4. Change `"apache"` to `"root"` as follows:

        - On Redhat/CentOS platforms:
          ```
          daemon --user "root" ${START_TOMCAT}
          ```

        - On SLES platforms:
          ```
          start_daemon -u r "root" ${START_TOMCAT}
          ```

     5.  Save this change and close the file in your editor.

1. Restart the server. 
   1. Launch the DeployR administrator utility script with administrator privileges, `root` or a user with `sudo` permissions:
       + On Windows:
          ```
          cd C:\Program Files\Microsoft\DeployR-8.0.5\deployr\tools\ 
          adminUtility.bat
          ```
    
       + On Linux:
          ```
          cd /home/deployr-user/deployr/8.0.5/deployr/tools/  
          ./adminUtility.sh
          ```

   1. Choose option **Start/Stop Server**.

   1. Enter `R` to restart the server. It may take some time for the Tomcat process to terminate and restart.

   1. Save this change and close the file in your editor.


>If you have enabled PAM authentication as part of the required steps for enabling R Session Process Controls, then please continue with your configuration using [these steps](#r-session-process-controls).



## CA Single Sign-On (SiteMinder) Pre-Authentication

By default, the **CA Single Sign-On** (formerly known as SiteMinder) security provider is disabled. To enable CA Single Sign-On support, you must first update CA Single Sign-On Policy Server configuration. Then, you must update the relevant properties in your DeployR external configuration file.

**To enable CA Single Sign-On support:**

1.  Define or update your CA Single Sign-On Policy Server configuration. For details on how to do this, [read here](../deployr-admin-configure-ca-sso.md).

2.  Update the relevant properties in your DeployR external configuration file.
    This step assumes that:

    -   Your CA Single Sign-On Policy Server is properly configured and running
    -   You understand which header files are being used by your policy server

```
/*
* Siteminder Single Sign-On (Pre-Authentication) Policy Properties
*/

deployr.security.siteminder.preauth.enabled = false

// deployr.security.preauth.username.header
// Identify Siteminder username header, defaults to HTTP_SM_USER as used by Siteminder Tomcat 7 Agent.
deployr.security.preauth.username.header = 'HTTP_SM_USER'

// deployr.security.preauth.group.header
// Identify Siteminder groups header.
deployr.security.preauth.group.header = 'SM_USER_GROUP'

// deployr.security.preauth.group.separator
// Identify Siteminder groups delimiter header.
deployr.security.preauth.group.separator = '^'

// deployr.security.preauth.groups.map
// Allows you to map Siteminder group names to DeployR role names.
// NOTE: Siteminder group names must be defined using the distinguished
// name for the group. Group distinguished names are case sensitive.
// For example, your Siteminder distinguished group name
// "CN=finance,OU=company,DC=acme,DC=com" must appear in the map as
// "CN=finance,OU=company,DC=acme,DC=com". DeployR role names must
// begin with ROLE_ and must always be upper case.
deployr.security.preauth.groups.map = [ 'CN=finance,OU=company,DC=acme,DC=com' : 'ROLE_BASIC_USER',
                                  'CN=engineering,OU=company,DC=acme,DC=com' : 'ROLE_POWER_USER' ]
			
// deployr.security.preauth.default.role
// Optional, grant default DeployR Role to all Siteminder authenticated users:
deployr.security.preauth.default.role = 'ROLE_BASIC_USER'
```








## R Session Process Controls

By default, R sessions executing on the DeployR grid are not authorized to access files or directories outside of the R working directory. To enable broader file system access for a given R session to files or directories based on specific authenticated user ID and group ID credentials, you must first do ONE of the following:

-   Enable [PAM authentication](#pam-authentication), or
-   Enable [LDAP authentication](#ldap-authentication), or
-   Enable [Active Directory authentication](#active-directory-authentication)

Once you have enabled PAM, LDAP, or AD authentication, you must (Step 1) update the relevant process controls properties on the server **and then** (Step 2) apply system-level configuration changes to every single node on your DeployR grid, as follows:

### Step 1: Update Process Control Properties

After you've enabled either PAM, LDAP, or Active Directory authentication, you can update the relevant process control properties in your DeployR server external configuration file, `deployr.groovy`, on the main DeployR server:

-   deployr.security.r.session.process.controls.enabled
-   deployr.security.r.session.process.default.uid
-   deployr.security.r.session.process.default.gid

Relevant snippet from deployr.groovy file shown here:

        /*
        * DeployR R Session Process Controls Policy Configuration
        *
        * By default, each R session on the DeployR grid executes
        * with permissions inherited from the master RServe process
        * that was responsible for launching it.
        *
        * Enable deployr.security.r.session.process.controls to force
        * each individual R session process to execute with the
        * permissions of the end-user requesting the R session,
        * using their authenticated user ID and group ID.
        *
        * If this property is enabled but the uid/gid associated with
        * the end-user requesting the R session is not available,
        * then the R session process will execute using the default[uid,gid]
        * indicated on the following properties.
        *
        * The default[uid,gid] values indicated on these policy
        * configuration properties must correspond to a valid user and
        * group ID configured on each node on the DeployR grid.
        *
        * For example, if you installed DeployR as deployr-user then
        * we recommend using the uid/gid for deployr-user for these
        * default property values.
        */
        deployr.security.r.session.process.controls.enabled=false
        deployr.security.r.session.process.default.uid=9999
        deployr.security.r.session.process.default.gid=9999

### Step 2: Make System-Level Configuration Changes to Every Node

> **Before You Begin!** Make sure you've enabled the appropriate process control properties before beginning this step.

**For Non-Root Installs**

>Apply the following configuration changes on **each and every node** on your DeployR grid, including the default grid node.

On each machine hosting a grid node:

1.  Before making any configuration changes to system files, you must stop Rserve and any other DeployR-related services:

        cd /home/deployr-user/deployr/8.0.5
        ./stopAll.sh

2.  Grant `deployr-user` permission to execute a command as a `sudo` user so that the RServe process can be launched. This is required so that the DeployR server can enforce R session process controls.

    A. Log in as `root`.

    B. Using your preferred editor, edit the file:

        /etc/sudoers

    C. Find the following section:

        ## Command Aliases

    D. Add the following line to this section:

        Cmnd_Alias DEPLOYRRSERVE = /home/deployr-user/deployr/8.0.5/rserve/rserve.sh

    E. Find the following section:

        ## Allow root to run any commands anywhere

    F. Add or append `DEPLOYRRSERVE` for `%deployr-user` to this section:

        ## If an entry for %deployr-user is not found, add this line:
        %deployr-user      ALL = DEPLOYRRSERVE
        ## Otherwise append as shown:
        %deployr-user      ALL = DEPLOYRTOMCAT,DEPLOYRRSERVE

    G. Save these changes and close the file in your editor.

    H. Log out `root`.

3.  Update the DeployR `startAll.sh` shell script to take advantage of the `sudo` command configured above.

    A. Log in as `deployr-user`.

    B. Using your preferred editor, edit the file:

        /home/deployr-user/deployr/8.0.5/startAll.sh

    C. Find the following line:

        /home/deployr-user/deployr/8.0.5/rserve/rserve.sh start

    D. Change it to the following:

        sudo /home/deployr-user/deployr/8.0.5/rserve/rserve.sh start

    E. Save this change and close the file in your editor.

4.  Update the DeployR `stopAll.sh` shell script to take advantage of the `sudo` command configured above.

    A. Using your preferred editor, edit the file:

        /home/deployr-user/deployr/8.0.5/stopAll.sh

    B. Find the following line:

        /home/deployr-user/deployr/8.0.5/rserve/rserve.sh stop

    C. Change it to the following:

        sudo /home/deployr-user/deployr/8.0.5/rserve/rserve.sh stop

    D. Save this change and close the file in your editor.

5.  Set group privileges on the user directory containing the DeployR grid node install directory.

    A. Log in as `root`.

    B. Set group privileges.

         cd /home
         chmod -R g+rwx deployr-user

6.  Add each user that will authenticate with the server to the `deployr-user` group.

    A. Log in as `root`.

    B. Execute the following command to add each user to the `deployr-user` group.

        usermod -a -G deployr-user <some-username>

    C. Repeat step **B.** for each user that will authenticate with the server.

    D. Log out `root`.

7.  Restart Rserve and any other DeployR-related services on the machine hosting the DeployR grid node:

    A. Log in as `deployr-user`.

    B. Start Rserve and any other DeployR-related services:

        cd /home/deployr-user/deployr/8.0.5
        ./startAll.sh

1. Repeat these steps for each grid node.

**For Root Installs**

>Apply the following configuration changes on **each and every node** on your DeployR grid, including the default grid node.

On each machine hosting a grid node:

1.  Log in as `root`.

2.  Before making any configuration changes to system files, stop Rserve and any other DeployR-related services:

        cd /opt/deployr/8.0.5
        ./stopAll.sh

3.  Grant `root` permission to launch the RServe process. This is required so that each DeployR grid node can enforce R session process controls.

    A. Using your preferred editor, edit the file `/opt/deployr/8.0.5/rserve/rserve.sh` as follows:

    -   On Redhat/CentOS platforms, find the following section:

            daemon --user "apache"

        and, change `apache` to `root` as follows:

            daemon --user "root"

    -   On SLES platforms, find the following section:

            start_daemon -u r "apache"

        and, change `apache` to `root` as follows:

            start_daemon -u r "root"

    B. Save this change and close the file in your editor.

4.  Set group privileges on the DeployR install directory.

        cd /opt
        chown -R apache.apache deployr
        chmod -R g+rwx deployr

5.  Execute the following command to add each user to the `apache` group.
    **Repeat for each user that will authenticate with the server.**

        usermod -a -G apache <some-username>

6.  Restart Rserve and any other DeployR-related services:

        cd /home/deployr-user/deployr/8.0.5
        ./startAll.sh
        
1. Repeat these steps for each grid node.