
# Get service **LAN access**:

---

### 1️⃣ PgAdmin

* Open browser:

```
http://192.168.1.7:5050
```

* Login with:

  * Email: `admin@iotroot.com`
  * Password: `ietlab25`
* Add a server in PgAdmin:

  * **Host name/address:** `192.168.1.7`
  * **Port:** `5432`
  * **Username:** `ietpsql`
  * **Password:** `ietlab25`
  * **Maintenance database:** `iot_db`

---

### 2️⃣ MongoDB

* Connect using Mongo client (Compass, Robo 3T, etc.) or CLI:

```
Host: 192.168.1.7
Port: 27017
Username: ietmdb
Password: ietlab25
Authentication DB: iot_db
```

CLI example:

```bash
mongo --host 192.168.1.7 --port 27017 -u ietmdb -p ietlab25 --authenticationDatabase iot_db
```
Connection String
```bash
mongodb://ietmdb:ietlab25@192.168.1.7:27017/iot_db?authSource=admin
```
---

### 3️⃣ Node-RED

* Open browser:

```
http://192.168.1.7:1880
```

* Node-RED UI is ready for flows.

---

Everything should now be **accessible in your LAN**.

Do you want me to make a **small test Node-RED flow** to insert test data into Postgres and MongoDB so you can verify connectivity?
