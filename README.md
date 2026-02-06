# DOMjudge Setup Guide

This repository provides a simple guide to quickly set up **DOMjudge** for small to medium programming contests.

## What is DOMjudge?

**DOMjudge** is a contest management system that allows you to run programming competitions (ICPC-style).

With a single host machine (e.g., a reasonably powerful laptop), you can easily handle **~30 participants or more**.

## Hardware Recommendation

* A laptop/server with good CPU and RAM
* A local network (router/modem) for participants to connect
* Ubuntu 22.04 (tested environment)

> ⚠️ These commands have been tested on **Ubuntu 22.04**

---

## Step 1 — Start Required Containers

First, run the following three Docker containers:

### 1) Database (MariaDB)

```bash
docker run -it --name dj-mariadb \
  -e MYSQL_ROOT_PASSWORD=rootpw \
  -e MYSQL_USER=domjudge \
  -e MYSQL_PASSWORD=djpw \
  -e MYSQL_DATABASE=domjudge \
  -p 13306:3306 \
  mariadb \
  --max-connections=1000 \
  --max-allowed-packet=256M \
  --innodb-log-file-size=512M
```

### 2) DOMserver

```bash
docker run --link dj-mariadb:mariadb -it \
  -e MYSQL_HOST=mariadb \
  -e MYSQL_USER=domjudge \
  -e MYSQL_DATABASE=domjudge \
  -e MYSQL_PASSWORD=djpw \
  -e MYSQL_ROOT_PASSWORD=rootpw \
  -p 12345:80 \
  --name domserver \
  domjudge/domserver:latest
```

### 3) Judgehost

```bash
docker run -it \
  --privileged \
  --cgroupns=host \
  -v /sys/fs/cgroup:/sys/fs/cgroup \
  --name judgehost-0 \
  --link domserver:domserver \
  --hostname judgedaemon-0 \
  -e DAEMON_ID=0 \
  -e JUDGEDAEMON_PASSWORD=1k0Wx02CW9OG/VM/Do453iYCQwprJqC7 \
  domjudge/judgehost:latest
```

---

## Step 2 — Access the Web Interface

After starting the containers:

Open your browser and go to:

```
http://localhost:12345
```

You should see the DOMjudge panel.

Login to the **admin panel** using the credentials printed in the server terminal.

---

## Step 3 — Create Contest & Problems

From the admin web interface you can:

* Add problems
* Create contests
* Manage users/teams
* Configure submissions

Most of these steps are straightforward using the GUI.

---

## Step 4 — Let Participants Connect

To allow contestants to join:

1. Bring a router/modem
2. Make sure it supports **30+ users**
3. Connect the server laptop to the router
4. Ask participants to connect to the same network
5. Share the server **IP address + port (12345)**

Example:

```
http://192.168.1.10:12345
```

Participants can now access the contest and start submitting solutions.

---

## Notes

* One judgehost is usually enough for small contests
* For heavier loads, you can add more judgehosts
* Make sure Docker is installed before starting

---
