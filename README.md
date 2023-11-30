# How to : Remote Server for Machine Learning
A guide to use remote server for machine learning


## Config file
Let's say you just arrived in a compagny, they gave you your username and password and now 
you can connect through ssh : 

```console
~: ssh username@ssh.compagny.com
```


To avoid tapping username@ssh.compagny.com every time you will connect, we can create a config file name «config» in the __.shh__ directory.

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
## Password 

Entering your password evrytime you want to connect is annoying, this can be by : 

Creating private/public key : 
```console
ssh-keygen -t rsa
```
Now you can do : 
```console
ssh-copy-id username@ssh.compagny.com
```
It will ask for your password one last time. 

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
![image](https://user-images.githubusercontent.com/90333559/235734416-a6aa8771-f7ee-4442-96ec-cf0e2fecafed.png)



Then it will let you select the remote sever you define in your config file. 

## Managing file graphicaly with Nautilus. 

Nautilus, which is the name of the default file browser in Ubunutu, let you easily connect to distant server through ssh.

![image](https://user-images.githubusercontent.com/90333559/235733988-ef1de286-1705-4fa1-b67f-c8ccaa7e3c6d.png)


## TensorBoard

If you are using TensorBoard to track the training of your model, to be able to vizualize it in real time, you need to do :

1) One remote Server (named remote_name in your config file) : 

```
python3 -m tensorboard.main --logdir path/to/logs
```
It will open your the tensorboard on a port (here let's say 6006)

Then on your local machine : 

```
command = "ssh -N -f -L localhost:16006:localhost:6006 remote_name"
```
Then you will be able to visualize your tensorboard on : __http://localhost:16006/__

## Screen command 

If you running commands on the distant server and then close your terminal, it will stop the execution of your program. 

We obiously don't want to wait until our training is done to shut down our local computer, this is where the __screen__ command takes place. 

Let's say you want to lauch your trainining script. 

First, you will need to create a screen session : 

```
screen -S Training
```

Then you be automaticly inside this session. You can run your script : 

```
python3 training_script.py 
```

And now hit __ctrl + a__ then __ctrl + d__ to quit the session. 

You will come back to your terminal in the state it was before you calling screen, and you can close it. 

```
[detached from XXXXXX.Training]
```

To see your opened session : 

```
screen -list 
```
```
There is a screen on:
	XXXXXX.Training	(00/00/0000 00:00:00)	(Detached)
1 Socket in /run/screen/S-username.
```

To retach to your session when it's detached: 
```
screen -r Training 
```
Sometime, if you close your terminal to early, (before hitting __ctrl + a__ __ctrl + d__), your session can stay attached, and then
__screen -r__ will not work. 

You will need to do :

```
screen -x Training
```

One you are done with your training, simply type __exit__ to close the session. 

## Check GPU availability 

If There are multiple GPUs on the remote server, you may want to be sure to select one 
that are not used by an other person. Ideally, your program will take as argument the device id (which can be "cpu" or 0,1,2 ...), 
but you still need to check manually which GPU is available before running your script. 
It will happen that your forget causing in the best case, the well known CUDA OUT OF MEMORY, and in the worse
your program will execut on the same GPU, which is slow. 

What i use is this simple gpu_utils file : 

```python
from pynvml.smi import nvidia_smi

class AlreadyInUseGPUError(Exception) :
    def __init__(self, device) -> None:
        message = f"Already in use GPU : {device}. Please use other GPU."
        super().__init__(message)

def is_device_in_use(device) :
    if device == "cpu" :
        return False
    return nvidia_smi.getInstance().DeviceQuery()["gpu"][device]["processes"] is not None

def list_not_in_use_gpu() :
    nv = nvidia_smi.getInstance().DeviceQuery()["gpu"]
    total = len(nv)
    l = [idx for idx in range(total) if nv[idx]['processes'] is None]
    return l
```
