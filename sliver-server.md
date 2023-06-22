# How To Create a Listener Server Using Sliver


This tutorial will cover how to setup a [Sliver](https://github.com/BishopFox/sliver) host  (*Tested with Ubuntu 20.04 but should work with other distributions*)

## Linux One-Liner Installation
To get started, the simplest way is to utilize their 'one-liner' installation.

Simply go to any terminal with elevated permissions and run

`curl https://sliver.sh/install|sudo bash`

This should begin the installation process for whatever version of Linux you are using.

> **Note: The Sliver host *can* be a Windows machine, but Sliver themselves advise against it as some features will not work as expected**

Before you run the service, you may want to install `mingw-w64` to allow DLL payloads and shellcode to be deployed.

To run the service, simply type `sliver` into your terminal and it should start up. 
