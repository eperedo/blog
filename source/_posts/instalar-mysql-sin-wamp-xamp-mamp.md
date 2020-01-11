---
title: Instalar MySql sin WAMP, XAMP, MAMP, etc
date: 2020-01-05 15:00:00
---

TL;DR: Instala docker y ejecuta el siguiente comando

```bash
docker run --name mi_bd -d -p 3306:3306 mysql:5.7
```

ingresar al contenedor

```bash
docker exec -it mi_bd bash
```

conectarte a mysql

```
mysql -p
```
