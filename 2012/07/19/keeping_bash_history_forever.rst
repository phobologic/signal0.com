Keeping bash history forever
============================

I spend a lot of time in a terminal.  More specifically, I spend a lot of time
in bash - being a sysadmin/developer type guy kind of necessitates it.  One of
my favorite features of most modern shells is the fact that they record the
commands you run, allowing you to easily repeat commands or to go back and
look at how you did something.

What's even better is that bash saves your history to a file whenever you
logout of your shell.  While that's awesome, it's little less awesome
that the size of your history file is limited (dictated by the
**HISTFILESIZE** environment variable, by default to 500 lines,
at least on Mountain Lion).  There are tons of times when I go to look back
through my history and the command I needed was missing because of this.

The problem becomes even worse when you have multiple terminals open.

Wouldn't it be awesome if after every command that command was dropped in a
file?  What if there was no limit to the # of history lines you could record?
What if you could record other useful data, like say what directory you were
in when you executed the command, the time you executed it, and what its
return code was?  Finally, wouldn't it be sweet if you could easily add notes
to that history file?

If you answered yes to any of the above, then look no further!

.. more::

So, to be honest, there might be a better way to do this.  If there is, I
haven't found it and I've looked a few times over the years.  Also, it might
be that another shell already does this for you.  That's awesome, but bash
works pretty well for me and I don't think I have the time to learn another
shell.

The good news is that this is all pretty easy to setup.  All it takes is two
environment variables (one if you don't care about the ability to annotate
your forever-history) and one alias (which is used for the annotation).

First I'm just going to paste in the entirety of what you need to put in your
.bash_profile to make all of this work just in case you don't want to read
through all my long winded explanation and just want to play with this:

.. code-block:: bash

    HISTIGNORE="hnote*"
    # Used to put notes in a history file
    function hnote {
        echo "## NOTE [`date`]: $*" >> $HOME/.history/bash_history-`date +%Y%m%d`
    }

    # used to keep my history forever
    PROMPT_COMMAND="[ -d $HOME/.history ] || mkdir -p $HOME/.history; echo : [\$(date)] $$ $USER \$OLDPWD\; \$(history 1 | sed -E 's/^[[:space:]]+[0-9]*[[:space:]]+//g') >> $HOME/.history/bash_history-\`date +%Y%m%d\`"

Now lets go into the environment variable that makes the forever-history
possible.  It's called *PROMPT_COMMAND*.  Here's the info about it from the
bash man page in case you don't want to have to search for it::

    PROMPT_COMMAND
        If set, the value is executed as a command prior to issuing each
        primary prompt.

So basically it lets you specify a list of commands you want to execute
**everytime** your prompt is displayed.

So using that, here's my *PROMPT_COMMAND* variable:

.. code-block:: bash
    
    PROMPT_COMMAND="[ -d $HOME/.history ] || mkdir -p $HOME/.history; echo : [\$(date)] $$ $USER \$OLDPWD\; \$(history 1 | sed -E 's/^[[:space:]]+[0-9]*[[:space:]]+//g') >> $HOME/.history/bash_history-\`date +%Y%m%d\`"

Thats it.
Ok, so lets step through each of the pieces of that.  First we have this bit:

.. code-block:: bash

    [ -d $HOME/.history] || mkdir -p $HOME/.history

All that does is make sure that the directory where it stores the forever
history files exists.  You could probably just have this line in your .bashrc
by itself - its not likely that .history is going to disappear between logins,
but it's not like this is going to kill your disk, so why not have that extra
little bit of insurance?

Ok, next we have the meat of the code that actually saves the history data.
Basically all it does is echo a long string of data into a file.  Here's the
first part of that:

.. code-block:: bash

    echo : [\$(date)] $$ $USER \$OLDPWD\;

**Update:** I realized that using $PWD wasn't actually working right.  You
want $OLDPWD instead.

So first it puts in a ':' followed by the date in brackets, followed by the
process id of the shell, then the logged in username and finally the current
working directory followed by a ';'.

The reason the information is surrounded by a set of ':' and ';' is so that you
can cut and paste the entire line from the logfile and it will work just fine.
Why is that?  It's because in bash ':' is a command that takes arguments and
does **nothing** with them.  It's effectively a null.  From the man-page::

    : [arguments]
        No  effect;  the command does nothing beyond expanding arguments
        and performing any specified redirections.  A zero exit code  is
        returned.

