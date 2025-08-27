
# How to Setup/Install PostgreSQL Server on Azure/AWS/GCP (Ubuntu)
# https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cloud-infrastructure-services.postgresql-ubuntu
This guide walks through the steps required to install and configure PostgreSQL on an Ubuntu server running in a cloud environment such as **Azure**, **AWS**, or **GCP**.

---

## 📦 Step 1: Create Postgres User and add Postgres User to Sudo Group & nstall PostgreSQL
```bash
sudo useradd postgres
```
Ensure the `postgres` user has root (sudo) access.

```bash
sudo usermod -aG sudo postgres
```
```bash
sudo apt update
sudo apt install postgresql postgresql-contrib
---
```
Confirm installation:
After installation, you can confirm that PostgreSQL is installed by checking its version:

```bash
psql --version
```
Start PostgreSQL Service
```bash
sudo systemctl start postgresql
sudo systemctl enable postgresql
```
---
## 🔐 Step 2: Set PostgreSQL User Password 

Set a system password for the `postgres` user:

```bash
sudo passwd postgres
```

Set a password inside PostgreSQL:

```bash
sudo -u postgres psql -c "ALTER USER postgres PASSWORD 'YourSecurePassword';"
```

---

## 💻 Step 3: Access PostgreSQL Shell

Switch to the `postgres` user:

```bash
sudo su -l postgres
```

Enter PostgreSQL shell:

```bash
psql
```


How many database users (roles) you have in PostgreSQL
```sql
SELECT rolname FROM pg_roles;
```

---
Exit the PostgreSQL shell:
```
\q
```
## 🛠 Step 4: Create Database and User Roles

Create a new database and user inside the PostgreSQL shell:

```sql
CREATE DATABASE mytestdb;
CREATE USER mytestuser WITH ENCRYPTED PASSWORD 'MyStr0ngP@SS';
GRANT ALL PRIVILEGES ON DATABASE mytestdb TO mytestuser;
```

List databases:

```sql
\l
```

Connect to a database:

```sql
\c mytestdb
```

---

## 🌐 Step 5: Enable Remote Connections

PostgreSQL by default only allows local connections. To allow remote connections:

### Edit `postgresql.conf`

First, find your installed PostgreSQL version:

```bash
ls /etc/postgresql/
```

Then edit:

```bash
sudo nano /etc/postgresql/16/main/postgresql.conf
```

Uncomment and set:

```conf
listen_addresses = '*'
```

### Edit `pg_hba.conf`

```bash
sudo nano /etc/postgresql/16/main/pg_hba.conf
```

Add one of the following lines at the bottom:

**To allow all IPs (Not recommended for production):**
```
host    all             all             0.0.0.0/0               md5
```

**To allow a trusted subnet:**
```
host    all             all             10.10.10.0/24           md5
```

---

## 🔄 Step 6: Restart PostgreSQL Service

```bash
sudo systemctl restart postgresql
```

---

## 🧰 Step 7: Setup pgAdmin Web Tool (Optional)

If you want to manage PostgreSQL via web UI:

```bash
sudo /usr/pgadmin4/bin/setup-web.sh
```

Then open in browser:

```
http://<your-server-ip>/pgadmin4
```

Login with the email and password created during the setup script.

---

## 🔒 Step 8: Firewall Ports

Ensure the following ports are open on your VM firewall and cloud security group settings:

- **5432** – PostgreSQL database port
- **80** – HTTP (for pgAdmin)
- **443** – HTTPS (for pgAdmin)

---

## ✅ Done!

Your PostgreSQL server is now installed and configured on Ubuntu in the cloud. You can connect remotely using `psql`, pgAdmin, or application clients.

---
