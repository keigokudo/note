# Create volume mount

Things to do:

- mount volume for mysql
- create a container
- dispose the container
- check if the data is persistant

## Process

1. Create volume

```
docker volume create mysqlvolume
```

2. Check it

```
docker volume ls

// result
DRIVER    VOLUME NAME
local     mysqlvolume
```

3. Create mysql container

```
docker run -dit --name db01 --mount type=volume,src=mysqlvolume,dst=/var/lib/mysql -e MYSQL_ROOT_PASSWORD=mypassword mysql:5.7
```

Explanations:

```
// this command combines `docker create`, `docker pull` and `docker start`
docker run

// running background and interactive mode
-dit

// name the container as `db01`
--name db01

// specify mount type as volume (other option is `bind`)
// connect src(your server) and dst(your container)
--mount type=volume,src=mysqlvolume,dst=/var/lib/mysql
// you also can just bind with `-v` but this is safer

// -e is environment variable
// pass root users's initial password to mysql
-e MYSQL_ROOT_PASSWORD=mypassword mysql:5.7
```

4. Get inside the container

```
docker ex -it db01 /bin/bash
```

5. Execute mysql comman

```
mysql -p
```

You need -p because you have to type the password

6. Create database

```
CREATE DATABASE exampledb
```

7. Create table

```
CREATE TABLE exampletable (id INT NOT NULL AUTO_INCREMENT, name VARCHAR(50), PRIMARY KEY(id));
```

8. Add some data

```
INSERT INTO exampletable (name) VALUES ('user1');
INSERT INTO exampletable (name) VALUES ('user2');
```

9. Check it

```
SELECT * FROM exampletable;

// return
+----+-------+
| id | name  |
+----+-------+
|  1 | user1 |
|  2 | user2 |
+----+-------+
```

10. Exit from mysql

```
\q
```

11. Exit from docker container

```
exit
```

12. Dispose docker container

```
docker stop db01
docker rm db01
```

13. Recreate docker container

```
docker run -dit --name db01 --mount type=volume,src=mysqlvolume,dst=/var/lib/mysql -e MYSQL_ROOT_PASSWORD=mypassword mysql:5.7
```

14. Get inside the container again

```
docker exec -it db01 /bin/bash
```

15. Check db

```
// type your password
mysql -p
use exampledb;
select * from exampletable;

// return: data is not deleted!
+----+-------+
| id | name  |
+----+-------+
|  1 | user1 |
|  2 | user2 |
+----+-------+

```

16. Exit

```
// exit from db
\q

// exit from container
exit
```

17. Dispose the container again

```
docker stop db01
docker rm db01
```

## How to do volume backup

Where is the data mounted?

```
root@ip-xxx-xxx-xxx-xxx:/home/ubuntu# ls -a
// return (you can not find where it is)
.  ..  .bash_history  .bash_logout  .bashrc  .cache  .gnupg  .local  .profile  .ssh  .sudo_as_admin_successful  index.html  web01data
```

```
docker volume inspect mysqlvolume

// return

[
    {
        "CreatedAt": "2021-04-25T15:21:38Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/mysqlvolume/_data",
        "Name": "mysqlvolume",
        "Options": {},
        "Scope": "local"
    }
]
```

1. Check that docker container is not running

```
docker ps -a
```

2. Back up with tar

```
docker run --rm -v mysqlvolume:/src -v "$PWD":/dest busybox tar czvf /dest/backup.tar.gz -C /src .
```

You can create back up as `backup.tar.gz`.

busybox is a light weight of linux. You can use basic commands of linux.

```
ubuntu@ip-xxx-xxx-xx-xx:~$ ls -a
.  ..  .bash_history  .bash_logout  .bashrc  .cache  .gnupg  .local  .profile  .ssh  .sudo_as_admin_successful  backup.tar.gz  index.html  web01data
```

3. Check inside of the file

```
tar tzvf backup.tar.gz
```

tar options

````
DESCRIPTION
     -c      Create a new archive containing the specified items.
     -x      Extract to disk from the archive.  If a file with the same name appears more than once in the archive, each copy
             will be extracted, with later copies overwriting (replacing) earlier copies.
     -t      List archive contents to stdout.

OPTIONS
     -z      (c mode only) Compress the resulting archive with gzip(1).  In extract or list modes, this option is ignored.  Note
             that, unlike other tar implementations, this implementation recognizes gzip compression automatically when reading
             archives.
     -v      Produce verbose output.  In create and extract modes, tar will list each file name as it is read from or written to
             the archive.  In list mode, tar will produce output similar to that of ls(1).  Additional -v options will provide
             additional detail.
     -f file
             Read the archive from or write the archive to the specified file.  The filename can be - for standard input or
             standard output.

             ```
````

## How to restore from the backup file

1. Delete the volume

```
docker volume rm mysqlvolume
```

2. Check it

Nothing is here.

```
docker volume ls
DRIVER    VOLUME NAME
```

3. Create volume

This done not have to be the same name(mysqlvolume)

```
docker volume create mysqlvolume
```

4. Check it

```
docker volume ls

// result
DRIVER    VOLUME NAME
local     mysqlvolume
```

5. Restore the volume from backup

```
docker run --rm -v mysqlvolume:/dest -v "$PWD":/src busybox tar xzf /src/backup.tar.gz -C /dest
```

## option

--volumes-from
