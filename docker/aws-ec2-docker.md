# AWS and docker

## How to create EC2 instance on AWS and connect it with ssh

Prerequisite:

- You have an account on AWS
- Use AWS within free-tire
- Create EC2 instance and later use it for docker and kubernates

### Create EC2 instance

1. Open EC2 page on AWS
2. Click `Instances` on your sidebar
3. Click `Lanch Instances`
4. Choose Amazon Machine Image and click `Select` (Here I choose Ubuntu Server 18.04 LTS (HVM), SSD Volume Type. 64bit (x86))
5. Choose an Instance Type (I choose `t2.micro`) and click `Next: COnfigure Instance Detail`
6. Click `Next: Add Storage`
7. Change the `Size` to `20`. (To use docker or kubernates we need more space)
8. Click `Review and Launch`
9. Click `Edit security groups`
10. Click `Add Rule` and choose `HTTP` as a Type (As default setting you only can access it with ssh)
11. Click `Add Rule` and choose `HTTPS` as a Type
12. Click `Add Rule` and choose `Custom TCP Protocol` as a Type and set `8080-8082` in the Prot Range
13. Click `Add Rule` and choose `Custom TCP Protocol` as a Type and set `30000` in the Prot Range
14. Click `Review and Launch` (If an error is retuned, add `0.0.0.0/0, ::/0` to the `Srouce`)
15. Click `Launch`
16. Choose `Create a new key pair` and type key pair name `docker_ec2` (This is a password that you need to connect with ssh later)
17. Click `Download Key Pair` (You only can download this now)
18. Click `Launch`
19. Click `View Instance`

### Connect with ssh

1. Click `Instance ID`
2. Note `Public IPv4 adress` (This adress and `Public IPv4 DNS` are access points)
3. Move directory to the place where you saved the key pair file.
4. Change the access right to read only `chmod 400 docker_ec2.pem`
5. Connect to the EC2 instance `ssh ubuntu@XXX.XXX.XXX.XXX -i docker_ec2.pem` (Swap XXX.XXX.XXX.XXX with your`Public IPv4 adress`) \*When you stop EC2 instance and reraunch it, the IP adress will be changed.
6. Type `yes` to this `Are you sure you want to continue connecting (yes/no/[fingerprint])? yes`
7. Now you're connected to EC2 instance with ssh

Port:

- 22 for ssh connection
- 80 for Web(http)
- 443 for Web(https)
- Use 8080, 8081 and 8082 for Web(test)
- User 30000 for kubernates

#### What is EC2 instance

#### What is Amazon Machine Image

It's simply an OS which is installed on the machine.

#### What is an Instance Type

It's the spec(CPU or memory) of the EC2 instance.

### How to installe Docker

[Install Docker to ubuntu](https://docs.docker.com/engine/install/ubuntu/)

1. Update apt package index

```
sudo apt-get update
```

2. Install package to allow `apt` to use repository over HTTPS.

```
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

3. Add docker's official GPG key (Gnu Private Guard is something that you can check if the file is authentic)

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

4. Add docker's download site into apt repository

```
echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

5. Update apt packege index again

```
sudo apt-get update
```

6. Install docker

```
 sudo apt-get install docker-ce docker-ce-cli containerd.io
```

7. Check

```
docker --version
```

Docker version 20.10.6, build 370c289

8. (Optional) Allow ubuntu user to use docker
   You can user docker without sudo command

```
sudo gpasswd -a ubuntu docker
```

## How to build web server with docker

1. Check Docker Image name.
   You need to go to [Docker Hub](https://hub.docker.com/) and search image. We are going to install apache so search `apache`. And you will see apache's official image name:`httpd`.

2. Run docker without `Docker.file`

```
sudo docker run -dit --name my-apache-app -p 8080:80 -v "$PWD":/usr/local/apache2/htdocs/ httpd:2.4
```

Now docker container with Apache is running.

3. Check status

```
sudo docker ps
```

`STATUS` should be `UP` now.

`ps` command is an old command, alternatively you can use command below.

```
sudo docker container ls
```

3. Check it with web browser

Access to `http://XXX.XXX.XXX.XXX:8080/` (Swap XXX.XXX.XXX.XXX with your`Public IPv4 adress`)

You might see the current directory of Ubuntu.
This should be avoided for the proper use.

4. Create index.html

```
nano index.html
```

And type something like this:

```
<html>
    <body>
        <div>Hello world</div>
    </body>
</html>
```

Save it.

- ctr + x
- Y
- [Enter]

5. Refresh web broweser

You'll see `Hello world` in the brower.

6. Stop docker container

```
sudo docker stop my-apache-app
```

To stop docker container, you can specify the container with the name or container ID. You can check them with `docker ps' command.

7. Refresh web broweser

You can not access to the web page anymore.

8. Check docker container's status

```
sudo docker ps -a
```

The STATUS should be `EXTENDED`.

9. Restart the containre

```
sudo docker start my-apache-app
```

10. Check log

```
sudo docker logs my-apache-app
```

11. Dispose docker container

The container has to be stopped before you remove it.

```
sudo docker stop my-apache-app
```

Delete the container

```
sudo docker rm my-apache-app
```

12. Dispose the doceker image

You can check what image was installed in the container.

```
sudo docker image ls

-- return --
REPOSITORY   TAG       IMAGE ID       CREATED      SIZE
httpd        2.4       0b932df43057   8 days ago   138MB
```

Delete docker image

```
sudo docker image rm httpd:2.4
```

Image name is `REPOSITORY` + `TAG`.
You also may ommit the `TAG` part. Then latest version will be delted.

13. Exit from the server

```
exit
```

## Stop EC2 instance on AWS

1. Open EC2 instance's page
2. Click `Actions` dropmenu
3. Click `Manage instance state`
4. Choose `Stop` in `Instance state settings`
5. Click `Change state`
