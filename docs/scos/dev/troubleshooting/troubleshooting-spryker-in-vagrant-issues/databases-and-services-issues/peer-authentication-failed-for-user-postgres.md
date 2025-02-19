---
title: Peer authentication failed for user postgres
description: Learn how to fix the issue Peer authentication failed for user postgres
last_updated: Jun 16, 2021
template: troubleshooting-guide-template
originalLink: https://documentation.spryker.com/2021080/docs/peer-authentication-failed-for-user-postgres
originalArticleId: 62007cfd-f1d2-4cd8-ade0-47a440cfdebf
redirect_from:
  - /2021080/docs/peer-authentication-failed-for-user-postgres
  - /2021080/docs/en/peer-authentication-failed-for-user-postgres
  - /docs/peer-authentication-failed-for-user-postgres
  - /docs/en/peer-authentication-failed-for-user-postgres
  - /v6/docs/peer-authentication-failed-for-user-postgres
  - /v6/docs/en/peer-authentication-failed-for-user-postgres
---

## Description

When running `./setup`, on the `Setup Zed` step, the following error occurs:

```php
Zed Exception: RuntimeException - psql: FATAL:  Peer authentication failed for user "postgres"
```

## Solution

Open the PostgreSQL configuration file :

```bash
sudo vim /etc/postgresql/9.4/main/pg_hba.conf
```

and make sure that the first line contains the following input:

```php
TYPE      DATABASE    USER      ADDRESS     METHOD
local     all         postgres              trust
```
