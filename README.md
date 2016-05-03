# Jsoknit
This document describes the Jsoknit interface definition, a (http://www.json.org/ "JSON") based 
method of creating embedded software interfaces, and some implementation examples.

# INTRODUCTION

The objectives of Jsoknit are 1) to create a JSON based method of
defining embedded software functions such that functions may be called 
remotely, 2) to create a parser able to deal with a jsoknit definition
such that an interface may be readily available for controlling embedded
software, 3) to facilitate IOT/IOE implementations by providing a single
and consistent embedded software definition standard.      
  
By creating a consistent interface definition, that can be handled by 
various computing device guests, the hardware interface to the embedded software may
be abstracted. 

# QUICKSTART - THE LED EXAMPLE

Consider the JSON string:

```
{"libdef":{
    "def":"833cd4e3",
    "app":"Led control"},
 "function":[
    {"id":1,
     "label":"Led On",
     "type":"button"
    },
    {"id":2,
     "label":"Led Off",
     "type":"button"
    },
    {"id":3,
     "label":"Blink Led",
     "type":"button"
    }
 ]
}
```

The Jsoknit interface is defined at the embedded software level. The interface definition is returned in reply to a _libdef_ string sent to the embedded device. Sending JSON strings to the device, such as "{"id":1}", "{"id":2}" and "{"id":3}", would generate actions such as describe in the label attributes.


Once a jsoknit enabled device receives a libdef request, it returns a JSON
string containing a libdef object consisting of:
1. def attribute with a unique identifier to be used in acknowledgement 
(ack) requests.

ack request

If an aknowledgement is required, an ack request should be part of the 
JSON string. Consider the jsoknit definition below:

```
{"libdef":{
    "def":"833cd4e3",
    "app":"Led control"},
 "function":[
    {"id":1,
     "label":"Led On",
     "type":"button",
     "call":"ledOn()"
    },
    {"id":2,
     "label":"Led Off",
     "type":"button",
     "call":"ledOff()"
    },
    {"id":3,
     "label":"Blink Led",
     "type":"button",
     "call":"ledBlink()"
    }
 ]
}
```

The sender has not included an ack request so no acknowledgment of receipt 
will be sent back. ack requests should be included with a unique ack 
identifier, tipically the def string plus time stamp, mac address or a
combination of both:

The function object array

The function object array is a base 1 array containing the following labels
1. id
The function index
2. label
The function identifier as presented to the front-end user
3. type
The widget type as presented to the front-end user
4. call
The function name in the remote node, used to make the function call.

Implemented types:

button  
switch  
reader  
writer  

```
{"libdef":{
    "def":"833cd4e3",
    "app":"Led switch"},
 "function":[
    {"id":1,
     "label":"Led On",
     "type":"button",
     "call":"ledOn()"
    },
    {"id":2,
     "label":"Led Off",
     "type":"button",
     "call":"ledOff()"
    },
    {"id":3,
     "label":"Blink Led",
     "type":"button",
     "call":"ledBlink()"
    }
 ],
 "comms":{
    "interval":5,
    "retries":3},
 "ack":"833cd4e3:20160405224421"
}
```

In this example, once the requester receives the libdef object definition,
an ack must be returned in the form of:

{"ack":"833cd4e3:20160405224421"}

Once the requestee receives the ack, the libdef object definition (in this
example) is removed from delivery queue. If the ack is not received in the
stipulated interval, the message is resent until an ack is received or
until the retry threshold is reached, whichever comes first. If no ack is
returned in a timely manner, within the stipulated number of retries, the 
requester will be deemed dormant and no further action will be taken from 
the requestee.

Delivery queue

The requestee and the requester implement a message delivery queue following 
the communication (comms) definition in the libdef object definition.

An implementation in pseudo-code would look like:

<IMPLEMENTATION GOES HERE>

