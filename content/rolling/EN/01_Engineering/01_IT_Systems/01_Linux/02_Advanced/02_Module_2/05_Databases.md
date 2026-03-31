@title: Databases: MariaDB and PostgreSQL Installation
@icon: 💾
@description: Deploy data engines. Secure installation (mysql_secure_installation), user management, and basic backups with mysqldump and pg_dump.
@order: 5

# MariaDB and PostgreSQL: operating on Linux

Database servers are **critical stores**: they need **hardening**, **least-privilege accounts**, **tested backups**, and **monitoring**. This lesson covers **LPIC-2** operations and **RHEL/Debian** practice.

@section: 1. MariaDB / MySQL

**Install:** `sudo apt install mariadb-server` or `dnf install mariadb-server`.

**Initial hardening:**

```bash
sudo mysql_secure_installation
```

Removes anonymous users, restricts remote root per policy, and sets a strong root password.

**Example SQL:**

```sql
CREATE DATABASE appdb CHARACTER SET utf8mb4;
CREATE USER 'appuser'@'%' IDENTIFIED BY 'strong_secret';
GRANT SELECT,INSERT,UPDATE ON appdb.* TO 'appuser'@'%';
FLUSH PRIVILEGES;
```

**Logical backups:**

```bash
mysqldump --single-transaction --all-databases > backup.sql
```

**Service:** `systemctl status mariadb`. Socket often `/var/run/mysqld/mysqld.sock` (paths vary).

**RHEL:** SELinux booleans `mysql_*`; data under `/var/lib/mysql`.

**InnoDB and consistency:** `mysqldump --single-transaction` avoids long locks on InnoDB tables during dumps. Legacy **MyISAM** behaves differently (prefer migrating to InnoDB).

**Tuning:** `innodb_buffer_pool_size` is often sized to a large fraction of dedicated DB RAM—measure before copying blindly.

@section: 2. PostgreSQL

**Install:** `sudo apt install postgresql`.

**Roles:** OS user `postgres` is the DB superuser. Switch to it:

```bash
sudo -u postgres psql
```

```sql
CREATE ROLE appuser LOGIN PASSWORD 'strong_secret';
CREATE DATABASE appdb OWNER appuser;
```

**Backups:**

```bash
sudo -u postgres pg_dump appdb > appdb.sql
sudo -u postgres pg_dumpall > all.sql
```

**Remote access:** `postgresql.conf` (`listen_addresses`), `pg_hba.conf` (`md5`, `scram-sha-256`).

**`pg_hba.conf` order matters:** the first matching rule wins. A misplaced `reject` can block legitimate admin access.

**Physical backups:** `pg_basebackup` for replicas and filesystem-level copies (needs WAL coordination).

@section: 3. systemd and resources

* **OOM killer:** large DB RAM usage needs **limits** and sized **swap**.
* **Cgroups** / systemd `MemoryMax` when sharing the host.

@section: 4. Replication and HA (overview)

* **MariaDB:** Galera Cluster.
* **PostgreSQL:** streaming replication, Patroni.

You are not expected to build them here, but you should **name** them in design discussions.

@section: 5. Security

* Do not expose DB ports to the Internet without firewall and TLS.
* Rotate credentials; **never** commit passwords to repos.
* Auditing: `general_log` (MySQL) with performance care; `log_statement` in Postgres.

@section: 6. Extended lab

1. Create a DB user with minimal privileges and test `REVOKE`.
2. Restore a `mysqldump` into a new empty database.
3. In PostgreSQL, create a read-only role and verify it cannot `DELETE`.
4. Time a `pg_dump` of a test database with and without `-Fc` (custom format) and document when each is appropriate.

@section: 7. RPO, RTO, and PITR

* **RPO (Recovery Point Objective):** how much data loss you accept—drives dump frequency and WAL/binlog retention.
* **RTO (Recovery Time Objective):** how long until you are back online—a huge dump on slow storage stretches RTO.

**PITR (Point-in-Time Recovery)** in PostgreSQL with WAL and base backups lets you restore to before an accidental `DROP`—worth a dedicated project, but know the term.

@quiz: Which interactive script hardens a default MariaDB/MySQL install?
@option: postgres_init
@correct: mysql_secure_installation
@option: sqlite3_secure

@quiz: Which command creates a logical dump of a single PostgreSQL database?
@option: mysqldump
@correct: pg_dump
@option: redis-cli --dump

@quiz: Which file controls who may connect to PostgreSQL and with which auth method?
@option: postgresql.conf
@correct: pg_hba.conf
@option: my.cnf
