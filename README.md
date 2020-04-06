# HW4
Weihang Qin
Liam Goodwin

We used C++ REST SDK for both server and client. RESTSDK is great for client but has only experimental features for servers. See server side for details. 
## Server side:
Besides the cache files that are from the last homework, the server side includes three additional files, namely cache_server.cpp, handler.h and handler.cpp.

### cache_server.cpp
This is merely a shell for initiating the server, it uses getopt to get arguments from command line and then passes them as parameter to initialize a handler class. The handler class then keeps listening to requests until "Enter" is pressed. 

### handler

handler, as the name suggests, is a class that handles http requests. Its main structure is implemented by both a cache and a http_listener. The listener is a restsdk class that supports user-defined methods to handle different requests.

There are six methods that our listener will support: 
- handle_put
- handle_get
- handle_delete
- handle_post
- handle_head
- handle_error

The first five methods are as described in the project requirement. Handle_error currently ignores the error but can be defined for specific instructions. 

We always return (and send) messages in json form. In addition to the returned value (val type for get and void for others), we also reply the request with a status_code and a reply message so that the client knows what's going on.  

For handle_get, our implementation was to take the char array that value points to and convert it into a string, then return a copy of that string to the client. This solves the sharing address problem on the same computer and makes cross-machine communication possible. 

For handle_head, restsdk has a built-in mechanism which accepts a user defined reply message but does not pass it to the client.  The same code, if used in other type of requests, would return the used space to the client as desired, however did not work for handle_head. I do not know whether this is a feature or a bug. To get around it, we will pass a specific command to Get request to fulfill the space_used method for the client. 

Note: utility::string_t in restsdk is defined differently for different operating systems, if you are using it outside linux, you might need to adjust your code. 


## Client side
We reimplemented the cache class with cache_client.cpp and modified the test accordingly. (since the parameters for constructing the actual cache is passed to server now)
Our test assumes the server is running on http://127.0.0.1:34568

Restsdk uses Asynchronous Function Calls to make http requests, where at each step we take in a lambda function to execute the request and pass the return value as the input parameter to the next lambda function. 

We've tested the implementation both on Windows and Linux and it works fine. The issue we encountered are all related to restsdk's OS-specific implementation.