Modifying Tinkerer Themes
=========================

*Update: After I wrote this I ended up going back to the 'native' pygments
style.  The reason was that monokai didn't seem to produce css for the shell
console code-block type, and I wanted those blocks to be pretty like the rest
of them.*

So one of the reasons it took me so long to create this site was that I had
a really hard time finding a system I liked to create it with.  My thought
process on that is probably the content of another blog post, but since I'm
in the middle of working on making this site the way I want it I figured I'd
share some of the things I've found about the static blog generator I settled
on, Tinkerer_.

While for the most part I really enjoyed Tinkerer's default theme (modern5),
one thing I wanted to mess around with was the theme it used for syntax
highlighting.

.. more::

It turns out its actually pretty simple - and even more simple if you use an
existing theme.  The first thing I did was make a *themes* directory in my
site's source, then copied the modern5 theme into that directory with a new
name (signal0 in this case):

.. code-block:: console

    mkdir themes
    cp -r /usr/local/lib/python2.7/site-packages/tinkerer/themes/modern5 themes/signal0

*Note: My server is a FreeBSD host.  The path to your tinkerer themes may be
different on your server depending on a lot of things.  If you don't know the
correct path, here's a way you can figure it out:*

.. code-block:: pycon

    Python 2.7.2 (default, Oct 24 2011, 00:25:49) 
    [GCC 4.2.1 20070719  [FreeBSD]] on freebsd8
    Type "help", "copyright", "credits" or "license" for more information.
    >>> import tinkerer
    >>> print "%s/themes" % (tinkerer.__path__[0],)
    /usr/local/lib/python2.7/site-packages/tinkerer/themes

So now that you have the copy of your theme, now we need to update your theme
to use a new syntax highlighter.  Tinkerer uses Sphinx_, which in turn uses
Pygments_ for syntax highlighting.

Pygments calls its different highlighting themes 'styles'.  So lets see what
styles we have available on our system.  Again you want to do that from your
python interactive interpreter:

.. code-block:: pycon

    Python 2.7.2 (default, Oct 24 2011, 00:25:49) 
    [GCC 4.2.1 20070719  [FreeBSD]] on freebsd8
    Type "help", "copyright", "credits" or "license" for more information.
    >>> from pygments.styles import STYLE_MAP
    >>> import pprint
    >>> pprint.pprint(STYLE_MAP)
    {'autumn': 'autumn::AutumnStyle',
     'borland': 'borland::BorlandStyle',
     'bw': 'bw::BlackWhiteStyle',
     'colorful': 'colorful::ColorfulStyle',
     'default': 'default::DefaultStyle',
     'emacs': 'emacs::EmacsStyle',
     'friendly': 'friendly::FriendlyStyle',
     'fruity': 'fruity::FruityStyle',
     'manni': 'manni::ManniStyle',
     'monokai': 'monokai::MonokaiStyle',
     'murphy': 'murphy::MurphyStyle',
     'native': 'native::NativeStyle',
     'pastie': 'pastie::PastieStyle',
     'perldoc': 'perldoc::PerldocStyle',
     'tango': 'tango::TangoStyle',
     'trac': 'trac::TracStyle',
     'vim': 'vim::VimStyle',
     'vs': 'vs::VisualStudioStyle'}

As you can see there's a ton of styles available on my system.  In the end you
have to play with them to figure out which one you like best, but in the end
I settled on **monokai**.

In order to tell Sphinx to tell Pygments to use a new theme, there are a few
things that need to be changed.  First lets change our custom theme.  To do
that you need to edit the *theme.conf* file, changing pygments_style to the
appropriate theme name.  In this example, the file is located in
**themes/signal0/theme.conf**:

.. code-block:: ini

    [theme]
    inherit = boilerplate
    stylesheet = modern5.css
    pygments_style = monokai

Once you have that, now you have to tell Sphinx how to find your new theme, and
then to use it.  Both of these settings are located at the root of your site
source tree in **conf.py**.

The first thing you need to update is the *html_theme* setting.  This chooses
your theme.  The second thing you need to update is *html_theme_path*, which
tells Sphinx what paths to look through in order to find your theme.  After
I edited mine, here's what I was left with::

    html_theme = "signal0"
    html_theme_path = [tinkerer.paths.themes, 'themes']

And that's it!  Recompile your site and enjoy your new pretty syntax
highlighting colors.  If you have any questions, let me know!


.. _Tinkerer: http://tinkerer.me/
.. _Sphinx: http://sphinx.pocoo.org/
.. _Pygments: http://pygments.org/


.. author:: default
.. categories:: tinkerer, python
.. tags:: none
.. comments::
