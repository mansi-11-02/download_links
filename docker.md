**Launch instance - dockerhost**
```yml
yum update -y
yum install docker -y
systemctl start, enable, status docker
```
1.Using Dockerfile:

```yml
 docker pull ubuntu:latest
 systemctl start docker
systemctl enable docker
 docker pull ubuntu:latest
 vim Dockerfile.ap2
```
paste the contents in the file
```yml
# Use the official Ubuntu image from Docker Hub
FROM ubuntu:latest

# Update the package list and install Apache
RUN apt-get update && apt-get install -y apache2

# Expose port 80
EXPOSE 80

# Start Apache in the foreground
CMD ["apachectl", "-D", "FOREGROUND"]
```
```yml
vim Dockerfile.ng
```
paste the contents in the file
```yml

# Use the official Ubuntu image from Docker Hub
FROM ubuntu:latest

# Update the package list and install Nginx
RUN apt-get update && apt-get install -y nginx

# Expose port 90
EXPOSE 90

# Start Nginx in the foreground
CMD ["nginx", "-g", "daemon off;"]
```
```yml
 docker build -t my-appache-app -f Dockerfile.ap2 .
 docker build -t my-nginx-app -f Dockerfile.ng .

 docker run -d -p 9090:90 my-nginx-app
 docker run -d -p 8080:80 my-ap2-app
```
check : http://ip-address:8080
        http://ip-address:9090
**********************************************************************
2. Do it manually by entering each code
```yml
docker info
docker pull httpd
docker pull ubuntu
docker image
docker ps -a
docker run -it --name web-app -p 7070:80 ubuntu:latest /bin/bash  ( Create a container)
```

You will directly enter the container if not run : docker start webapp and docker attach web-app
In the container:
```yml
apt update -y
apt install apache2 -y
cd /var/www/html
cat > index.html
cd 
service apache2 start
exit
curl http://ip-address
```
```yml
docker images
docker rmi -f <image-id>
```
