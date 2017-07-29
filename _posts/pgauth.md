---
layout: post
title:  "3 Steps (2 minutes) to Setup Your Personal Website with Jalpc"
date:   2017-01-31
desc: "3 Steps (2 minutes) to Setup Your Personal Website with Jalpc"
keywords: "Jalpc,Jekyll,gh-pages,website,blog,easy"
categories: [HTML]
tags: [Jalpc,Jekyll]
icon: icon-html
---

It's backend process that authenticates users in PostgreSQL... Not Postmaster Process... 
September 03, 2016


Earlier I used to think that, In PostgreSQL, it is postmaster process that authenticates the user connections (Which is of course not wrong but no more). But somewhere I read like "backend process authenticates the user connections" and this made me to investigate more on this. So here I'm writing what I found :).

Back in 2000, postmaster process used to authenticate the user connections but after PostgreSQL 7.x, When user try to connect to the database, postmaster process immediately forks a new child process for authentication and same child process becomes backend process for database connection. 

Here is what I found in `src/backend/postmaster/postmaster.c`

```When a request message is received, we now fork() immediately. The child process performs authentication of the request, and then becomes a backend if successful. This allows the auth code to be written in a simple single-threaded style (as opposed to the crufty "poor man's multitasking" code that used to be needed). More importantly, it ensures that blockages in non-multithreaded libraries like SSL or PAM cannot cause denial of service to other clients."
They made this change to eliminate problems with authentication blocking. Here is relevant commit I found which says,
"Handle reading of startup packet and authentication exchange after forking a new postmaster child process. 
This should eliminate problems with authentication blocking (.g., ident, SSL init) and also reduce problems with the accept queue filling up under heavy load.```


Also I would like to quote a line from this documentation.

`"Whenever a request for a connection is detected the process spawns a new server process."`


Also, here is comment found in source code of postmaster.c from postgreSQL 6.5 (Line 803) in which postmaster used to authenticate the user connections.


```"If there is no error and no outstanding data transfer going on, then the authentication handshake must be complete to the postmaster's satisfaction.  So, start the backend."```
