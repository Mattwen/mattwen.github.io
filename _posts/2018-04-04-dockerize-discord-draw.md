---
layout: post
title: Dockerize our Node.js App and Deploy it!
---

Want to deploy your app more quickly? No need to install dependencies of Node when Docker image can handle it all for you!

Here's an example of an app we will deploy: [http://draw.mwenger.io](http://draw.mwenger.io)
<br>
<hr>
(If you already have Docker you can skip to the end)

# Install Docker
```sh
wget -qO- https://get.docker.com | sh
```
Add your username to Docker group
```sh
sudo usermod -aG docker matt
```
Make sure to logout and log back in
```sh
exit
su matt
```
Verify version of Docker
```sh
docker --version
Docker version 18.03.0-ce, build 0520e24
```

<hr>

# Install npm & Node.js (Ubuntu 16.04)
```sh
sudo apt-get update
sudo apt-get install nodejs -y
sudo apt-get install npm -y
```
Verify the version of Node.js 
```sh
nodejs -v
v4.2.6
```
# Clone your Project from Github and install npm Dependencies
```sh  
cd ~
git clone https://github.com/mattwen/discord-draw && cd discord-draw
npm install
```
# Create the Dockerfile

With any Text Editor add the following:
```sh
# /home/matt/discord-draw/Dockerfile
# Gets the latest image of node
FROM node:carbon
    
# Create app directory
WORKDIR /usr/src/app

# Install app dependencies
# A wildcard is used to ensure both package.json AND 
# package-lock.json are copied
COPY package*.json ./

RUN npm install
# If you are building your code for production
# RUN npm install --only=production

# Bundle app source
COPY . .
# Run on port 8080
EXPOSE 8080
CMD [ "npm", "start" ]
```
# Create .dockerignore

Create a .dockerignore with the following and save
```sh
node_modules
npm-debug.log
```
# Build the Docker image

Build the image with the name 'mattwen/discord-draw' and a tag
```sh
docker build -t mattwen/discord-draw:latest .
Successfully built 76a4c762151b
Successfully tagged mattwen/discord-draw:latest
```
# Run our Image
    
This will run the docker container on port 8080, container will restart on reboot
```sh
docker run --restart=always -p 8080:8080 -d mattwen/discord-draw:latest
```
# Verify it's Working

Should return an HTML response
```sh
curl 0.0.0.0:8080
```
# (Optional) Push to Public Docker Hub

Log into Docker and enter your credentials, register an account if you don't have one [here](https://hub.docker.com/) 
```sh
login docker
Username: mattwen
Password: 
Login Succeeded
```
Push to the public Docker repo, where :latest is the tag name
```sh
docker push mattwen/discord-draw:latest
```
<hr>

# Docker 2-Step Deploy

If you already have Docker installed on a VM, you can install this image in two commands
```sh
docker pull mattwen/discord-draw:latest
docker run --restart=always -p 8080:8080 -d mattwen/discord-draw:latest
```
Visit \<host_ip_address\>:8080 to verify it's working! Make sure you create a host exception for tcp:8080
