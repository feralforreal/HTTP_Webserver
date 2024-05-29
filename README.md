# HTTP_Webserver
A web server is a program that receives requests from a client and sends the processed result back to the client as a response to the command. A client is usually a user trying to access the server using a web browser (Google Chrome, Firefox).

-------------------
GETTING FILES:

I interpret '/' and '/index.html' as index.html in a GET req. All other symbols I treat as a file to look for in the root (www) directory, and I return a 500 if that file does not exist. I hardcoded for every file extension in the directory because the main difference between HTTP/1.1 and /1.0 is that /1.0 does not support persistant connections, where 1.1 does. In that case, I should not get a "Connection" line in a well-formed /1.0 req and I will not send a "Connection" line back in a /1.0 response. Beyond this I treat the two the same. 

-----------------------
MULTIPLE CONNECTIONS:

Every time my listening socket gets a new connection, a POSIX thread is created. These run as long as necessary (more on that later) then die off. Their process IDs are stored in a set, which is run through when the process ends and each thread is "joined" before the server exits fully. This way, Chrome's rediculous multiple connection request thing is supported efficiently (checked Wireshark). 

--------------------
ERROR HANDLING:

Every time something doesn't work, I send a 500 message. I return this when I can't find a file, the HTML request is malformed in a few different ways, or I think I'm about to seg fault for some reason. The directions say "These messages in the exact format as shown above should be sent back to the client if any of the above error occurs[sic]," but Chrome does not like this approach. 

------------------
PIPELINING:

I set the socket file descriptors for my threads to non-blocking, so each thread runs through a loop continuously even if there is no data to be read from the socket. This way, I can almost continuously check the current time against the stored time of the last message and then kill the thread when the timer expires. 
Under the submission reqs, it seems this is extra credit (though that is not consistent with anywhere else)! I will gladly take it!

***********A NOTE ON TELNET***********
Telnet by default (on my system at least) seemed fixed on sending "\r\0\r\n" for every "\r\n" echoed into it! I assume its because we are abusing its function slightly. I fixed this by editing my .telnetrc file to run the "toggle crlf" commend in telnet by default on startup.

