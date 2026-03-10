The high availability architecture of the management platform primarily improves platform availability from the following dimensions:

-  primary/standby deployment: primary/standby nodes are deployed on different servers to avoid single points of failure.
-  primary/standby synchronization: The primary/standby nodes achieve configuration file synchronization with the backend YashanDB database to ensure data consistency between primary/standby nodes (monitoring data is independent). The configuration files include:
  - prometheus configuration files prometheus.yml, rules.yml
  - yashandb_exporter configuration file yasexporter.targets.yml
  - Secret files grpc.crt, grpc.key
  - Managed database password file yasdbpasswd.yml
  - Managed database configuration parameter file database_options.json
  - Custom alarm notification scripts
-  primary/standby switching: After the primary node fails, if the heartbeat to the standby node times out after 30 seconds, the standby node will promote itself to primary and continue to provide services externally, achieving automatic switching in case of a fault. However, after the switch, the URL of the management platform Web interface will change, requiring re-login to the platform. Users can log in using any standby node access address, and the login will automatically redirect to the new primary node login page.
-  Backend database high availability: The management platform's high availability architecture employs YashanDB as the backend database, requiring at least three instances of the backend YashanDB + enabling leader election + using maximize protection mode. If a fault occurs in the backend YashanDB, the management platform can switch the connection to the backend database based on YashanDB's high availability mechanism (user is unaware).