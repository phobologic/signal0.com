signal0?
========

Or *'Where did the name signal0 come from?'*

It comes from a 'trick' I like to use in unix shell scripts.

Basically when you want to check to see if a process is running with a given
process ID in unix there are a lot of ways to check - but the way I think is
the best is to execute a 'kill -0 <pid>'.  On my laptop I want to check if
there is a process with a PID of 777:

.. code-block:: console

    [0]mike@fenris:~>kill -0 777
    -bash: kill: (777) - No such process
    [1]mike@fenris:~>echo $?
    1

Or, used in a simple shell script that tells your nginx process is running:

.. code-block:: sh
    :linenos:

    #!/bin/sh

    _pid=`cat /var/run/nginx.pid`
    if kill -0 $_pid 2> /dev/null
    then
        echo "nginx pid $_pid is running."
    else
        echo "nginx pid $_pid is not running."
    fi

A while back (like 2010?) I was looking to start a blog to write about various
techy things I was working on and so I started looking for domains.  Took a
look, and sure enough signal0.com was available.

Of course I bought the domain, but then I never got around to actually writing
that blog.  Well here we are two years later.  Lets see if I'll keep up on
this.

.. author:: default
.. categories:: unix, shell
.. tags:: none
.. comments::
