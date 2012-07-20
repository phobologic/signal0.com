Checking git commit message format before commit'ing
====================================================

Seems like I've been on a 'style guide' tear lately.  Once I got git yelling at
me when I tried to check in non-pep8 compliant code a coworker of mine pointed
out that git commit messages have a 'style guide' of sorts.  Of course now I
needed to make sure that my git commit messages were good as well.

Fortunately the style guide for git commit messages is pretty simple, so I
wrote my own really basic checker.  If you're interested, keep reading.

.. more::

Basically the rules of git commit messages are these:

- The first line should act like a subject and be no longer than 50 characters
- That should be followed by a blank line
- Any other lines should be less than 72 characters wide

I told you they were simple.  Here's the python script I wrote to make sure
I was following those 3 rules:

.. code-block:: python

    #!/usr/bin/python                     
                                          
    import sys                            
    import os                             
                                          
    exit_code = 0                         
    with open(sys.argv[1]) as commit_msg: 
        for lineno, line in enumerate(commit_msg):
            line = line.strip()           
            if lineno == 0:               
                if len(line) > 50:        
                    exit_code = 1         
                    print "# E%d: First line should be less than 50 " \
                            "characters." % (lineno,)
            elif lineno == 1:             
                if line:                  
                    exit_code = 1         
                    print "# E%d: Second line should be empty." % (lineno,)
            else:                         
                if line.startswith('#'):  
                    # ignore comment lines                                          
                    continue              
                if len(line) > 72:        
                    exit_code = 1         
                    print "# E%d: No line should be over 72 characters " \
                            "long." % ( lineno,)      
                                          
    sys.exit(exit_code)                   

I went ahead and dropped that code into **.git/hooks/check-git-commit.py** then
made a soft link to it from **.git/hooks/commit-msg**.  Basically if there is
anything wrong with your commit message format that will stop your commit from
happening (because it exits with a non-zero status) and print out all the
things you did wrong.

What would be cool is if there were a way to make it so that rather than
dumping your commit completely it threw you back into your editor so you
could clean up your commit message rather than having to start from scratch.

I'll work on that. :)


.. author:: default
.. categories:: git, python
.. tags:: none
.. comments::
