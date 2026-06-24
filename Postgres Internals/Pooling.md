
Why do need postgres side poolers when we can create application side poolers ? 
I think mainly because this doesn't scale, if I want to add new pods how does the number of connections in the pool scales.
If we have a serverless environment database side pooling is the only option, this can also be an issue if you have auto scaling enabled


REUSE_PORT is a linux feature allowing multiple processess to listen to the same port. Linux decides how to load balance

Running Pgbouncer on the same machine would take some resources from postgres


Session Mode vs Transaction Mode

A single user sessions always connects to the same backend
Transaction mode, the same backend can work on multiple transactions.

Connection Queueing
Pgbouncer has the option of how many connections do we want to pass through and everything else has to wait.

Connection Routing
One good thing about Pgbouncer is that we can ship it with teh backend itself, so that the client code doesn't have to know about pgbouncer location and its config