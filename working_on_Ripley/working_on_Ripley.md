# Ripley

Here is a tutorial to work on the Ripley server at the Institut de recherche en biologie végétale (IRBV).

## Working on Ripley 

### How to connect to Ripley

To connect on Ripley, open a terminal window and enter the followong command:

```sh
ssh [your-user-name]@ripley-irbv.irbv.umontreal.ca
```

It could be useful to change the shell to make the syntax look nicer on the screen. To do so, use this command:

```sh
$ which bash                                                
/usr/bin/bash
$ chsh -s /usr/bin/bash
```

### Transfering files

I strongly suggest that you install a file transfer software to transfer your files to the cluster. One useful software that is cross-platform is FileZilla. You have to use port 22 to transfer files.


### Working with conda

When working on Ripley, it is important to work in a virtual environment. This allows to install softwares in this environement and makes sure that you will not be affecting other users.

Here are the main commands:

```sh
# Create a virtual environment named ipyrad
conda create -n ipyrad
# Enter in the virtual environment
conda activate ipyrad
# Exit the environment
conda desactivate
```

## Some useful info for working effectively

### Usage of the computer

To see the usage of the computer (if other users are currectly using it):

```sh
htop
```

To quit htop, just type ```q```.


### Working with screens

When working on Ripley through a terminal window, closing the connection will terminate the processes running on ipyrad. To avoid this, it is possible to work with screens. The idea is to open a new screen and send commands in this screen. Then, you can close this screen and the processes will continue to run. You can reconnect to the screen at any time to access to the past commands and processes.

To start a new Screen session, type the following in your terminal:

```sh
screen
```

Named sessions are useful when you run multiple screen sessions at the same time. To create a named session, run:

```sh
screen -S session_name
```

You can detach from the screen session at any time by pressing:

```sh
Ctrl+a d
```

The programs running inside the session continue to run after you detach. You can safely close your terminal or disconnect your SSH session. To resume a detached session, run:

```sh
screen -r
```

If you have multiple screen sessions running, you need to specify the session ID. To list all running sessions, run:

```sh
screen -ls

There are screens on:
    10835.pts-0.linuxize-desktop   (Detached)
    10366.pts-0.linuxize-desktop   (Detached)
2 Sockets in /run/screens/S-linuxize.
```

To reattach to a specific session, pass its ID to the -r option:

```sh
screen -r 10835
```

If a session is still marked as (Attached), for example after an unexpected disconnect, use -d -r to detach it first and then reattach:

```sh
screen -d -r 10835
```

More information on scree, you can look at [the documentation here](https://linuxize.com/post/how-to-use-linux-screen/).


### Where to store the data

Large datafiles should be placed in the data disk. For the Joly lab, it should go in this folder: ```/data/joly_data```


## Create users (admin only)

### create user and home directory

```sh
sudo useradd -m khader
```

### create password

```sh
sudo passwd khader
```


