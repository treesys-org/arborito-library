@title: Bases de Datos: Instalación MariaDB y PostgreSQL
@icon: 💾
@description: Desplegando los motores de datos. Instalación segura (mysql_secure_installation), gestión de usuarios y backups básicos con mysqldump y pg_dump.
@order: 5

# MariaDB y PostgreSQL: operación en Linux

Los servidores de bases de datos son **almacenes críticos**: requieren **hardening**, **cuentas con mínimo privilegio**, **backups** probados y **monitorización**. Esta lección cubre **LPIC-2** operativo y prácticas de **RHEL/Debian**.

@section: 1. MariaDB / MySQL

**Instalación:** `sudo apt install mariadb-server` o `dnf install mariadb-server`.

**Endurecimiento inicial:**

```bash
sudo mysql_secure_installation
```

Elimina usuarios anónimos, deshabilita login remoto de root según política, y **no** olvides contraseña de root.

**Cliente:**

```sql
CREATE DATABASE appdb CHARACTER SET utf8mb4;
CREATE USER 'appuser'@'%' IDENTIFIED BY 'secreto_fuerte';
GRANT SELECT,INSERT,UPDATE ON appdb.* TO 'appuser'@'%';
FLUSH PRIVILEGES;
```

**Backups lógicos:**

```bash
mysqldump --single-transaction --all-databases > backup.sql
```

**Servicio:** `systemctl status mariadb`. Socket `/var/run/mysqld/mysqld.sock` (rutas pueden variar).

**RHEL:** SELinux booleans `mysql_*`; datos en `/var/lib/mysql`.

**InnoDB y consistencia:** `mysqldump --single-transaction` evita bloqueos largos en tablas InnoDB durante el dump. Para **MyISAM** antiguo, el enfoque es distinto (no cubierto aquí: migrar a InnoDB).

**Variables de rendimiento:** `innodb_buffer_pool_size` suele ser el 50–70% de la RAM dedicada en servidores dedicados; no copies valores a ciegas sin medir.

@section: 2. PostgreSQL

**Instalación:** `sudo apt install postgresql`.

**Roles:** el usuario `postgres` es superusuario OS. Cambia a él:

```bash
sudo -u postgres psql
```

```sql
CREATE ROLE appuser LOGIN PASSWORD 'secreto';
CREATE DATABASE appdb OWNER appuser;
```

**Backups:**

```bash
sudo -u postgres pg_dump appdb > appdb.sql
sudo -u postgres pg_dumpall > all.sql
```

**Conexiones remotas:** `postgresql.conf` (`listen_addresses`), `pg_hba.conf` (métodos `md5`, `scram-sha-256`).

**Orden en `pg_hba.conf` importa:** la primera regla que coincide gana. Un `reject` mal colocado puede bloquear administración legítima.

**Backups físicos:** `pg_basebackup` para réplicas y copias a nivel de sistema de archivos (requiere coherencia y coordinación con WAL).

@section: 3. systemd y recursos

*   **OOM killer:** bases de datos con mucha RAM deben tener **límites** y **swap** dimensionado.
*   **Cgroups** / **systemd** `MemoryMax` en servicios si convive con otras cargas.

@section: 4. Replicación y HA (visión)

*   **MariaDB:** Galera Cluster.
*   **PostgreSQL:** streaming replication, Patroni.

No es objetivo de esta lección implementarlas, pero debes **nombrarlas** en diseño.

@section: 5. Seguridad

*   No exponer puertos a Internet sin firewall y TLS.
*   Rotación de credenciales; **no** guardar passwords en repos.
*   Auditoría: `general_log` (MySQL) con precaución de rendimiento; `log_statement` en Postgres.

@section: 6. Laboratorio ampliado

1.  Crea un usuario de BD con permisos mínimos y prueba `REVOKE`.
2.  Restaura un `mysqldump` en una base nueva vacía.
3.  En PostgreSQL, crea un rol de solo lectura y verifica que no puede `DELETE`.
4.  Mide el tiempo de un `pg_dump` de una base de prueba con y sin `-Fc` (formato custom) y documenta cuándo usar cada uno.

@section: 7. RPO y RTO en bases de datos

*   **RPO (Recovery Point Objective):** cuánta pérdida de datos aceptas (últimos 5 minutos, 24 h…). Define frecuencia de dumps y retención de WAL/binlog.
*   **RTO (Recovery Time Objective):** cuánto tardas en volver online. Un dump gigante en disco lento alarga el RTO.

**PITR (Point-in-Time Recovery)** en PostgreSQL con archivos WAL y backups base permite restaurar a un instante antes de un `DROP` accidental; merece un proyecto aparte, pero debes conocer el nombre.

@quiz: ¿Qué script interactivo endurece la instalación por defecto de MariaDB/MySQL?
@option: postgres_init
@correct: mysql_secure_installation
@option: sqlite3_secure

@quiz: ¿Qué comando genera un volcado lógico de una base PostgreSQL?
@option: mysqldump
@correct: pg_dump
@option: redis-cli --dump

@quiz: ¿Qué archivo controla quién puede conectar a PostgreSQL y con qué método de autenticación?
@option: postgresql.conf
@correct: pg_hba.conf
@option: my.cnf
