In the management platform, system privilege management and constraints are carried out through three dimensions: user groups, roles, and users.

- [User Group](User Group Management) can classify and manage users, achieving fine-grained isolation and hosting resources for users in different groups, and managing the shared roles (privileges) for all users under it.

- [Role](Role Management), as the carrier of various operational privileges in the management platform, needs to be bound to a user group or a specific user to take effect. By binding appropriate roles to a user group or user, users are restricted to perform operations only within the scope of their own role privileges, which effectively avoids misoperations and other security risks.

- [User](User Management) is the actual user of the management platform. Based on their bound roles, users have different platform functionality usage privileges. The privileges of a user are the union of all privileges of their bound roles. The platform has a pre-set super administrator (admin) with all privileges of all resources and functionality modules in the platform.