# PostgreSQL Configuration Guide for Ansible Automation Platform 2.4

This guide provides instructions for configuring and optimizing PostgreSQL for a single-node Ansible Automation Platform 2.4 installation. While the AAP installer sets up a basic PostgreSQL installation, additional configuration can improve performance and reliability.

## Overview

In a single-node AAP setup, PostgreSQL serves as the database backend for both:
- Automation Controller (using the `awx` database)
- Private Automation Hub (using the `automationhub` database)

## Prerequisites

- Ansible Automation Platform 2.4 installed according to the [AAP Installation Guide](aap_installation_guide.md)
- Root or sudo access to the AAP server

## Verifying PostgreSQL Installation

1. **Check PostgreSQL Service Status**
   ```bash
   sudo systemctl status postgresql
   ```

2. **Verify PostgreSQL Version**
   ```bash
   sudo -u postgres psql -c "SELECT version();"
   ```
   The output should show PostgreSQL 13.x.

3. **List Databases**
   ```bash
   sudo -u postgres psql -c "\l"
   ```
   You should see both the `awx` and `automationhub` databases.

## Basic PostgreSQL Configuration

The AAP installer configures PostgreSQL with default settings. Here's how to view and modify the configuration:

1. **Locate PostgreSQL Configuration Files**
   ```bash
   sudo find /var/lib/pgsql -name "postgresql.conf"
   ```
   Typically located at `/var/lib/pgsql/13/data/postgresql.conf`

2. **Backup the Configuration File**
   ```bash
   sudo cp /var/lib/pgsql/13/data/postgresql.conf /var/lib/pgsql/13/data/postgresql.conf.bak
   ```

3. **Edit the Configuration File**
   ```bash
   sudo vi /var/lib/pgsql/13/data/postgresql.conf
   ```

## Performance Optimization

For a single-node AAP setup, consider the following optimizations:

1. **Memory Settings**
   ```
   # Memory settings
   shared_buffers = 4GB                  # 25% of available RAM for dedicated server
   work_mem = 32MB                       # Depends on max_connections and complexity of queries
   maintenance_work_mem = 512MB          # For maintenance operations
   effective_cache_size = 12GB           # 75% of available RAM for dedicated server
   ```

2. **Connection Settings**
   ```
   # Connection settings
   max_connections = 200                 # Depends on your workload
   ```

3. **Write-Ahead Log (WAL) Settings**
   ```
   # WAL settings
   wal_level = replica                   # Minimum for replication
   max_wal_size = 2GB
   min_wal_size = 1GB
   checkpoint_completion_target = 0.9    # Spread out checkpoint I/O
   ```

4. **Background Writer Settings**
   ```
   # Background writer settings
   bgwriter_delay = 200ms
   bgwriter_lru_maxpages = 100
   bgwriter_lru_multiplier = 2.0
   ```

5. **Apply the Changes**
   ```bash
   sudo systemctl restart postgresql
   ```

## Security Hardening

1. **Configure Client Authentication**
   
   Edit the `pg_hba.conf` file:
   ```bash
   sudo vi /var/lib/pgsql/13/data/pg_hba.conf
   ```
   
   Ensure it contains appropriate restrictions:
   ```
   # TYPE  DATABASE        USER            ADDRESS                 METHOD
   local   all             postgres                                peer
   host    all             all             127.0.0.1/32            md5
   host    all             all             ::1/128                 md5
   host    awx             awx             localhost               md5
   host    automationhub   automationhub   localhost               md5
   ```

2. **Apply the Changes**
   ```bash
   sudo systemctl restart postgresql
   ```

## Backup and Recovery

1. **Create a Backup Script**
   
   Create a file named `/usr/local/bin/backup_aap_db.sh`:
   ```bash
   sudo vi /usr/local/bin/backup_aap_db.sh
   ```
   
   Add the following content:
   ```bash
   #!/bin/bash
   
   BACKUP_DIR="/var/lib/pgsql/backups"
   DATE=$(date +%Y%m%d_%H%M%S)
   
   # Create backup directory if it doesn't exist
   mkdir -p $BACKUP_DIR
   
   # Backup AWX database
   sudo -u postgres pg_dump awx > $BACKUP_DIR/awx_$DATE.sql
   
   # Backup Automation Hub database
   sudo -u postgres pg_dump automationhub > $BACKUP_DIR/automationhub_$DATE.sql
   
   # Compress backups
   gzip $BACKUP_DIR/awx_$DATE.sql
   gzip $BACKUP_DIR/automationhub_$DATE.sql
   
   # Keep only the last 7 backups
   find $BACKUP_DIR -name "awx_*.sql.gz" -type f -mtime +7 -delete
   find $BACKUP_DIR -name "automationhub_*.sql.gz" -type f -mtime +7 -delete
   
   echo "Backup completed: $BACKUP_DIR/awx_$DATE.sql.gz and $BACKUP_DIR/automationhub_$DATE.sql.gz"
   ```

