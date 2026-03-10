Basic operation and maintenance management includes instance management, configuration parameters, data space management, session management, privilege management, and database auditing.

- [Instance Management](Instance Management): View the server configuration of the instance, update database instance information, start or stop the instance, delete standby nodes, and other functionalities.
- [Configuration Parameters](Configuration Parameters): Provide functionalities for querying, modifying, or temporarily storing modifications of database parameters.
- [Data Space Management](Data Space Management): Support quick management of tablespaces and tablespace sets functionalities.
- [Session Management](Session Management): View information about currently connected sessions on the instance, delete and terminate user sessions functionalities.
- [Privilege Management](Auth Management): Support creating database users and roles.
- [Database Audit](Database Audit): Support creating audit policies to audit database behaviors.
- [Security Management](Security Management): Supports configuring SSL certificates in management platform to access YashanDB.

## Navigation Bar

The detail page provides a secondary navigation bar, allowing users to click each navigation item to perform corresponding management operations on YashanDB.

The primary navigation bar is divided into: Basic Information, Tenant Management, Database Management, and Diagnosis & Optimization.

- For **Standalone Deployment Databases**, the following functionality modules are provided:
  - Basic Information: Details, Alarm Monitoring, Topology Diagram, Configuration, Network Configuration, Security Management
  - Tenant Management: Tenant List, Tenant Monitor
  - Database Management: Data Space Management (Tablespaces), Privilege Management, Session Management, Backup Management, Reliability Solutions, Database Auditing
  - Diagnosis & Optimization: Performance Reports, Lock Management, Transaction Analysis, Slow SQL Analysis, Log Analysis, Database Logs

- For **Distributed Deployment Databases**, the following functionality modules are provided:
  - Basic Information: Details, Alarm Monitoring, Topology Diagram, Configuration, Network Configuration, Security Management
  - Database Management: Data Space Management (Tablespaces and Tablespace Sets), Privilege Management, Session Management, Backup Management, Reliability Solutions, Database Auditing
  - Diagnosis & Optimization: Lock Management, Transaction Analysis, Slow SQL Analysis, Log Analysis, Database Logs

- For **YAC Deployment Databases**, the following functionality modules are provided:
  - Basic Information: Details, Alarm Monitoring, Topology Diagram, Configuration, Network Configuration, Security Management
  - Database Management: Data Space Management (Tablespaces), Privilege Management, Session Management, Backup Management, Database Auditing
  - Diagnosis & Optimization: Performance Reports, Lock Management, Transaction Analysis, Slow SQL Analysis, Log Analysis, Database Logs

>**Note**:
>
> The Tenant Management primary navigation menu is only displayed when the database is in multi-tenant mode.

## Login

The management platform provides session-level user information saving functionality. It supports the following two login methods:

- Manual Login: Users can enter the database detail page, click **[Login]**, and input the username and password.
- Automatic Login: Users can save database accounts and passwords in [Database Password Management](../../Platform Management/Account Center/Resource Password Saved), then enable automatic login, allowing the management platform to facilitate seamless automatic login.

## Subscription

The management platform supports users subscribing to the database, and when the database triggers an alarm or resource change, it will push internal messages to notify users.