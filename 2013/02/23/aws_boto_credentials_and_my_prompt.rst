AWS/Boto Credentials and my prompt
==================================

I do a lot of stuff with Amazon AWS/EC2 everyday.  I use many different
amazon accounts with different credentials.  For example I post this site to S3
in my personal AWS account.  I also have multiple work accounts for various
things.  Most of my interactions with AWS are done through python & `boto`_,
sometimes through scripts and sometimes through the python shell.  I used to
keep all of my various credentials in my *~/.boto* file, with only the one I
was using uncommented.

This meant that I had to be extra careful and doublecheck my .boto file to make
sure I was using the right account when doing anything with Amazon.  After a
couple of times where I ran commands (non-destructive) in the wrong account I
complained to my coworker.  He forwarded me a trick he'd been using, and I
expanded on it.

One of boto's features is that it can gather your credentials from a few
places.  One of those places is via the shell variables **AWS_ACCESS_KEY_ID**
and **AWS_SECRET_ACCESS_KEY**.  So first I created functions in my
**~/.bash_profile** that allowed me to reset those variables easily.  As well,
I had those functions set another variable **AWS_KEY_NAME**.  That isn't used
by boto at all, but it's useful for me.  For example, say I have two accounts:
my personal account and my work account.  I'd setup the following in my
profile:

.. code:: bash

    function aws_personal () {
        export AWS_KEY_NAME="personal"
        export AWS_ACCESS_KEY_ID=<my personal access key id>
        export AWS_SECRET_ACCESS_KEY=<my personal secret access key>
    }

    function aws_work () {
        export AWS_KEY_NAME="work"
        export AWS_ACCESS_KEY_ID=<my work access key id>
        export AWS_SECRET_ACCESS_KEY=<my work secret access key>
    }

With that in place (and after resourcing my .bash_profile) I can now call
either of those functions from my shell and I'm using those credentials in that
shell until I choose to change them again.

That's useful, but now it'd be really good to know what credentials I'm
currently using.  There are two places I really need to know that:

- My shell
- My python interactive interpreter

So lets start with the first.  In bash it's easy enough to change your prompt.
Since I set the **AWS_KEY_NAME** variable I can use that in my prompt.
That said, my prompt is already pretty long.  I finally gave in and decided
I needed to have a two line prompt.

Since I was going to do a little surgery on my prompt, and I don't do that very
often, I decided to add a couple of features to it.  Those were:

- multiline
- unobtrusive color unless a previous command failed, then make sure I know
- adding my current AWS credentials if they are set

The first is easy.  The second I used a variable I set in my **PROMPT_COMMAND**
that I discussed in a previous article `Keeping bash history forever`_.  The
third I used the new **AWS_KEY_NAME** variable.  In the end, this is what I
ended up with for my new prompt:

.. code:: bash

    COLOR_RESET="\e[00m"
    COLOR_GRAY="\e[01;30m"
    COLOR_RED="\e[22;31m"

    PROMPT_COMMAND="ECODE=\$?; TODAY=\`date +%Y%m%d\`;[ -d $HOME/.history ] || mkdir -p $HOME/.history; echo : [\$(date)] $USER \$ECODE \$PWD\; \$(history 1 | sed -E 's/^[[:space:]]+[0-9]*[[:space:]]+//g') >> $HOME/.history/bash_history-\$TODAY"

    PS1="${COLOR_GRAY}[\$([ \$ECODE -ne 0 ] && echo \"${COLOR_RED}\$ECODE${COLOR_GRAY} \")\u@\h \$([ ! -z \$AWS_KEY_NAME ] && echo \"aws:\$AWS_KEY_NAME \")\w]${COLOR_RESET}\n> "

With that set I have a two line, dark grey prompt that will display which of my
Amazon credentials I am currently using and will also display the exit code of
my last command in red **only** if the last command exited with a non-zero
status (thx to my buddy John for that idea).  Here's an example:

.. image:: /_static/images/aws_creds_prompt.png
    :width: 540px

Now my shell is displaying things, but I also play around in the python
interactive interpreter a lot as well, so it'd be good to see what credentials
I was using in there as well.  Fortunately you can modify that prompt by
setting **sys.ps1** in the **~/.pythonrc.py** file.  The trick is that we want
the prompt to be dyamic - fortunately **sys.ps1** allows you to do that by
calling *str()* on any non-string object you give it.  This means you can pass
an object with a dynamic **__str__** method.  Here's the excerpt from my
**.pythonrc.py** that does this:

.. code:: python

    import os
    import sys

    class AWSCredentialsPrompt(object):
        def __init__(self, base_prompt):
            self.base = base_prompt
            
        def __str__(self):
            try:
                return "(aws:%s) %s " % (os.environ['AWS_KEY_NAME'], self.base)
            except KeyError:
                return "%s " % (self.base)

    sys.ps1 = AWSCredentialsPrompt('>>>')
    sys.ps2 = AWSCredentialsPrompt('...')

That's it!  If I were using the work credentials and I enter my python
interpreter, here's what you'd see:

.. image:: /_static/images/aws_creds_python_prompt.png
    :width: 540px

So that's all there is to it.  I'm working on a few functions in python to act
the same as the shell functions to switch credentials.  It shouldn't be too
difficult.

Let me know if you have any questions or something doesn't work for you.  Oh,
and if you have any ideas for enhancements leave them in the comments!

.. _`boto`: https://github.com/boto/boto
.. _`Keeping bash history forever`: http://signal0.com/2012/07/19/keeping_bash_history_forever.html

.. author:: default
.. categories:: aws, python, shell
.. tags:: none
.. comments::
