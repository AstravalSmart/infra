# VerneMQ Installation and Password Authentication Guide

## 1. Install Dependencies

Make sure you have the required dependencies:

```bash
sudo apt update
sudo apt install -y build-essential libssl-dev erlang git wget
````

---

## 2. Download and Build VerneMQ

Clone the VerneMQ repository:

```bash
cd ~/server
git clone https://github.com/vernemq/vernemq.git
cd vernemq
```

Build VerneMQ:

```bash
make rel
```

After building, the release will be located at:

```
_build/default/rel/vernemq
```

---

## 3. Start VerneMQ

Start the VerneMQ node:

```bash
_build/default/rel/vernemq/bin/vernemq start
```

Check the node status:

```bash
_build/default/rel/vernemq/bin/vernemq ping
```

Expected output:

```
pong
```

Attach to the node (optional):

```bash
_build/default/rel/vernemq/bin/vernemq attach
```

---

## 4. Configure Password Authentication

Open the VerneMQ configuration file:

```bash
nano _build/default/rel/vernemq/etc/vernemq.conf
```

Ensure the following line is set:

```text
allow_anonymous = off
```

---

### 4.1 Create Password File and Add User

Create the password file and add a user `testuser`:

```bash
_build/default/rel/vernemq/bin/vmq-passwd -c _build/default/rel/vernemq/etc/vmq.passwd testuser
```

You will be prompted to enter and confirm the password.

Restart VerneMQ to apply changes:

```bash
_build/default/rel/vernemq/bin/vernemq restart
```

---

## 5. Test MQTT Connection

### 5.1 Subscribe to a Topic

```bash
mosquitto_sub -h 127.0.0.1 -p 1883 -t "test/topic" -u testuser -P yourpassword
```

### 5.2 Publish a Message

```bash
mosquitto_pub -h 127.0.0.1 -p 1883 -t "test/topic" -m "Hello MQTT" -u testuser -P yourpassword
```

If successful, the subscriber will receive the message.

---

## 6. Notes

* Password file location:

```
_build/default/rel/vernemq/etc/vmq.passwd
```

* `ulimit -n` recommended minimum: `65536`.
* Add more users by repeating the `vmq-passwd` add command.
* For production, consider enabling TLS and proper ACLs.

