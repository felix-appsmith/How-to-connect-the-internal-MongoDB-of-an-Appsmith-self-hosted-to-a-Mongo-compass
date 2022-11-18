# How to connect to the internal Mongo database of a self-hosted Appsmith

### What is this repository about?
In this guide I will show a way to connect from MongoCompas to the internal self-hosted MongoDB database.

### What use can make this connection
Making a connection to the internal MongoDB database of the self-hosted can be of great help to find bugs, for example the current [SaaS integrations bug.](https://github.com/appsmithorg/appsmith/issues/18209)
In addition to that, support could be provided with a remote connection to users when they have a problem with this database and thus determine more quickly what is going wrong.


# How to expose Mongo database

In order to expose the internal Mongo database of a self-hosted we will follow these steps

### Steps
1. Enter the command execution of the docker container where Appsmith is
 ```console
 $ docker ps 
 CONTAINER ID   IMAGE                  COMMAND                  CREATED       STATUS                 PORTS                                                                      NAMES
08f8110ee9c0   appsmith/appsmith-ce   "/opt/appsmith/entry…"   2 hours ago   Up 2 hours (healthy)   0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp   appsmith

 $ docker exec -it c2d969adde7a bash 
root@c2d969adde7a:/#
 ```
2. Create a folder called Ngrok and go to that folder
```bash
 mkdir ngrok 
 cd ngrok
```
3. install Ngrok in the container\
```bash
curl -s https://ngrok-agent.s3.amazonaws.com/ngrok.asc | tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null && echo "deb https://ngrok-agent.s3.amazonaws.com buster main" | tee /etc/apt/sources.list.d/ngrok.list && apt update && apt install ngrok
```
4. Get your Ngrok token for this create an account in this [link](https://dashboard.ngrok.com/signup) and the web will give you the token
![2022-11-18_10-18](https://user-images.githubusercontent.com/114161539/202760076-035c672a-369e-4991-9c31-e78635e25bd8.png)

5.  Bind Ngrok to your account with the following command
```bash
ngrok config add-authtoken <token>
```
6. now expose the Mongo database port
```bash
ngrok tcp 27017
```
- Example how to get Ngrok host and port to make connections.
```console
appsmith@ngrok:~$ ngrok tcp 27017
Session Status   
online                                                                                   
Account                       Appsmith-svg (Plan: Free)                                                                  
Version                       3.1.0                                                                                     
Region                        Europe (eu)                                                                               
Latency                       164ms                                                                                    
Web Interface                 http://127.0.0.1:4040                                                                     
Forwarding                    tcp://0.tcp.eu.ngrok.io:16696 -> localhost:27017
Connections                  
ttl     opn     rt1     rt5     p50     p90                                                                            
0       0       0.00    0.00    0.00    0.00                                                                                 
```

- For example, the host and the port to make that connection would be.

`host: 0.tcp.eu.ngrok.io`
`port: 16696`

### How to get Mongo credential

Next we will show how to obtain the connection credentials

1. Find the folder where you have your `docker-compose` file
2. Go to the folder `stacks.`
3. Go to the `configuration` folder
4. Enter the `docker.env` file.
5. In this file look for `APPSMITH_MONGODB_USER`
`Note: performing this search is easier from VS code`
6. Once this file is found, you will see all the information necessary to connect to this Mongo database.
![image (4)](https://user-images.githubusercontent.com/114161539/202760125-aba84a65-5a1c-4b79-a171-f3502054814f.png)


### How to connect Mongo compass

To connect via Mongo compass you can use this URI

1. You only have to put your password and the host and port that Ngrok gave you

`mongodb://appsmith:<<password>>@0.tcp.ngrok.io:19311/?authMechanism=DEFAULT&authSource=appsmith&directConnection=true`
