Introduction

You should do this preliminary exercise to familiarise yourself with UDP socket programming. It is an example of a very simple client-server program in C. The client sends a message to the server, the server prints the message on screen with the total number of bytes received. If the client enters a message that begins with a ".", the client will send the message and then terminate. When the server receives a message that begins with a ".", it terminates.

Unless you are working on cs-linux (see below), use the localhost as a client and as a server. This is called the loopback interface. Packets sent to the local host address (127.0.0.1)  are not put onto the physical network but a loopback emulation of a network. They are processed locally and treated as incoming packets. At the level of abstraction of socket programming there is no difference between a physical network interface and the loopback interface. Provided you have appropriate privileges, the same program will run using the loopback interface or across a real physical network. This is useful for testing.

Example session
The following is an example of input and output on the client side, and corresponding output on the server. The messages sent from client to server are shown in red ("hello", "you got that?" and ".").

Client

started as: ./echo_client 127.0.0.1 20333

Server

started as: ./echo_server 20333

please enter the message: hello
client sent 16 bytes to server
please enter the message: you got that?
client sent 16 bytes to server
please enter the message: .
client sent 16 bytes to server
user entered ., exiting ...
waiting ....
server received 16 bytes:
hello
--------------------------
waiting ....
server received 16 bytes:
you got that?
--------------------------
waiting ....
server received ., exiting ...
Running the exercise
Copy the echo-server.zip  Download echo-server.ziparchive to the environment you have been using for C programming. Unzip the archive and you should see the following directory and files:

    echo-server/
        Makefile
        echo_client.c
        echo_server.c
Build the client and server
To build the client and server do:

    make
or

    make clean
    make
Starting server and client
You will need two terminal windows. Start the server in one terminal window and then the client in the other terminal.

The server command is:

    ./echo_server port
where "port" is replaced by a number in the range: 1025 to 65535

Our recommendation is to use 203XX for the port where XX is replaced by the day of your birth, e.g. 01 for 1st day of month, 24 for 24th day of month.

If you get the error:

    bind failed: Address already in use
when you start the server, add 10 to the port (e.g. if you tried 20324 before, try 20334 now). Keep trying new ports until you get the following output from the server:

    waiting
See the "Choosing ports" section for further information about choosing ports.

Now in the other terminal window start the client by entering the following command:

    ./echo_client 127.0.0.1 port
where 127.0.0.1 is the localhost or loopback address and port is replaced by the port you started the server listening to. (See the "Running on cs-linux" section for the server address you should use if you are working on the cs-linux cluster.)

You will then be prompted to enter text at the client that will be echoed at the server. When you enter a message beginning "." at the client, both server and client will terminate.

After you have run the server and client and seen how they work:

look at the client and server source code and try to understand it

You can look at the socket programming slides for an explanation of system calls used
Look at the man pages for the systems calls
Ask questions if you do not understand something
You can modify the source code if you wish and experiment with what it does - for example, you can do the extension exercise
Choosing ports
A network port is an integer in the range 0 to 65535. Ports in the range 0 to 1024 are "well-known" or privileged ports. For example, port 22 is for ssh and port 80 is for http. You must have superuser privileges to bind a server to a privileged port.

Ports 1025 to 65535 can be used without superuser privileges, though a number of ports in that range are also registered for use by known network services. It is not possible for two servers on the same host to bind to the same port number. Any attempt to do so will result in a "Address already in use" error.

For your CSC2035 work, you can choose any port number that satisfies the following two rules:

It must NOT be in the range 0 to 1024
It cannot be in use by another server on the host - you will find out if it is because you will get an error.
When you are working on your own machine or virtual machine, you are unlikely to get a port in use error (provided you do not use a port below 1025). When you are working on a shared machine (such as unix.ncl.ac.uk or cs-linux.ncl.ac.uk) it is possible you may get a port in use error. The higher the port number, the less likely you will get a clash. Therefore, we suggest the following simple algorithm for choosing a port that should eventually terminate with a port that you can use:

Choose a port number 203XX, where XX is your day of birth (this is so that if more than one of you is working at the same time, there is some chance that you will choose a unique port straight away)
If there is no error, your server will start
Else:
While there is an error:
Add 10 to the port you tried before and try again
The port that your server binds to must be the port that you tell the client to connect to.

Running the client on cs-linux.ncl.ac.uk
cs-linux.ncl.ac.uk is not a single host. It is a load balanced cluster of hosts. Therefore, if you start two ssh terminal sessions to cs-linux.ncl.ac.uk you will be working on two different hosts (they are named cs-linux1, cs-linux2 and so on). This means that the client cannot connect to the server on the loopback or localhost interface (127.0.0.1) because the server is actually running on a different host. You solve this problem by using the actual IP address of the server host as opposed to the localhost address (127.0.0.1). To find out the IP address of the server, use one of the following two methods.

Method 1: Use ping to find the server IP address
In the server terminal window type:

    hostname
This will give you the hostname of the server machine (e.g. cs-linux3)

In the client terminal window type:

    ping cs-linux3
replacing cs-linux3 with the name you got for the server.

This will result in output similar to:

    PING cs-linux3.ncl.ac.uk (10.4.224.8) 56(84) bytes of data.
    64 bytes from cs-linux3.ncl.ac.uk (10.4.224.8): icmp_seq=1 ttl=64 time=0.244 ms
    ...
    ...
Press CTRL-C to stop ping.

The server address to use is in brackets after the hostname (shown in bold in the example output above). Use that address as the first parameter when starting the client (in place of 127.0.0.1).

Method 2: Use ifconfig to find the server IP address
In the server terminal window type:

    ifconfig
You will get information for the host's network interfaces (including lo - the loopback interface). The first interface should look something like:

    ens160 Link encap:Ethernet  HWaddr 00:50:56:93:19:7b  
           inet addr:10.4.224.8  Bcast:10.4.227.255  Mask:255.255.252.0
           inet6 addr: fe80::250:56ff:fe93:197b/64 Scope:Link
           UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
           RX packets:393345789 errors:0 dropped:241 overruns:0 frame:0
           TX packets:335402470 errors:0 dropped:0 overruns:0 carrier:0
           collisions:0 txqueuelen:1000 
           RX bytes:1886788970098 (1.8 TB)  TX bytes:1874568906332 (1.8 TB)
    ...
    ...
The host address is labelled inet addr (and is shown in bold in the example output above). Use that address as the first parameter when starting the client (in place of 127.0.0.1).