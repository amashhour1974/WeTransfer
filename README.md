# WeTransfer circuit-breaker

The solution is building a simple circuit-breaker using Python.

The idea is that when you make a remote call(HTTP Request/RPC) to another service, there are chances that the remote call might fail.


## Description

A service is best described in three states:

* Closed: The closed state is the default "everything is working as expected" state. Requests pass freely through.
* Open: The open state rejects all requests without attempting to send them.
* Half-Open: A set number of requests are let through in order to test the status of the resource. This state       determines if the circuit returns to closed or open.

### Purpose

* To prevent a network or service failure from cascading to other services.
* Saves bandwidth by not making requests over a network when the service you’re requesting is down.
* Gives time for the failing service to recover.


## Getting Started

### Dependencies

Install Flask and requests. Ipython is optional

* pip install requests
* pip install Flask
* pip install ipython


### Installing

* How/where to download your program
* Any modifications needed to be made to files/folders

### Executing program

* How to run the program
* Step-by-step bullets
```
code blocks for commands
```

## Help

Any advise for common problems or issues.
```
command to run if program contains helper info
```