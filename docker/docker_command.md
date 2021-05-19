# Docker Command

## docker run

docker run is a combination of

- docker pull
- docker create
- docker start

Ex(apache):

```
docker run -dit --name my-apache-app -p 8080:80 -v "$PWD":/usr/local/apache2/htdocs/ httpd:2.4
```

This is equal to the code above.

```
docker pull httpd:2.4
docker create --name my-apache-app -p 8080:80 -v "$PWD":/usr/local/apache2/htdocs/ httpd:2.4
docker start my-apache-app
```

## docker pull

pull(download) an image

```
// download apache into docker host
// when the tag is not specified, it installs latest version
docker pull httpd

// you also can specify the tag(version)
docker pull httpd:2.4
```

## docker image

```
// you can check image that you downloaded.
docker image ls
```

```
// delete the image
docker rm httpd:2.4
```

## docker port

Check the port mapping

## doker start , docker stop , docker kill

## docker exec

When you want to control inside running container

## docker container prune / docker image prune

remove all the stopped containters / images

```
// execute shell inside the container
docker exec --it my-apceh-app /bin/sh
```

## docker cp

Copy a file

From Docker host -> Container

`docker cp <options> <path of host> <container name or ID>:<path of container>`

```
docker cp

```

From Container -> Docker host
`docker cp <options> <container name or ID>:<path of container> <path of host>`

```

```

This command cannot copy system file under proc/ sys/ dev/ or tempfs/

## docker volume

bind mount vs volume mount

## bind mount

This is something that you use directory of server.
This shares directory between server and conntainer

Use this for

- config file

### volume mount

mount an area on Docker Engine. This area called volume or data volume.

Use this for

- database

- docker volume create
- docker volume inspect
- docker volume ls
- docker volume prune
- docker volume rm

## Important option

### --name

Name the container

```
// Name the container as `my-apache-app`
docker run -dit --name my-apache-app -p 8080:80 -v "$PWD":/usr/local/apache2/htdocs/ httpd:2.4
```

### --p (--publish)

set port

```
// Docker host's TCP port 8080 is connected to continaer 80.
docker run -dit --name my-apache-app -p 8080:80 -v "$PWD":/usr/local/apache2/htdocs/ httpd:2.4
```

`https://<docker's host IP address>:8080`

### -v (--volume)

mount the volume

```
// mount "$PWD" onto /usr/local/apache2/htdocs/ directory
// "$PWD" is an environment variable for current directory
docker create --name my-apache-app -p 8080:80 -v "$PWD":/usr/local/apache2/htdocs/ httpd:2.4
```

If you need to keep data persist, you can mount a directory and keep the data there.
Or basically you should put persist data outside of container. Because if the container is deleted, the contens are also removed.

### -dit

runnging process in the background

-dit is a combination of three options

- -d : running background
- -i : Interactive mode. If you don't specify this, you can not control the container with your keyboard
- -t : Pseudo-tty. This allows you to use your `Ctrl` or `esc` key.

```

// apache is running background
docker run -dit --name my-apache-app -p 8080:80 -v "$PWD":/usr/local/apache2/htdocs/ httpd:2.4
```