2. **Make the Script Executable**
   ```bash
   sudo chmod +x /usr/local/bin/backup_aap_db.sh
   ```

3. **Schedule Regular Backups with Cron**
   ```bash
   sudo crontab -e
   ```
   
   Add the following line to run the backup daily at 2 AM:
   ```
   0 2 * * * /usr/local/bin/backup_aap_db.sh > /var/log/aap_db_backup.log 2>&1
   ```

4. **Test the Backup Script**
   ```bash
   sudo /usr/local/bin/backup_aap_db.sh
   ```

## Database Restoration Procedure

In case you need to restore from a backup:

1. **Stop AAP Services**
   ```bash
   sudo systemctl stop automation-controller.service
   sudo systemctl stop pulp.service
   ```

2. **Restore the Database**
   ```bash
   # For AWX database
   gunzip -c /var/lib/pgsql/backups/awx_YYYYMMDD_HHMMSS.sql.gz > /tmp/awx_restore.sql
   sudo -u postgres psql -c "DROP DATABASE awx;"
   sudo -u postgres psql -c "CREATE DATABASE awx;"
   sudo -u postgres psql awx < /tmp/awx_restore.sql
   
   # For Automation Hub database
   gunzip -c /var/lib/pgsql/backups/automationhub_YYYYMMDD_HHMMSS.sql.gz > /tmp/automationhub_restore.sql
   sudo -u postgres psql -c "DROP DATABASE automationhub;"
   sudo -u postgres psql -c "CREATE DATABASE automationhub;"
   sudo -u postgres psql automationhub < /tmp/automationhub_restore.sql
   ```

3. **Restart AAP Services**
   ```bash
   sudo systemctl start automation-controller.service
   sudo systemctl start pulp.service
   ```

## Monitoring PostgreSQL

1. **Check Database Size**
   ```bash
   sudo -u postgres psql -c "SELECT pg_size_pretty(pg_database_size('awx'));"
   sudo -u postgres psql -c "SELECT pg_size_pretty(pg_database_size('automationhub'));"
   ```

2. **Check Table Sizes**
   ```bash
   sudo -u postgres psql -d awx -c "SELECT relname, pg_size_pretty(pg_total_relation_size(relid)) FROM pg_catalog.pg_statio_user_tables ORDER BY pg_total_relation_size(relid) DESC LIMIT 10;"
   ```

3. **Check for Long-Running Queries**
   ```bash
   sudo -u postgres psql -c "SELECT pid, age(clock_timestamp(), query_start), usename, query FROM pg_stat_activity WHERE query != '<IDLE>' AND query NOT ILIKE '%pg_stat_activity%' ORDER BY query_start desc;"
   ```

## Troubleshooting

### Database Connection Issues
- Check PostgreSQL is running: `sudo systemctl status postgresql`
- Verify connection settings in `/etc/tower/conf.d/postgres.py` and `/etc/pulp/settings.py`
- Check PostgreSQL logs: `sudo tail -f /var/lib/pgsql/13/data/log/postgresql-*.log`

### Performance Issues
- Run VACUUM to optimize the database: `sudo -u postgres psql -c "VACUUM ANALYZE;"`
- Check for bloated tables: `sudo -u postgres psql -d awx -c "SELECT relname, n_dead_tup FROM pg_stat_user_tables ORDER BY n_dead_tup DESC LIMIT 10;"`
- Consider increasing `shared_buffers` if you have sufficient RAM

### Disk Space Issues
- Check available disk space: `df -h`
- Identify large tables and consider archiving old data
- Ensure WAL logs are being archived or removed properly

## Next Steps

Your PostgreSQL database is now optimized for Ansible Automation Platform. Proceed to the labs in the [labs directory](../labs/) to learn how to use all the features of Ansible Automation Platform and prepare for the EX374 exam.
