VIM Crosshairs
==============

VIM is my editor of choice.  I've tried a few others, but I'm too used to using
vi keybindings (I even use them for command line editing in bash) and so I have
a really hard time walking away from it.

Since I write python code, indent levels become very important.  I try to keep
my functions nice and short, but sometimes when looking at other people's code
I'll find long functions and it becomes increasingly hard to tell which indent
level I am at or should be at.

A useful way I've found for dealing with this is by having *crosshairs* that
cross over where my cursor is.  The best way to expalin this is by an example,
so here it is:

.. image:: /_static/images/vim_crosshairs.jpg

Setting this up is actually pretty easy.  The full explanation of it can be
found on the `vim wikia site`_.  The way I have it setup is almost exactly
like the example they give - when you hit '\c' the crosshairs are toggled on
or off.  To get that going, put the following in your **.vimrc** or your
custom colors file:

.. code-block:: console

    hi CursorLine   cterm=NONE ctermbg=235
    hi CursorColumn cterm=NONE ctermbg=235
    nnoremap <Leader>c :set cursorline! cursorcolumn!<CR>

.. _`vim wikia site`: http://vim.wikia.com/wiki/Highlight_current_line


.. author:: default
.. categories:: vim
.. tags:: none
.. comments::
