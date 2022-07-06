# WeTransfer circuit-breaker
The following solution is building a simple circuit-breaker using Python

Purpose
-------
- When all services were working and the remote calls were returning without any errors, we call this state — “Closed”.

- When the remote calls continued to fail and when we stopped making any more remote calls to the failing service, we call this state — “Open”

- After a certain delay, when we make a remote call to the failing service, the state transitions from “Open” to “Half-Open”. If the remote call does not fail, then we transition the state from “Half Open” to “Closed” and the subsequent remote calls are allowed to be made. 

- In case the remote call failed, we transition the state from “Half Open”, back to “Open” state and we wait for a certain period of time till we can make the next remote call (in Half Open state)



# Why do you need it?
- To prevent a network or service failure from cascading to other services.
- Saves bandwidth by not making requests over a network when the service you’re requesting is down.
- Gives time for the failing service to recover.

# Pre-Requists 

Install Flask and requests. Ipython is optional

pip install requests
pip install Flask
pip install ipython

# Complete Code

- circuit_breaker.py : CircuitBreaker class that handles all of the circuit breaker logic
- main.py : creating of the endpoints APIs to mock the server
- snippet.py : You can use these snippets to test it out.

# Execution pathway

- Run the development server

export FLASK_APP=main.py; flask run

By default it runs on port 5000

- Now to test it out. You can use the snippet.py to test it out.
- open up a terminal and run the following commands using ipyhton

Line 1 and Line 2 are just imports. In line 3, it creates a CircuitBreaker object for make_request. 
setting exceptions=(Exception,), this will catch all the exceptions. 
narrow down the exception to the one that we actually want to catch, in this case, Network Exceptions.

WeTransfer git:(main) ✗ ipython
In [1]: from circuit_breaker import CircuitBreaker
In [2]: from snippets import make_request, faulty_endpoint, success_endpoint
In [3]: obj = CircuitBreaker(make_request, exceptions=(Exception,), threshold=5, delay=10)
In [4]: obj.make_remote_call(success_endpoint)
Call to http://localhost:5000/success succeed with status code = 200
06:07:51,255 INFO: Success: Remote call
Out[4]: <Response [200]>
In [5]: obj.make_remote_call(success_endpoint)
Call to http://localhost:5000/success succeed with status code = 200
06:07:53,610 INFO: Success: Remote call
Out[5]: <Response [200]>
In [6]: vars(obj)
Out[6]:
{'func': <function snippets.make_request(url)>,
 'exceptions_to_catch': (Exception,),
 'threshold': 5,
 'delay': 10,
 'state': 'closed',
 'last_attempt_timestamp': 1607800073.610199,
 '_failed_attempt_count': 0}

# Now make successive calls to the faulty endpoint.

 - Make following calls as fast as possible. After the first five callls to the faulty_endpoint, the next call(Line 12) will not make an api-request to the flask server instead it will raise an Exception, mentioning to retry after a specified number of secs. Even if you make an API call to the success_endpoint endpoint (Line 13), it will still raise an error. It is in "Open" state.


In [7]: obj.make_remote_call(faulty_endpoint)
In [8]: obj.make_remote_call(faulty_endpoint)
In [9]: obj.make_remote_call(faulty_endpoint)
In [10]: obj.make_remote_call(faulty_endpoint)
In [11]: obj.make_remote_call(faulty_endpoint)
In [12]: obj.make_remote_call(faulty_endpoint)

Traceback data ..........
RemoteCallFailedException: Retry after 8.688776969909668 secs  
In [13]: obj.make_remote_call(success_endpoint)
---------------------------------------------------------------------------
Traceback data......
RemoteCallFailedException: Retry after 6.096494913101196 secs


-  Now, after the delay has elapsed, make a call to the success_endpoint, it will transition from Half-Open to Closed state

In [19]: obj.make_remote_call(success_endpoint)
06:25:10,673 INFO: Changed state from open to half_open
...
06:25:10,678 INFO: Changed state from half_open to closed
Out[19]: <Response [200]>
 