So whatever you put after the ':' does nothing.  The ';' ends the ':' command,
so whatever follows it is a whole new command.  So lets take a look at what
comes AFTER the ';':

.. code-block:: bash
    
    \$(history 1 | sed -E 's/^[[:space:]]+[0-9]*[[:space:]]+//g')

Unfortunately, in bash, there isn't a really easy way to just get the last
command you ran.  It'd be awesome if there was a $LAST_COMMAND environment
variable or something, but I couldn't find it, so I fall back on executing the
*history* command in a subshell.  Giving it the argument of 1 means that it
will only return 1 line from the history.  The unfortunate thing about the
history command is that it doesn't just return the command, it also returns
a # representing what command # it was during that session.  I don't really
care about that and it ruins the ability to cut and paste commands from the
forever-history file- hence the sed command.

::

    I'm not an expert on regular expressions, and this may not be the very
    best one.  If you have a better one that you feel will work better,
    PLEASE leave a comment about it and I'll change it.  Thanks!

Alright, now you've got the full echo statement.  There's only one last bit
to this, and thats where this data is being sent:

.. code-block:: bash

    >> $HOME/.history/bash_history-\`date +%Y%m%d\`

I didn't want one giant, ever growing file.  Instead this provides a file per
day, stamped with the date.  For example the filename on the 19th of July is
**bash_history-20120719**.

So that's how I make use of *PROMPT_COMMAND* to make forever-history a reality.
That only leaves this bit of shell hackery, both of which are entirely related
to adding notation to your history files:

.. code-block:: bash

    HISTIGNORE="hnote*"

    function hnote {
        echo "## NOTE [`date`]: $*" >> $HOME/.history/bash_history-`date +%Y%m%d`
    }

First lets talk about the function.  All this does is create a new 'command' in
your bash shell called **hnote** (for history-note).  It takes a message as
it's argument, and echos that message into your current history file in a
format that is easy to find later on.

The *HISTIGNORE* variable gives bash a list of patterns that it shouldn't put
into your history.  I figured it was probably useless to see the hnote
command itself being executed in the history file.

That's it!  So before I close this out, lets show you an example.  Here's me
executing a few commands in my shell (including me being a bonehead and 
forgetting syntax!):

.. code-block:: console

    $ pwd
    /Users/mike
    $ cd git/boto
    $ ls
    Changelog.rst   README.markdown boto            pylintrc        tests
    MANIFEST.in     bin             docs            setup.py
    $ ps auwwwx | head 3
    head: 3: No such file or directory
    $ ps auwwwx | head -3
    USER             PID  %CPU %MEM      VSZ    RSS   TT  STAT STARTED      TIME COMMAND
    mike             267   5.7  0.7  3944824 123800   ??  S     2:04PM  14:21.83 /Applications/Adium.app/Contents/MacOS/Adium -psn_0_28679
    mike             273   2.9  0.5  2727352  79176   ??  S     2:04PM   1:31.09 /Applications/iTerm.app/Contents/MacOS/iTerm -psn_0_53261
    $ hnote "Yay, I remembered how to use the head command."

And here's the relevant output in my history file:

.. code-block:: console

    : [Thu Jul 19 10:15:01 PDT 2012] 2221 mike /Users/mike; pwd
    : [Thu Jul 19 10:15:11 PDT 2012] 2221 mike /Users/mike/git/boto; cd git/boto
    : [Thu Jul 19 10:15:14 PDT 2012] 2221 mike /Users/mike/git/boto; ls
    : [Thu Jul 19 10:15:29 PDT 2012] 2221 mike /Users/mike/git/boto; ps auwwwx | head 3
    : [Thu Jul 19 10:15:37 PDT 2012] 2221 mike /Users/mike/git/boto; ps auwwwx | head -3
    ## NOTE [Thu Jul 19 10:18:25 PDT 2012]: Yay, I remembered how to use the head command.

Thats it!  Let me know if you have any issues with this or have any questions.

::

    One last thing: there are a few bugs in this.  Mostly they just involve
    the repeating of some commands into your history file.  This happens
    when you do things like open an editor (or whatever) then background
    it (with ^Z).  Your prompt is then redrawn, but you didn't execute a
    command, so you end up with the last command getting echo'd into your
    history file.  The hnote command has a similar bug and it almost makes
    me think I should just do away with HISTIGNORE completely.  If you have
    any ideas on how to make this better (I'm considering writing a python
    script for it) please let me know!  Thanks!


.. author:: default
.. categories:: shell
.. tags:: none
.. comments::
