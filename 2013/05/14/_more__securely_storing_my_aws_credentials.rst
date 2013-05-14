(more) securely storing my AWS credentials
==========================================

This is an update to my previous post, `AWS/Boto Credentials and my prompt`_.

In that post I started storing my AWS credentials in my .bash_profile.  It's
worked great for a while, but there's been one thing that's bothered me about
it: my credentials were stored in plain text in my .bash_profile.  Not
awesome.

So I set out to fix it.  The first step was to remove all the aws credential
functions and instead move them to their own file.  I named it
*.bash_aws_profiles*.  I then encrypted it with openssl using the aes-256
cipher with the following command:

.. code:: bash

    openssl aes-256-cbc -a -salt -in .bash_aws_profiles -out .bash_aws_profiles.enc

It will ask for a password - pick a secure one, and don't share it.

Next you need a way to get those functions back into your shell.  I tried to
do this by having *source* or *eval* read in the output from openssl
unencrypting the file, but neither of them worked for me for various reasons.
In the end I had to use a temporary file, which I'm not super excited about,
but it only exists for a very short period of time, which is better than what
we had before.

As my buddy Tim would say: "Don't let best be the enemy of good!"

So I added these functions to my *.bash_profile*:

.. code:: bash

    function load_aws () {
        AWSKEYFILE=$HOME/.bash_aws_profiles.enc
        TEMPFILE=`mktemp $HOME/.bash_aws_profiles.XXXXXXXXXXX`
        [ ! -f $AWSKEYFILE ] && echo "Error: Amazon key file ($AWSKEYFILE) not found." && return
        openssl aes-256-cbc -d -a -in $AWSKEYFILE -out $TEMPFILE
        source $TEMPFILE
        rm $TEMPFILE
        echo "! AWS keys loaded."
    }

    function unload_aws () {
        KEYFUNCS=`set | awk '/aws_[^ ]+ \(\)/ {print $1}'`
        for k in $KEYFUNCS
        do
            echo "! Unloading $k"
            unset $k
        done
    }

Now whenever I want to use my AWS credentials I first have to type *load_aws*.
It asks me for my password, then loads them into the environment.  When I'm
finished I can run *unload_aws* to delete them.

I'd really like it if there was a sort of *boto-agent* that would store the
keys instead of the shell, and then expire them after a given amount of time.
I'll work on that someday.

Let me know if you have any questions, or any ideas for improvements.  Thanks!


.. _`AWS/Boto Credentials and my prompt`: http://signal0.com/2013/02/23/aws_boto_credentials_and_my_prompt.html



.. author:: default
.. categories:: aws, shell
.. tags:: none
.. comments::
