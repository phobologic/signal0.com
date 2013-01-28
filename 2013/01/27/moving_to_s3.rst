Moving to S3
============

Sooooo I finally decided it was time to shut down the server I've ran for the
past 8 years.  In doing that I've had to start moving the various sites and
services I ran on it to other places.  That includes this site.

In the end, since this is a static website, I decided to go ahead and host it
on `Amazon S3`_, especially since they recently announced that they have given
sites on S3 the ability to host their root zone as a website there.

So I went and looked for directions on Amazon's website and found `this
walkthrough`_.  It's pretty easy to follow.  The only slightly annoying thing
is that you need to use `Route53`_ for your nameservers, which meant I had to
move it away from my registrar.  That didn't bother me too much in the end -
I don't have any particular love for GoDaddy these days, and the domain for
this website is tiny.

So first things first.  At first I considered writing my own script for pushing
the files on my site up to Amazon.  After playing with it in boto, I decided to
have a look around and see what else was out there.  That's when I found
`s3cmd`_.

It filled all my needs, specifically the sync functionality.  So I installed it
on my Mac laptop using `Homebrew`_.  Once that was done, I went ahead and wrote
a simple script that would handle a few things for me:

#. Run `Tinkerer`_ to compile the static site.
#. Updated my sitemap using the `Google Sitemap Generator`_
#. Call `s3cmd`_ to push my site up to S3.

I've gone ahead and pushed that script up to github (along with the rest of
the site).  You can find it here: `s3push`_.

One other file that's worth looking at is my `.gitignore`_ - I updated it so
that I wouldn't push my refreshed sitemap everytime I update the site.

Anyway, as you can see from the script, this is all super easy to do, and now
you're reading this site through the magic of S3.

Let me know if you have any questions or need a hand if you try to do the same
and I'll do my best!

.. _`Amazon S3`: http://aws.amazon.com/s3/
.. _`this walkthrough`: http://docs.aws.amazon.com/AmazonS3/latest/dev/website-hosting-custom-domain-walkthrough.html
.. _`Route53`: http://aws.amazon.com/route53/
.. _`s3cmd`: http://s3tools.org/s3cmd
.. _`Homebrew`: http://mxcl.github.com/homebrew/
.. _`Tinkerer`: http://www.tinkerer.me/
.. _`Google Sitemap Generator`: http://code.google.com/p/googlesitemapgenerator/
.. _`s3push`: https://github.com/phobologic/signal0.com/blob/master/bin/s3push
.. _`.gitignore`: https://github.com/phobologic/signal0.com/blob/master/.gitignore

.. author:: default
.. categories:: signal0, s3, shell
.. tags:: none
.. comments::
