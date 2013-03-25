indigo
======

Core OpenFlow agent and infrastructure modules

Overview
========

This repository is available at https://github.com/floodlight/indigo.git.
It provides

* The Indigo-2 architecture header files
* The implementation of the core modules related to OpenFlow switch state
* Unit tests for each module

Getting Started
===============

System Requirements
-------------------

This code has been tested on Ubuntu 12.10 server with the packages
indicated below.  In general, the code should build on Linux based
servers with gcc, make and Python.

The following packages were installed on Ubuntu 12.10 using apt-get to
allow the unit tests to compile and run.

* git (version 1.7.10.4)
* make (GNU make version 3.81)
* gcc (version 4.7)
* libcap2-bin (for setcap)

Building Unit Tests
-------------------

Run `make check`.  This will build and run each module's unit test.  In
general, you will see a lot of output and occasionally even error messages
for tests that exercise error handling.

Tracking Issues
===============

Issues are currently tracked in the github repository tracking facility.
Please select the Issues tab at the repository web site:
https://github.com/floodlight/indigo


Contributions
=============

Pull requests are welcomed and will be evaluated as quickly as possible.
Coding guidelines have yet to be documented.

License
=======

Please see LICENSE at the top level of the repository.

Code Overview
=============

The code in this repository is organized into modules under the Modules
subdirectory.  The module's code files are divided between published 
include headers (usually in the subdirectory `module/inc/`) and the actual
implementation (usually in the subdirectory `module/src/`).

In general, each module has instructions on how to build a library.  
Modules may have dependencies on other modules, though these must be
limited to the published header files.

Applications are built by creating a subdirectory under targets.  Each
target lists the set of modules on which it depends and then provides
application code.  Currently, only unit test applications are made
available in this distribution.

The build infrastructure is implemented by a collection of make files
and some scripts.  These live in the `Builder` subdirectory.  To support
multiple build environments, the `Builder` subdirectory has per-build 
environment subdirectories.  Currently only the unix build environment
is supported in this distribution.

Architectural Overview
======================

The Indigo architecture uses Loci, the C output of LoxiGen (available at
https://github.com/floodlight/loxigen.git).  That library provides a set
of interfaces for working with OpenFlow objects.  In general, Loci
definitions are prefixed with `of_`.  The Loci files are included in the
indigo distribution, though they can be updated using LoxiGen directly.

The Indigo architecture (sometimes called the Indigo-2 architecture)
on which this code is based is specified in the header files in the
`indigo` module.  Note that this can be a bit confusing as the top level
distribution and the module defining these interfaces both have the same
name.

This architecture is broken into two sets of modules:  The platform 
independent modules and the platform specific modules.  This repository
provides implementations of platform independent modules.  These
include:

* The socket manager: Provides the functionality for managing sockets.
It provides a generic socket registration process as well as timer
event processing to allow these functions to be integrated in single
threaded environments.
* The OpenFlow connection manager: Provides the functionality for
managing OpenFlow connections such as addition and removal of
connection instances, tracking the connection state (handshakes,
keep alives) and applying rate limiting policy for asynchronous
messages.
* The OpenFlow state manager: Provides the functionality for
representing the OpenFlow state of the switch in a platform
independent way.  This allows the decoupling of database-like queries
on the OpenFlow flow table from the manipulation of the forwarding
state that is platform specific.

The platform specific modules (not included in this distribution) are:

* Forwarding:  This module exposes interfaces that allow the
manipulation of the platform's forwarding engine as represented by
OpenFlow protocol updates.
* Port manager:  This module exposes interfaces the allow the
interrogation and manipulation of the platforms data plane entries.
* Configuration:  This module provides platform and implementation
specific access to the interfaces provided by the other modules that
permit updating the running configuration of the application.

In addition to the Indigo architecture implementations, some modules
providing basic infrastructure are also provided in this
distribution.  These include:

* AIM: A set generic infrastructure primitives providing I/O
abstractions (pvs), conventional semantics such as error return code
values, logging abstractions, and data type utilities.
* murmur: An implementation of the murmur hash function.
* BigList:  Under `BigData/BigList`, this module provides a
dynamically managed generic linked list implementation.