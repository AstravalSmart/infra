## ✅ Fix Steps for MongoDB 

1. **Stop MongoDB container**:

```bash
docker compose down
```

2. **Remove the MongoDB data volume** (this will reset MongoDB):

```bash
docker volume rm server_mongodata
```

3. **Start fresh**:

```bash
docker compose up -d mongodb
```

Now the root user will be recreated with the credentials from your compose file:

* **Username:** `ietmdb`
* **Password:** `ietlab25`
* **Database:** `iot_db`

---

### Connection string (works now):

```
mongodb://ietmdb:ietlab25@192.168.1.7:27017/iot_db?authSource=admin
```

* Test in CLI:

```bash
mongo "mongodb://ietmdb:ietlab25@192.168.1.7:27017/iot_db?authSource=admin"
```

## ✅ Fix Steps for Postgresql
---

### Steps to fix:

1. **Stop all containers**:

```bash
docker compose down
```

2. **Remove the Postgres volume** to force re-initialization:

```bash
docker volume rm server_pgdata
```

3. **Start containers again**:

```bash
docker compose up -d
```

Now Postgres will be initialized fresh with:

* **User:** `ietpsql`
* **Password:** `ietlab25`
* **Database:** `iot_db`

---

After this, in PgAdmin:

* **Host:** `192.168.1.7`
* **Port:** `5432`
* **Username:** `ietpsql`
* **Password:** `ietlab25`
* **Database:** `iot_db`

You will be able to connect successfully.
<img width="819" height="291" alt="image" src="https://github.com/user-attachments/assets/0b31c3bf-4bb8-4c8a-8833-269efdd873d6" />

---
