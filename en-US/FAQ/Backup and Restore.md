## Platform Backup and Recovery

An error occurred during the platform backup as follows:

```bash
backup ycm, tag: 20240822-172541...
[backup db] exec yasbak run backup database
cluster: YCM_YASDB primary node not be found
[full backup yashandb] check code failed, ret=1
```

Due to the existence of dirty data from historical backups, a full backup must be performed again. Execute the method to clean historical backups: `yasbak clean -c YCM_YASDB --purge`.