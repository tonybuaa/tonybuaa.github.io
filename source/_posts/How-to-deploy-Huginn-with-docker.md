---
title: How to deploy Huginn with docker
date: 2018-12-24 16:04:40
tags:
- huginn
- docker
- vps
categories: Technology
lang: en
---

1. Deploy a huginn server with docker:

    ``` bash
    docker run -it -p 3000:3000 -v /home/huginn/mysql-data:/var/lib/mysql huginn/huginn
    ```
    If following error is observed:
    ``` bash
    bootstrap stderr | mv:  bootstrap stderr | cannot create directory ‘/var/lib/mysql/mysql’ bootstrap stderr | : Permission denied bootstrap stderr |
    bootstrap stderr | mv:  bootstrap stderr | cannot create directory ‘/var/lib/mysql/performance_schema’ bootstrap stderr | : Permission denied bootstrap stderr |
    ```
    then run:
    ``` bash
    chown 1001:1001 mysql-data/
    ```

2. Open Huginn in the browser `http://<server-ip>:3000`

3. Log in to your Huginn instance using the username  `admin`  and password `password`

4. Modify .env file in container:
    ``` bash
    docker exec -it mystifying_buck bash
    vi /app/.env
    ```
    Make change to following settings:
    ```
    ENABLE_INSECURE_AGENTS="true"
    TIMEZONE="Beijing"
    ```
    Save and exit container.
5. Restart container to take effect:
    ``` bash
    docker container restart mystifying_buck
    ```