Syntax+pep8 checking before committing in git
=============================================

*UPDATE 2012/08/13:* I've updated the git hook code per suggestions from folks
in the comments.

I write quite a bit of code in python.  As a considerate python programmer I
tend to try to follow PEP8_ as best I can.  That said, even though I've been
working with python & pep8 for years it's still easy to forget the little
things.

While PEP8 is great while you're working alone, it's even more important when
you're working on code with other people.  Wouldn't it be nice if you could
make sure that every piece of python code you push into your repository that
other people might see is pep8 compliant?  Fortunately this is very possible,
and you can even add some syntax checking as well thanks to Tarek Ziade's
awesome flake8_ script.

.. more::

So, first of all you're going to need to get flake8 installed.  Easy enough
with the help of pip:

.. code-block:: console

    $ sudo pip install flake8
    Downloading/unpacking flake8
      Downloading flake8-1.4.tar.gz
      Running setup.py egg_info for package flake8
        
    Installing collected packages: flake8
      Running setup.py install for flake8
        changing mode of build/scripts-2.7/flake8 from 644 to 755
        
        changing mode of /usr/local/bin/flake8 to 755
    Successfully installed flake8
    Cleaning up...

At this point you're all set.  Say you write some code and you manually want
to check it for syntax errors and pep8 violations?  You just run
*flake8 <filename>* and it'll tell you how badly you've done.  For example,
say I wrote this lovely piece of code:

.. code-block:: python

    #!/usr/bin/python

    import sys, string, random

    def random_capital_letter()
      letters = string.uppercase
        print "Capital Letter: "+random.choice(letters)
        print ---

    random_capital_letter()

As you can see there are all sorts of syntax & pep8 errors in the code.  It'd
suck if you checked that in - noone wants to be teased for goofy looking code.
So lets run it through flake8 and see what it says:

.. code-block:: console

    $ flake8 random-capital-letter.py 
    random-capital-letter.py:5: invalid syntax
    def random_capital_letter()
                               ^
    random-capital-letter.py:3:11: E401 multiple imports on one line
    random-capital-letter.py:5:1: E302 expected 2 blank lines, found 1
    random-capital-letter.py:6:3: E111 indentation is not a multiple of four
    random-capital-letter.py:6:3: E113 unexpected indentation
    random-capital-letter.py:7:5: E113 unexpected indentation
    random-capital-letter.py:7:29: E225 missing whitespace around operator

Awesome right?

Now rather than having to remember to run flake8 against everything you write
everytime you write it, how about we make it so git (*and you can do this
in just about any VCS I'm sure*) uses flake8 to check every python file we
try to commit before we commit it, and then won't let us commit if there's an
issue?  Again, this is super easy - thanks to git's hooks_.

The hook we're interested in is called pre-commit.  Hooks are just scripts, and
in this case we're going to use a shell script to call flake8 on every python
file we check in.  First, here's the hook itself.  You want to save this as
**.git/hooks/pre-commit** in your repository.

.. code-block:: sh

    #!/bin/sh
    # Auto-check for pep8 so I don't check in bad code
    FILES=$(git diff --cached --name-only --diff-filter=ACM | grep -e '\.py$')

    if [ -n "$FILES" ]; then
        flake8 -r $FILES
    fi

And thats it.  Save that file, and now if you try to *git commit* the code from
above into your repo it'll fail with the same error that you got when you ran
flake8 manually.


.. _PEP8: http://www.python.org/dev/peps/pep-0008/
.. _flake8: https://bitbucket.org/tarek/flake8
.. _hooks: http://git-scm.com/book/en/Customizing-Git-Git-Hooks



.. author:: default
.. categories:: python, git
.. tags:: none
.. comments::
