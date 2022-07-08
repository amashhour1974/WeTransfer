# Circuit-breaker

The solution is building a simple circuit-breaker using Python.

The idea is that when you make a remote call(HTTP Request/RPC) to another service, there are chances that the remote call might fail.


## Description

* circuit_breaker.py : CircuitBreaker class that handles all of the circuit breaker logic.
* main.py : the endpoints APIs to mock the server.
* snippet.py : use these snippets to test it out.

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

```
sudo pip install requests
sudo pip install Flask
sudo pip install ipython
```

### Installing

* Run the development server
```
export FLASK_APP=main.py;

flask run
```
By default it runs on port 5000


### Executing program

* Use the snippet.py to test it out.
* Open up a terminal and run the following commands using ipyhton

    - Line 1 and Line 2 are just imports. 
    - In line 3, it creates a CircuitBreaker object for make_request. 
    - Setting exceptions=(Exception,), this will catch all the exceptions. 

```
WeTransfer git:(main) ✗ ipython

 In [1]: from circuit_breaker import CircuitBreaker

In [2]: from snippets import make_request, faulty_endpoint, success_endpoint

In [3]: obj = CircuitBreaker(make_request, exceptions=(Exception,), threshold=5, delay=10)

In [4]: obj.make_remote_call(success_endpoint)
Call to http://localhost:5000/success succeed with status code = 200
22:25:30,332 INFO: Success: Remote call
22:25:30,332 INFO: Success: Remote call
Out[4]: <Response [200]>

In [5]: obj.make_remote_call(success_endpoint)
Call to http://localhost:5000/success succeed with status code = 200
22:25:40,242 INFO: Success: Remote call
22:25:40,242 INFO: Success: Remote call
Out[5]: <Response [200]>

In [6]: vars(obj)
Out[6]: 
{'func': <function circuit_breaker.APICircuitBreaker.__call__.<locals>.decorator(*args, **kwargs)>,
 'exceptions_to_catch': (Exception,),
 'threshold': 5,
 'delay': 10,
 'state': 'closed',
 'last_attempt_timestamp': 1657303824.772016,
 '_failed_attempt_count': 0}

```


* Now make successive calls to the faulty endpoint.

    - After the first five callls to the faulty_endpoint, the next call(Line 12) will not make an api-request to the flask server instead it will raise an Exception, mentioning to retry after a specified number of secs. 

    - If you make an API call to the success_endpoint endpoint (Line 13), 
    it will still raise error. It is in "Open" state.


```
In [7]: obj.make_remote_call(faulty_endpoint)
In [8]: obj.make_remote_call(faulty_endpoint)
In [9]: obj.make_remote_call(faulty_endpoint)
In [10]: obj.make_remote_call(faulty_endpoint)
In [11]: obj.make_remote_call(faulty_endpoint)
In [12]: obj.make_remote_call(faulty_endpoint)

Traceback data ..........
RemoteCallFailedException: Retry after 7.633885869918557 secs  
In [13]: obj.make_remote_call(success_endpoint)
---------------------------------------------------------------------------
Traceback data......
RemoteCallFailedException: Retry after 8.085483812012284 secs
```

* Finally, after the delay has elapsed, make a call to the success_endpoint, it will transition from Half-Open to Closed

```
In [19]: obj.make_remote_call(success_endpoint)
22:34:49,251 INFO: Changed state from open to half_open
...
22:34:49,251 INFO: Changed state from half_open to closed
Out[19]: <Response [200]>
 
```
 

## Help

Any advise for common problems or issues.
```
command to run if program contains helper info
```