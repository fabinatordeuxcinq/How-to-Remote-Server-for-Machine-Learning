# How to : Remote Server for Machine Learning
A guide to use remote server for machine learning


## Config file
Let's say you just arrived in a compagny, they gave you your username and password and now 
you can connect through ssh : 

```console
personal_computer~: ssh username@ssh.compagny.com
```

It will ask you for your password every time you will connect. 

To avoid this, we can create a config file name «config» in the __.shh__ directory.

config : 
```
Host name_you_give
  HostName ssh.compagny.com
  User username

```
Now, you can simply do : 

```
ssh name_you_give
```
Now, let's suppose their is remote server that you can only access trough the server of your 
compagny. 

You don't want to, connect to your compagny server, then connect to the remote server from there. 

Edit your config file : 

```
Host remote_name
  ProxyJump name_you_give
  HostName hostname_of_remote
  User username
```

Now you can juste do : 

```
ssh remote_name
```

## Transfer file 

Ok, you that have access to server easily, you need to be able to transfer data from your computeur 
to the remote server.

To do so, you can use 
```
scp -r source/path/file remote_name:/destination/path/file 
```
The __-r__ option (for recursive) allow to transfer directories and every thing they contain. 

To transfer data form the remote server to your computeur, use : 
```
scp -r remote_name:/source/path/file destination/path/file
```

## Coding on remote server with VS code 

You can use VS code to connect to the remote server. 

To do so, there is this extensions : 

![image](https://user-images.githubusercontent.com/90333559/235730760-e2bdfe51-aafd-4a39-aead-0948077952a5.png)

Then, hit __ctrl + Maj + p__ and select __Remote-SSH : Connect Current Window to Host__

![image](https://user-images.githubusercontent.com/90333559/235731162-ba567084-2b30-4336-9c54-82639cf7a4df.png)


Then it will let you select the remote sever you define in your config file. 

## Managing file graphicaly with Nautilus. 

Nautilus, which is the name of the default file browser in Ubunutu, let you easily connect to distant server through ssh.

![image](https://user-images.githubusercontent.com/90333559/235732156-5fce2d94-723b-4e4b-a201-8964eac41d5a.png)


