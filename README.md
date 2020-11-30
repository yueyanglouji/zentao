# Docker Image for Zentao

update to 12.5.stable  2020.11.30

update to 12.4.4 -

DockerHub:[https://hub.docker.com/r/yueyanglouji/zentao/](https://hub.docker.com/r/yueyanglouji/zentao/)

Office Support:[http://www.zentao.net/](http://www.zentao.net/)
### Tags

**Open soure edition**

- `12.3.2`,`latest`
- `12.0`,`12.0.1`,`12.3`
- `11.3`,`11.4.1`,`11.5.1`,`11.6`,`11.6.1`,`11.6.5`,`11.7`
- `10.0`,`10.1`,`10.3`,`10.4`,`10.5`,`10.6`
- `9.6.3`,`9.7`,`9.8`,`9.8.3`

**Pro edition**

- `pro-8.7.1`,`pro`
- `pro-8.2`,`pro-8.3`,`pro-8.4`,`pro-8.5.1`,`pro-8.5.3`
- `pro-7.1`,`pro-7.3`,`pro-7.5.1`
- `pro-6.7.3`

### QuickStart

open soure edition:
``` bash
mkdir -p /data/zbox && \
docker run -d -p 80:80 -p 3306:3306 \
        -e ADMINER_USER="root" -e ADMINER_PASSWD="password" \
        -e BIND_ADDRESS="false" \
        -v /data/zbox/:/opt/zbox/ \
        --add-host smtp.exmail.qq.com:163.177.90.125 \
        --name zentao-server \
        idoop/zentao:latest
```

pro edition:
``` bash
mkdir -p /data/zbox && \
docker run -d -p 80:80 -p 3306:3306 \
        -e USER="root" -e PASSWD="password" \
        -e BIND_ADDRESS="false" \
        -v /data/zbox/:/opt/zbox/ \
        --add-host smtp.exmail.qq.com:163.177.90.125 \
        --name zentao-server-pro \
        idoop/zentao:pro
```

Note: Make sure your Host feed available on either port `80` or `3306`.

### Environment configuration

* `ADMINER_USER` : set the web login database Adminer account.
* `ADMINER_PASSWD` : set the web login database Adminer password. 
* `BIND_ADDRESS` : if set value with `false`,the MySQL server will not bind address.
* `SMTP_HOST` : set the smtp server IP and host.(If can't send mail,it will be helpful.) Can also use `extra_host` in docker-compose.yaml,or use param `--add-host` when use `dokcer run` command.

Note: The Zentao administrator account is **admin**,and default initialization password is **123456**.
      And MySQL root account password is **123456**,please change password when you first login.

### Upgrade Version

> If you want upgrade the zentao version, just run a container with the latest docker image and mount the same zbox path `$volume/zbox/`.

``` bash
# stop and backup old container
docker stop zentao-server
docker rename zentao-server zentao-server-bak
# backup zbox
cp -r /data/zbox /data/zbox-bak
# pull the latest image
docker pull idoop/zentao:latest
# run new container with the latest image and mount the same path
docker run -d -p 80:80 -p 3306:3306 \
        -e ADMINER_USER="root" -e ADMINER_PASSWD="password" \
        -e BIND_ADDRESS="false" \
        -v /data/zbox/:/opt/zbox/ \
        --add-host smtp.exmail.qq.com:163.177.90.125 \
        --name zentao-server \
        idoop/zentao:latest
docker logs -f zentao-server
```
You will see the upgrading process logs like following.
```
Installed Zentao version: 11.0
New Zentao version: 11.4
Backuping config/my.php and upload ...
Upgrading Zentao ...
Restoring config/my.php and upload ...
Upgraded Zentao version to: 11.4
Please visit your Zentao website to complete the upgrade task.
ZBOX是Apache、Mysql、PHP的精简的集成环境。使用时，需要将其解压到/opt目录。

/opt/zbox/zbox -h     可以获取帮助
/opt/zbox/zbox start  启动脚本

Mysql 用户名root，密码为123456。

更多可以访问http://www.zentao.net/goto.php?item=zbox.
Start Apache success
Start Mysql success
Start xxd success
```

Wait until `Start xxd success`, visit your zentao website to complete the upgrade task step by step.
After you complete the upgrade task in your zentao website and confirm everything looks good, delete the backups to save your disk space.`docker rm -f zentao-server-bak && rm -rf /data/zbox-bak`
> [See Detail](https://www.zentao.net/book/zentaopmshelp/67.html)

### Building the image

Clone this repo, modify `Dockerfile` or `docker-entrypoint` if you want.
Then execute the following command:

``` bash
docker build -t zentao .
```

### FAQ
upgrade or auto remove plugins when restart?

added `ZENTAO_VER` env when u startup (e.g. `-e ZENTAO_VER="12.3.stable"`, because Dockerfile's `ZENTAO_VER` (that is `12.3.2`) not eq `cat /opt/zbox/app/zentao/VERSION` (that is `12.3.stable` ))

```bash
docker run -d -p 80:80 -p 3306:3306 \
        -e ADMINER_USER="root" -e ADMINER_PASSWD="password" \
        -e BIND_ADDRESS="false" \
        -e ZENTAO_VER="12.3.stable" \
        -v /data/zbox/:/opt/zbox/ \
        --add-host smtp.exmail.qq.com:163.177.90.125 \
        --name zentao-server \
        idoop/zentao:latest
```
