# JSOKNIT

This document describes the Jsoknit interface definition, a [JSON](http://www.json.org/) based 
method of creating embedded software interfaces, and some implementation examples.

## Motivation

As the world becomes more connected, it is desirable that embedded devices speak a common language, such that they may be accessed by any computing device capable of understanding the language.  

By creating a consistent interface definition, that can be handled by various computing device guests, the hardware interface to the embedded software e.g. buttons, displays, etc, may be abstracted, facilitating IOT/IOE implementations.

## Jsoknit Implementation Elements

To implement a Jsoknit interface two basic elements are required:  
1. A _libdef_ library definition JSON string on the embedded device  
2. A Jsoknit engine on the client  
A Jsoknit engine is capable of requesting the libdef library definition from the embedded device and implementing a graphical interface on the fly, as well as exposing the definition for additional uses that may not involve graphical interfaces, or graphical interfaces that combine any number of Jsoknit devices, as well as any other desired inputs.

## Quickstart

On the embedded device, a Jsoknit library definition libdef string or array of characters is defined:

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

The Jsoknit definition is returned in reply to a _libdef_ string sent to the embedded device. Sending JSON strings to the device, such as "{"id":1}", "{"id":2}" and "{"id":3}", would generate actions such as describe in the label attributes.


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

