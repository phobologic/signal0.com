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
    from subprocess import call

    editor = os.environ['EDITOR']
    message_file = sys.argv[1]
    # Used to figure out when we've reached the part in the commit message
    # where the errors go.
    error_header = '# GIT COMMIT MESSAGE FORMAT ERRORS:'


    def check_format_rules(lineno, line):
        """
        Given a line number and a line, compare them against a set of
        rules.  If it it fails a given rule, return an error message.  If
        it passes all rules then return false.
        """
        # Since enumerate starts at 0
        real_lineno = lineno + 1
        if lineno == 0:
            if len(line) > 50:
                return "E%d: First line should be less than 50 characters " \
                        "in length." % (real_lineno,)
        if lineno == 1:
            if line:
                return "E%d: Second line should be empty." % (real_lineno,)
        if not line.startswith('#'):
            if len(line) > 72:
                return "E%d: No line should be over 72 characters long." % (
                        real_lineno,)
        return False


    while True:
        # Temporary storage for the commit message so we can recreate it
        # and then append errors if there are any.
        commit_msg = list()
        errors = list()
        with open(message_file) as commit_fd:
            for lineno, line in enumerate(commit_fd):
                stripped_line = line.strip()
                # Break out of the loop if we've hit the error header
                if stripped_line == error_header:
                    break
                commit_msg.append(line)
                e = check_format_rules(lineno, stripped_line)
                if e:
                    errors.append(e)
        if errors:
            with open(message_file, 'w') as commit_fd:
                for line in commit_msg:
                    commit_fd.write(line)
                commit_fd.write('%s\n' % (error_header,))
                for error in errors:
                    commit_fd.write('#    %s\n' % (error,))
            re_edit = raw_input('Invalid git commit message format.  Would '
                    'you like to re-edit it?  (If you answer no, your '
                    'commit will fail) [Y/n]')
            if re_edit in ('N', 'n', 'NO', 'no', 'No', 'nO'):
                sys.exit(1)
            call('%s %s' % (editor, message_file), shell=True)
            continue
        # No errors (otherwise it would have either continued or exited) so lets
        # break out of the while loop and exit cleanly
        break

I went ahead and dropped that code into **.git/hooks/check-git-commit.py** then
made a soft link to it from **.git/hooks/commit-msg**.  Basically if there is
anything wrong with your commit message format that will stop your commit from
happening (because it exits with a non-zero status) and print out all the
things you did wrong.

**Update:** The old script I had here would fail and not let you re-edit the
message.  That's not the case with this new one.

BTW, you can get the latest version of my git hooks in my `github repo`_.

.. _`github repo`: https://github.com/phobologic/git-hooks


.. author:: default
.. categories:: git, python
.. tags:: none
.. comments::
