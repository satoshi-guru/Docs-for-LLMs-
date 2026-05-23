---
title: ""
url: https://supabase.com/docs/guides/platform/migrating-to-supabase/postgres
---

# 

Platform

#

Migrate from Postgres to Supabase

##

Migrate your existing Postgres database to Supabase.

* * *

This is a guide for migrating your Postgres database to [Supabase](https://supabase.com). Supabase is a robust and open-source platform. Supabase provides all the backend features developers need to build a product: a Postgres database, authentication, instant APIs, edge functions, real-time subscriptions, and storage. Postgres is the core of Supabase—for example, you can use row-level security, and there are more than 40 Postgres extensions available.

This guide demonstrates how to migrate your Postgres database to Supabase to get the most out of Postgres while gaining access to all the features you need to build a project.

This guide provides three methods for migrating your Postgres database to Supabase:

  1. **Google Colab** \- Guided notebook with copy-paste workflow
  2. **Manual Dump/Restore** \- CLI approach, works for all versions
  3. **Logical Replication** \- Minimal downtime, requires Postgres 10+


## Connection modes#

Supabase provides the following connection modes:

  * Direct connection
  * Supavisor session mode
  * Supavisor transaction mode


Use Supavisor session mode for the database migration tasks (pg_dump/restore and logical replication).

## Method 1: Google Colab (easiest)#

Supabase provides a Google Colab migration notebook for a guided migration experience: [Supabase Migration Colab Notebook](https://colab.research.google.com/github/mansueli/Supa-Migrate/blob/main/Migrate_Postgres_Supabase.ipynb)

This is ideal if you prefer a step-by-step, copy-paste workflow with minimal setup.

## Method 2: Manual dump/restore#

This method works for all Postgres versions using CLI tools.

### Prerequisites#

#### Source Postgres requirements#

  * Connection string with rights to run `pg_dump`
  * No special settings required for dump/restore
  * Network access from migration VM


#### Migration environment#

  * Cloud VM running Ubuntu in the same region as source or target database
  * Postgres client tools matching your source database version
  * tmux for session persistence
  * Sufficient disk space (usually ~50% of source database size is enough, but varies case by case)


### Pre-Migration checklist#


    1

    -- Check database size

    2

    select pg_size_pretty(pg_database_size(current_database())) as size;

    3

    4

    -- Check Postgres version

    5

    select version();

    6

    7

    -- List installed extensions

    8

    select * from pg_extension order by extname;

    9

    10

    -- Check active connections

    11

    select count(*) from pg_stat_activity;

#### Check available extensions in Supabase#


    1

    -- Connect to your Supabase database and check available extensions

    2

    SELECT name, comment FROM pg_available_extensions ORDER BY name;

    3

    4

    -- Compare with source database extensions

    5

    SELECT extname FROM pg_extension ORDER BY extname;

    6

    7

    -- Install needed extensions

    8

    CREATE EXTENSION IF NOT EXISTS extension_name;

### Step 1: Set up migration VM#

For optimal performance, run the migration from a cloud VM, not your local machine. The VM should be in the same region as either your source or target database to optimize network performance. See the Resource Requirements table in Step 2 for VM sizing recommendations.

#### Set up Ubuntu VM#


    1

    # Install Postgres client and tools

    2

    sudo apt update

    3

    sudo apt install software-properties-common

    4

    sudo sh -c 'echo "deb http://apt.Postgres.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'

    5

    wget --quiet -O - https://www.Postgres.org/media/keys/ACCC4CF8.asc | sudo apt-key add -

    6

    sudo apt update

    7

    sudo apt install Postgres-client-17 tmux htop iotop moreutils

    8

    9

    # Start or attach to tmux session

    10

    tmux a -t migration || tmux new -s migration

### Step 2: Prepare Supabase project#

  1. Create a Supabase project at [supabase.com/dashboard](/dashboard)
  2. Note your database password
  3. Install required extensions via SQL or Dashboard
  4. Get your connection string:
     * Go to **Project → Settings → Database → Connection Pooling**
     * Select **Session pooler** (port 5432) and copy the connection string
     * Connection format: `Postgres://postgres.[ref]:[password]@aws-0-[region].pooler.supabase.com:5432/postgres`


**Important Notes** :

  * **Users/roles are not migrated** \- You'll need to recreate roles and privileges after import ([Supabase Roles Guide](/blog/postgres-roles-and-privileges))
  * **Row Level Security (RLS) status on tables is not migrated** \- You'll need to enable RLS for tables after migration.


**Resource Requirements** :

Database Size| Recommended Compute| Recommended VM| Action Required
---|---|---|---
< 10 GB| Default| 2 vCPUs, 4 GB RAM| None
10-100 GB| Default-Small| 4 vCPUs, 8 GB RAM| Consider compute upgrade
100-500 GB| Large compute| 8 vCPUs, 16 GB RAM, NVMe| Upgrade compute before restore
500 GB - 1 TB| XL compute| 16 vCPUs, 32 GB RAM, NVMe| Upgrade compute before restore
> 1 TB| Custom| Custom| [Contact support](/dashboard/support/new) first

Also, you can temporarily increase compute size and/or disk IOPS and throughput via Settings → Compute and Disk if you want faster database restore (you can use larger -j for pg_restore if you do so).

### Step 3: Create database dump#

#### Set source database to read only mode for production migration#

If doing a maintenance window migration, prevent data changes:


    1

    -- Connect to source database and run:

    2

    ALTER DATABASE your_database_name SET default_transaction_read_only = true;

For testing without a maintenance window, skip this step but use lower -j values.

#### Dump the database#


    1

    # Determine number of parallel jobs based on:

    2

    # - Source database CPU cores (don't saturate production)

    3

    # - VM CPU cores

    4

    # - For testing without maintenance window: use lower values to be gentle

    5

    # - For production with maintenance window: can use higher values

    6

    7

    DUMP_JOBS=4  # Adjust based on your setup

    8

    9

    # Check available cores on VM

    10

    nproc

    11

    12

    # Create dump with progress logging

    13

    pg_dump \

    14

      --host=<source_host> \

    15

      --port=<source_port> \

    16

      --username=<source_username> \

    17

      --dbname=<source_database> \

    18

      --jobs=$DUMP_JOBS \

    19

      --format=directory \

    20

      --no-owner \

    21

      --no-privileges \

    22

      --no-subscriptions \

    23

      --verbose \

    24

      --file=./db_dump 2>&1 | ts | tee -a dump.log

**Notes about dump flags** :

  * `--no-owner --no-privileges`: Applied at dump time to prevent Supabase user management conflicts. While these could be used in pg_restore instead, applying them during dump keeps the dump file cleaner and more portable.
  * `--no-subscriptions`: Logical replication subscriptions won't work in the target
  * The dump captures all data and schema but excludes ownership/privileges that would conflict with Supabase's managed environment
  * To only migrate a single database schema, add the `--schema=PATTERN` parameter to your `pg_dump` command.
  * To exclude a schema: `--exclude-schema=PATTERN`.
  * To only migrate a single table: `--table=PATTERN`.
  * To exclude a table: `--exclude-table=PATTERN`.


Run `pg_dump --help` for a full list of options.

#### Recommended parallelization (-j values)#

Database Size| Testing (no maintenance window)| Production (with maintenance window)| Limiting Factor
---|---|---|---
< 10 GB| 2| 4| Source CPU
10-100 GB| 2-4| 8| Source CPU
100-500 GB| 4| 16| Disk IOPS
500 GB - 1 TB| 4-8| 16-32| Disk IOPS + CPU

**Note** : For testing without a maintenance window, use lower -j values to avoid impacting production performance.

### Step 4: Restore to Supabase#

#### Set connection and restore#


    1

    # Set Supabase connection (Session Pooler on port 5432 or direct connection)

    2

    export SUPABASE_DB_URL="Postgres://postgres.[ref]:[password]@aws-0-[region].pooler.supabase.com:5432/postgres"

    3

    4

    # Determine restore parallelization based on your Supabase compute size:

    5

    # Free tier: 2 cores → use -j 2

    6

    # Small compute: 2 cores → use -j 2

    7

    # Medium compute: 4 cores → use -j 4

    8

    # Large compute: 8 cores → use -j 8

    9

    # XL compute: 16 cores → use -j 16

    10

    11

    RESTORE_JOBS=8  # Adjust based on your Supabase compute size

    12

    13

    # Restore the dump (parallel mode)

    14

    # Note: -j cannot be used with --single-transaction

    15

    pg_restore \

    16

      --dbname="$SUPABASE_DB_URL" \

    17

      --jobs=$RESTORE_JOBS \

    18

      --format=directory \

    19

      --no-owner \

    20

      --no-privileges \

    21

      --verbose \

    22

      ./db_dump 2>&1 | ts | tee -a restore.log

If restore fails with extension errors, check that errors are only extension-related.

### Step 5: Post-Migration tasks#

#### Update statistics (important)#


    1

    psql "$SUPABASE_DB_URL" -c "VACUUM VERBOSE ANALYZE;"

For Postgres 18+, pg_dump includes statistics with `--with-statistics`, but you should still run VACUUM for optimal performance.

#### Verify migration#


    1

    -- Check row counts

    2

    select schemaname, tablename, n_live_tup

    3

    from pg_stat_user_tables

    4

    order by n_live_tup desc

    5

    limit 20;

    6

    -- Verify data with application-specific queries

#### Re-enable writes on source (if keeping it)#


    1

    ALTER DATABASE your_database_name SET default_transaction_read_only = false;

### Migration time estimates#

Database Size| Dump Time| Restore Time| Total Time
---|---|---|---
10 GB| ~5 min| ~10 min| ~15 min
100 GB| ~30 min| ~45 min| ~1.5 hours
500 GB| ~2 hours| ~3 hours| ~5 hours
1 TB| ~4 hours| ~6 hours| ~10 hours

_Times vary based on hardware, network, and parallelization settings_

### Important notes#

  1. **Region proximity matters** : VM should be in the same region as the source or target for best performance
  2. **Downgrade migrations** : While technically possible in some cases, highly not recommended
  3. **Testing without downtime** : Use lower `-j` values for pg_dump to avoid impacting production
  4. **For pg_restore** : Can use full parallelization regardless of production impact
  5. **Monitor resources** : Watch CPU, disk I/O with `htop`, `iotop`
  6. **Disk I/O** : Often the bottleneck before network bandwidth


* * *

## Method 3: Logical replication#

This method allows migration with minimal downtime using Postgres's logical replication feature. Requires Postgres 10+ on both source and target.

### When to use logical replication#

  * You need minimal downtime (minutes instead of hours)
  * Source database is Postgres 10 or higher
  * You can configure logical replication on the source
  * Database has high write activity that can't be paused for long


### Source Postgres prerequisites#

#### Access & privileges#

  * Connection string with rights to CREATE PUBLICATION and read tables
  * Superuser or replication privileges recommended


#### Required settings for logical replication#

  * `wal_level = logical`
  * `max_wal_senders ≥ 1`
  * `max_replication_slots ≥ 1`
  * Sufficient `max_connections` (current + 1 for subscription)


#### Replica identity#

Every table receiving UPDATE/DELETE must have a replica identity (typically a PRIMARY KEY). For tables without one:


    1

    ALTER TABLE schema.table_name REPLICA IDENTITY FULL;

#### Non-Replicated items#

  * **DDL changes** (schema modifications)
  * **Sequences** (need manual sync)
  * **Large Objects (LOBs)** (use dump/restore or store in regular bytea columns)


Plan a schema freeze, sequence sync before cutover, and handle LOBs separately.

### Step 1: Configure source database#

Edit Postgres configuration files:

#### Postgres.conf#


    1

    # Set Supabase connection (Session Pooler on port 5432 or direct connection)

    2

    export SUPABASE_DB_URL="Postgres://postgres.[ref]:[password]@aws-0-[region].pooler.supabase.com:5432/postgres"

    3

    4

    # Set WAL level to logical

    5

    wal_level = logical

    6

    7

    # Ensure sufficient replication slots

    8

    max_replication_slots = 10

    9

    10

    # Ensure sufficient WAL senders

    11

    max_wal_senders = 10

    12

    13

    # Set appropriate max_connections (current connections + 1 for subscription)

    14

    max_connections = 200  # Adjust based on your needs

    15

    16

    # Optional: Enable SSL for secure replication

    17

    ssl = on

    18

    19

    # Allow connections from Supabase

    20

    listen_addresses = '*'  # Or specific IP addresses

#### pg_hba.conf#


    1

    # Allow replication connections from Supabase

    2

    # Replace <supabase_ip_range> with actual Supabase IP range

    3

    host    replication     all     <supabase_ip_range>    md5

    4

    host    all            all     <supabase_ip_range>    md5

    5

    6

    # With SSL:

    7

    hostssl replication     all     <supabase_ip_range>    md5

    8

    hostssl all            all     <supabase_ip_range>    md5

Restart Postgres:


    1

    sudo systemctl restart Postgres

    2

    sudo systemctl status Postgres

### Step 2: Verify configuration#


    1

    -- Should return 'logical'

    2

    SHOW wal_level;

    3

    4

    -- Check other parameters

    5

    SHOW max_replication_slots;

    6

    SHOW max_wal_senders;

    7

    8

    -- Check current connections

    9

    SELECT count(*) FROM pg_stat_activity;

### Step 3: Check and set replica identity#


    1

    -- Find tables without primary keys

    2

    SELECT n.nspname, c.relname

    3

    FROM pg_class c

    4

    JOIN pg_namespace n ON n.oid = c.relnamespace

    5

    LEFT JOIN pg_constraint pk ON pk.conrelid = c.oid AND pk.contype = 'p'

    6

    WHERE c.relkind = 'r'

    7

      AND pk.oid IS NULL

    8

      AND n.nspname NOT IN ('pg_catalog','information_schema');

    9

    10

    -- For tables without a primary key, set REPLICA IDENTITY FULL

    11

    ALTER TABLE my_schema.my_table REPLICA IDENTITY FULL;

### Step 4: Export and restore schema only#


    1

    # Export schema from source

    2

    pg_dump \

    3

      -h <source_host> \

    4

      -U <source_user> \

    5

      -p <source_port> \

    6

      -d <source_database> \

    7

      --schema-only \

    8

      --no-privileges \

    9

      --no-subscriptions \

    10

      --format=directory \

    11

      -f ./schema_dump

    12

    13

    # Restore schema to Supabase (use Session Pooler)

    14

    pg_restore \

    15

      --dbname="$SUPABASE_DB_URL" \

    16

      --format=directory \

    17

      --schema-only \

    18

      --no-privileges \

    19

      --single-transaction \

    20

      --verbose \

    21

      ./schema_dump

### Step 5: Create publication on source#


    1

    -- Create publication for all tables

    2

    CREATE PUBLICATION supabase_migration FOR ALL TABLES;

    3

    4

    -- Or for specific tables only (doesn't require superuser)

    5

    CREATE PUBLICATION supabase_migration FOR TABLE

    6

      schema1.table1,

    7

      schema1.table2,

    8

      public.table3;

    9

    10

    -- Verify publication was created

    11

    SELECT * FROM pg_publication;

### Step 6: Create subscription on Supabase#

Connect to your Supabase database:


    1

    -- Create subscription with SSL (recommended)

    2

    CREATE SUBSCRIPTION supabase_subscription

    3

    CONNECTION 'host=<source_host> port=<source_port> user=<source_user> password=<source_password> dbname=<source_database> sslmode=require'

    4

    PUBLICATION supabase_migration;

    5

    6

    -- Or without SSL (if source doesn't support it)

    7

    CREATE SUBSCRIPTION supabase_subscription

    8

    CONNECTION 'host=<source_host> port=<source_port> user=<source_user> password=<source_password> dbname=<source_database> sslmode=disable'

    9

    PUBLICATION supabase_migration;

### Step 7: Monitor replication status#


    1

    -- On Supabase (subscriber) - check subscription status

    2

    select * from pg_subscription_rel;

    3

    4

    -- srsubstate = 'r' means ready (synchronized)

    5

    -- srsubstate = 'i' means initializing

    6

    -- srsubstate = 'd' means data is being copied

    7

    8

    -- Overall subscription status

    9

    select * from pg_stat_subscription;

    10

    11

    -- On source database - check replication status

    12

    select * from pg_stat_replication;

    13

    14

    -- Check replication lag

    15

    select

    16

      slot_name,

    17

      pg_size_pretty(pg_wal_lsn_diff(pg_current_wal_lsn(), restart_lsn)) as lag_size

    18

    from pg_replication_slots;

Wait until all tables show `srsubstate = 'r'` (ready) status.

### Step 8: Synchronize sequences#

After initial data sync is complete, but BEFORE switching to Supabase:


    1

    # Set source to read-only

    2

    psql -h <source_host> -c "ALTER DATABASE <source_database> SET default_transaction_read_only = true;"

    3

    4

    # Export sequences from source

    5

    pg_dump \

    6

      -h <source_host> \

    7

      -U <source_user> \

    8

      -p <source_port> \

    9

      -d <source_database> \

    10

      --data-only \

    11

      --table='*_seq' \

    12

      --table='*_id_seq' > sequences.sql

    13

    14

    # Import sequences to Supabase

    15

    psql "$SUPABASE_DB_URL" -f sequences.sql

### Step 9: Switch to Supabase#

  1. Ensure replication lag is zero:




    1

    -- On Supabase

    2

    select * from pg_stat_subscription;

    3

    -- Check that latest_end_lsn is current

  2. Stop writes to the source database (if not already read-only)

  3. Drop subscription on Supabase:




    1

    DROP SUBSCRIPTION supabase_subscription;

  4. Update application connection strings to point to Supabase

  5. Verify application functionality


### Step 10: Cleanup#

On source database (after successful migration):


    1

    -- Remove publication

    2

    DROP PUBLICATION supabase_migration;

    3

    4

    -- Check and remove any remaining replication slots

    5

    SELECT * FROM pg_replication_slots;

    6

    DROP REPLICATION SLOT slot_name;  -- if any remain

    7

    8

    -- The source database should remain read-only or be decommissioned

    9

    -- Do NOT re-enable writes to avoid a split-brain scenario!

### Troubleshooting logical replication#

Issue| Solution
---|---
"could not connect to the publisher"| Check network connectivity, firewall rules, pg_hba.conf
"role does not exist"| Ensure replication user exists on source with REPLICATION privilege
"publication does not exist"| Verify publication name and that it was created successfully
Replication lag growing| Check network bandwidth, source database load, add more WAL senders
Tables stuck in `i` state| Check for locks on source tables, verify table structure matches
"out of replication slots"| Increase max_replication_slots in Postgres.conf

### Important limitations#

  * **DDL changes** : Schema modifications are not replicated - freeze schema during migration
  * **Sequences** : Need manual synchronization before cutover
  * **Large Objects (LOBs)** : Not replicated - use dump/restore or store in regular bytea columns
  * **Custom types** : May need special handling
  * **Users and roles** : Must be recreated manually on Supabase


For detailed restrictions, see [Postgres Logical Replication Restrictions](https://www.Postgres.org/docs/current/logical-replication-restrictions.html)

### When to use which method#

**Use Dump/Restore when:**

  * Downtime window is acceptable
  * Source is Postgres < 10
  * Simpler process preferred
  * Cannot configure logical replication on the source


**Use Logical Replication when:**

  * Minimal downtime required
  * Postgres 10+ on both sides
  * Can modify source configuration
  * Have replication privileges


## Getting help#

  * For databases > 150 GB: [Contact Supabase support](/dashboard/support/new) before starting
  * [Supabase Dashboard Support](/dashboard/support/new)
  * [Supabase Discord](https://discord.supabase.com)
  * [Postgres Roles and Privileges Guide](/blog/postgres-roles-and-privileges)
  * [Row Level Security Guide](/docs/guides/database/postgres/row-level-security)


### Is this helpful?

No Yes

### AI Tools

Copy as Markdown[Ask ChatGPT](https://chatgpt.com/?hint=search&q=Read from https://supabase.com/docs/guides/platform/migrating-to-supabase/postgres so I can ask questions about its contents)[Ask Claude](https://claude.ai/new?q=Read from https://supabase.com/docs/guides/platform/migrating-to-supabase/postgres so I can ask questions about its contents)