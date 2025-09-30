# DNS Week Hackathon Project Suggestions

Some project suggestions for the DNS Week Hackathon in Stockholm, October 2025

A suggested implementation platform for most of these projects
is the TDNS Framework, see (foobar)[https://github.com/johanix/tdns.git]

## 1. NOTIFY+EDE-based error reporting mechanism, sender + receiver

For the ongoing work on multi-provider DNS it is necessary to design an
error reporting mechanism which allows a "provider" to report an "error"
back to the zone owner.

The tentative plan is for the zone owner to publish the location of the error
reporting receiver as a DSYNC record.

### 1.a. Define a new DSYNC scheme called "ERROR".

Use the private DSYNC scheme space.

my.zone.   IN DSYNC ANY ERROR 1234 error-receiver.my.zone.

### 1.b. Define a new EDNS(0) EDE code for "multi-provider errors". 

Use the private EDE space.

1.c. Implement an extremely simple error-receiver.

The receiver should only listen for NOTIFY messages with an attached
EDNS(0) EDE OPT. On receipt of such a message it should be
configureable what to do with it. At least "print to terminal" and
"send to syslog" should be supported.

### 1.d. Implement a simple CLI sender tool.

The CLI tool should take a zone name and an error string as arguments.
The tool should look up the "DSYNC ERROR" record for the zone and then
send a NOTIFY(SOA) including an EDNS(0) EDE OPT containing the error
string.

### 1.e. Implement TSIG support between sender and receiver.

Only senders in possession of the TSIG key are able to send error messages (i.e.
the receiver should discard all messages not correctly signed).
