retina macbook pro & screenshots
================================

A while back I got a new Macbook Pro with the flashy retina display.  It's
probably the most powerful computer that I've ever owned, and the display made
the pain of being forced from my usual 17" laptop to the smaller 15" laptop
a whole lot easier to stomach (also man is this thing easier to carry around in
my laptop bag!)

One of the things that's proven to be a pain with it though is that all those
extra pixels make for *massive* screenshots.  This is made even worse when I
try to take a screenshot then post it to this site.  As of now I've made due
with using the really great website `Pixlr`_, but that isn't really ideal
because it's an extra step, and I'm super lazy.

Unfortunately despite quite a bit of searching I haven't found a solution that
provides for the following requirements:

- Acts like the regular screenshot command (shift-command-4 in other words)
- Automatically scales the image quality down to something reasonable (I
  realize that 'reasonable' isn't a great definition).

Honestly I don't know if such a tool exists, and maybe the haziness of the
second requirement makes it impractical.  Still, if anyone out there has found
something that would work, I'd love to hear about it.

In the meantime I think I'm going to give the following workaround for posting
the screenshots to this page a try.  It's not perfect, because it means I'm
using larger images than necessary, but at least it lets laziness prevail.

One of the cool features of Sphinx (the thing that renders the RST documents
I write into the HTML that you see, by way of Tinkerer) is that you can
specify image dimensions.  This isn't all that amazing honestly, since HTML
has allowed for this for a while, but its still useful.  So for now, I plan on
posting my images as normal, and then using something similar to the following
to make sure the image doesn't look GIGANTIC on the page:

.. code-block:: rest

    .. image:: /_static/images/example.jpg
        :width: 540px

Anyway, like I said its not optimal, and if I take any gigantic screenshots
I'll still use `Pixlr`_ to shrink them down before uploading them, but for
most of my screenshots I think using the width should be fine.

If you have any other suggestions I'm all ears!

.. _`Pixlr`: http://pixlr.com/editor/


.. author:: default
.. categories:: apple, tinkerer
.. tags:: none
.. comments::
