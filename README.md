# DNS Week Hackathon Project Suggestions

Some project suggestions for the DNS Week Hackathon in Stockholm, October 2025

A suggested implementation platform for most of these projects
is the TDNS Framework, see (foobar)[https://github.com/johanix/tdns.git]

## 1. NOTIFY+EDE-based error reporting mechanism, sender + receiver

For the ongoing work on multi-provider DNS it is necessary to design an
error reporting mechanism which allows a "provider" to report an "error"
back to the zone owner.

The tentative plan is for the zone owner to publish the location of
the error reporting receiver as a DSYNC record. The sender tool does a
DSYNC lookup to find the error reporting target and sends a TSIG
signed NOTIFY-message containing an error message encoded in an
EDNS(0) EDE option.

### 1.a. Define a new DSYNC scheme called "ERROR".

Use the private DSYNC scheme space.

`my.zone.   IN DSYNC ANY ERROR 1234 error-receiver.my.zone.`

### 1.b. Define a set of new new EDNS(0) EDE code points.

Use the private EDE space to define code points and "extra text"
for various "multi-provider errors". 

### 1.c. Implement an extremely simple error-receiver.

The receiver should only listen for NOTIFY messages with an attached
EDNS(0) EDE OPT. On receipt of such a message it should be
configureable what to do with it. A good start would be to support
"print to terminal" and "send to syslog".

### 1.d. Implement a simple CLI sender tool.

The CLI tool should take a zone name and an error string as arguments.
The tool should look up the "DSYNC ERROR" record for the zone and then
send a NOTIFY(SOA) including an EDNS(0) EDE OPT containing the error
string.

### 1.e. Implement TSIG support between sender and receiver.

Only senders in possession of a TSIG key known to the receiver are able
to send error messages that are accepted (i.e. the receiver should discard
all messages not correctly signed).


## 2. Refresh the TDNS KeyState EDNS(0) option.

This is mostly an internal TDNS project. The KeyState implementation is
lagging behind and is in need of some polishing.

### 2.a. Move the KeyState implementation into tdns/edns0/ as a separate module.

### 2.b. Implement a KeyState query tool.

The Query tool should take a zone and a keyid as arguments, put them into a
DNS query for the zone name + SOA with a KeyState EDNS(0) option and send to
the target of the "{zone} DSYNC UPDATE" record. When the response is received
the contents should be presented.

### 2.c. Implement missing KeyState server-side features.

Right now most of the defined features are missing.
