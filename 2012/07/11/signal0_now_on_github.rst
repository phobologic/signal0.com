signal0 now on github
=====================

Some friends asked that I share my site source and so I figured it was probably
worth sharing with the world.  I've added a link over on the sidebar to my
github repository for the signal0.com source code (and learned how to add my
own sidebar entries - I'll put a blog post up about that soon).

I figure I might as well give a quick outline of how I got the code into
github just in case folks were curious.

.. more::

First thing you have to do is create a blank repository in github.  You do that
by going to https://github.com/new.  Give your repository a name (I chose
signal0.com) and a description, then choose whether you want the repository to
be public or private (I chose public).

Once you're done you'll be given a page with instructions on various things.
It's definitely worth looking over if you're new to git, but what I'm going to
assume you know the basics and I'm just going to focus on the parts that I
had to perform.

So at this point I have my remote repository, but now I have to let my local
git repository know about the github remote repository.  From my local
signal0.com repository I executed the following commands:

.. code-block:: console

    $ git remote add github git@github.com:phobologic/signal0.com.git
    $ git push github
    Counting objects: 203, done.
    Delta compression using up to 2 threads.
    Compressing objects: 100% (154/154), done.
    Writing objects: 100% (203/203), 42.11 KiB, done.
    Total 203 (delta 83), reused 0 (delta 0)
    To git@github.com:phobologic/signal0.com.git
     * [new branch]      master -> master

The first command adds a new remote called *github* to my local git
configuration.  The second command pushes my local checkout up to to the
github remote.

The downside to this is that now when I push my code I have to do it twice:
once to my webserver, and once to github.  There are ways around that, but most
that I can think of have security consequences, and honestly for now I think
this should be good enough.

... that said, if I get lazy and forget to update github, don't hesitate to
yell at me. :)


.. author:: default
.. categories:: git, signal0
.. tags:: none
.. comments::
