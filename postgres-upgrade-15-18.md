# 🐘 PostgreSQL 15 → 18 Upgrade Guide (RHEL 8 / CentOS 8)

This document provides a complete, step-by-step process for upgrading PostgreSQL from version 15 to 18 using `pg_upgrade` on RHEL 8 or CentOS 8 systems.

---

## 🧩 Prerequisites

Before starting:

* You have **sudo (root)** privileges.
* PostgreSQL **15** is already installed and running.
* You have a **valid backup** of all databases.
* You have at least **2 GB of free disk space** for the new data directory.

---

## ⚙️ 1. Verify Existing PostgreSQL Installation

```bash
ls /opt/PostgreSQL/
psql --version
```

Ensure you see PostgreSQL **15** listed.

---

## 📦 2. Add the Official PostgreSQL YUM Repository

```bash
sudo dnf install -y \
  https://download.postgresql.org/pub/repos/yum/reporpms/EL-8-x86_64/pgdg-redhat-repo-latest.noarch.rpm
```

---

## 🚫 3. Disable the Built-In PostgreSQL Module Stream

```bash
sudo dnf -qy module disable postgresql
```

> **Why:** RHEL’s default module only provides older PostgreSQL versions (like 10–13). Disabling avoids conflicts with the official PostgreSQL repository.

---

## 🔍 4. Discover Available PostgreSQL Packages

```bash
dnf list postgresql* --showduplicates
```

Expected output includes packages like:

```
postgresql15-server.x86_64
postgresql17-server.x86_64
postgresql18-server.x86_64
```

---

## 📥 5. Install PostgreSQL 17

```bash
sudo dnf install -y postgresql17-server postgresql17
```

---

## 🏗️ 6. Initialize the PostgreSQL 17 Cluster

```bash
sudo /usr/pgsql-17/bin/postgresql-17-setup initdb
```

This creates the new data directory at `/var/lib/pgsql/17/data`.

---

## 🔄 7. Enable and Start PostgreSQL 17

```bash
sudo systemctl enable postgresql-17
sudo systemctl start postgresql-17
```

If it fails, view logs:

```bash
sudo tail -n 100 /var/lib/pgsql/17/data/log/postgresql-*.log
```

---

## 🧮 8. Define Version and Directory Variables

```bash
OLDVER=15
NEWVER=17
OLDDATA=/var/lib/pgsql/${OLDVER}/data
NEWDATA=/var/lib/pgsql/${NEWVER}/data
```

Confirm:

```bash
echo $OLDDATA
echo $NEWDATA
```

---

## 📴 9. Stop the Old PostgreSQL 15 Service

```bash
sudo systemctl stop postgresql-${OLDVER}
systemctl status postgresql-15
```

---

## 🧰 10. Run a Dry-Run Compatibility Check

```bash
/usr/pgsql-${NEWVER}/bin/pg_upgrade \
  --old-datadir=${OLDDATA} \
  --new-datadir=${NEWDATA} \
  --old-bindir=/usr/pgsql-${OLDVER}/bin \
  --new-bindir=/usr/pgsql-${NEWVER}/bin \
  --check
```

If it says `Clusters are compatible`, proceed.

---

## ⚡ 11. Perform the Actual Upgrade

```bash
/usr/pgsql-${NEWVER}/bin/pg_upgrade \
  --old-datadir=${OLDDATA} \
  --new-datadir=${NEWDATA} \
  --old-bindir=/usr/pgsql-${OLDVER}/bin \
  --new-bindir=/usr/pgsql-${NEWVER}/bin
```

This migrates all databases and metadata.

---

## 🧾 12. Adjust Configuration (Optional)

Edit configuration files:

```bash
vi /var/lib/pgsql/17/data/postgresql.conf
vi /var/lib/pgsql/17/data/pg_hba.conf
```

Common changes:

```
listen_addresses = '*'
```

Adjust authentication rules as needed.

---

## 🔁 13. Start PostgreSQL 17

```bash
sudo systemctl restart postgresql-17
systemctl status postgresql-17
```

---

## 🧍 14. Verify Processes and Connectivity

```bash
ps aux | grep postgres
sudo ss -ltnp | grep postgres
```

Ensure PostgreSQL 17 is listening on `127.0.0.1:5432` or your desired port.

---

## 🧹 15. Cleanup (Optional)

After confirming a successful upgrade:

```bash
sudo rm -rf /var/lib/pgsql/15
```

⚠️ **Only do this if your backups and migration are verified.**

---

## ✅ 16. Verify the Upgrade

```bash
sudo -iu postgres
psql
SELECT version();
```

Example output:

```
PostgreSQL 17.1 on x86_64-pc-linux-gnu ...
```

---

# 🚀 PostgreSQL 17 → 18 Upgrade Steps

Once PostgreSQL 17 is running and stable, you can upgrade to version 18 following the same process.

## 🧩 1. Install PostgreSQL 18

```bash
sudo dnf install -y \
  https://download.postgresql.org/pub/repos/yum/reporpms/EL-8-x86_64/pgdg-redhat-repo-latest.noarch.rpm
sudo dnf -qy module disable postgresql
sudo dnf install -y postgresql18-server
```

---

## 🏗️ 2. Initialize PostgreSQL 18 Cluster

```bash
sudo /usr/pgsql-18/bin/postgresql-18-setup initdb
```

---

## 🔄 3. Enable and Start PostgreSQL 18

```bash
sudo systemctl enable postgresql-18
sudo systemctl start postgresql-18
sudo systemctl status postgresql-18
```

If startup fails, check:

```bash
vi /var/lib/pgsql/18/data/log/postgresql-Tue.log
```

---

## 📴 4. Stop PostgreSQL 17 Before Upgrade

```bash
sudo systemctl stop postgresql-17
sudo systemctl status postgresql-17
```

---

## 🧮 5. Define Upgrade Variables

```bash
OLDVER=17
NEWVER=18
OLDDATA=/var/lib/pgsql/${OLDVER}/data
NEWDATA=/var/lib/pgsql/${NEWVER}/data

echo $OLDDATA
echo $NEWDATA
```

---

## 🧰 6. Run Compatibility Check

```bash
/usr/pgsql-${NEWVER}/bin/pg_upgrade \
  --old-datadir=${OLDDATA} \
  --new-datadir=${NEWDATA} \
  --old-bindir=/usr/pgsql-${OLDVER}/bin \
  --new-bindir=/usr/pgsql-${NEWVER}/bin \
  --check
```

If compatible, proceed.

---

## ⚡ 7. Perform the Actual Upgrade

```bash
/usr/pgsql-${NEWVER}/bin/pg_upgrade \
  --old-datadir=${OLDDATA} \
  --new-datadir=${NEWDATA} \
  --old-bindir=/usr/pgsql-${OLDVER}/bin \
  --new-bindir=/usr/pgsql-${NEWVER}/bin
```

---

## 🔁 8. Start PostgreSQL 18 and Verify

```bash
sudo systemctl start postgresql-18
sudo systemctl status postgresql-18
ps aux | grep postgres
sudo ss -ltnp | grep postgres
```

Ensure PostgreSQL 18 is listening correctly.

---

## ✅ 9. Verify Version

```bash
sudo -iu postgres
psql
SELECT version();
```

Expected output:

```
PostgreSQL 18.x on x86_64-pc-linux-gnu ...
```

---

## 🧹 10. Cleanup Old Data

```bash
sudo rm -rf /var/lib/pgsql/17
```

(Only after validating data integrity.)

---

🎉 **Done!** You’ve successfully upgraded PostgreSQL from **v15 → v18** using `pg_upgrade` on RHEL/CentOS 8.
