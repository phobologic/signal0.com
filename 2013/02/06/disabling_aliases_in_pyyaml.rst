disabling aliases in pyYaml
===========================

Lately I've been working with a python script to act as an
`external node classifier`_ for `Puppet`_.  Puppet expects the output of the
ENC to be in YAML, so naturally I reached for `PyYAML`_.  Everything was going
great, but then I tried to put the same bit of information in both the classes
AND parameters section.  See this script for example:

.. code-block:: python

    import yaml

    services = {
            'enabled': ['nginx', 'webapp'],
            'disabled': ['postgres'],
    }

    data = {'classes': {'services': services}, 'parameters': services}

    print yaml.safe_dump(data, default_flow_style=False)

When you run this script you end up with the following output:

.. code-block:: yaml

    classes:
      services: &id001
        disabled:
        - postgres
        enabled:
        - nginx
        - webapp
    parameters: \*id001

The issue with this is the use of aliases.  Basically since the data I had in
the 'services' dictionary was the same as what I had in parameters pyYAML
recognized that and created the alias &id001 pointing at that data, then
referenced it rather than copying that data into parameters.

Unfortunately the version of puppet that I am running (2.7.11, the default in
Ubuntu 12.04) can't handle aliases, even though they're a definitely a part of
the YAML syntax.  Honestly in most cases I'd be super happy that PyYAML was
smart enough to do this for me automatically.  But in this case it was causing
issues.

After digging around, I found out a way to work around it.  Unfortunately it's
not as simple as an argument to safe_dump.  Instead you need to provide your
own instance of a Dumper object, and then override ignore_aliases so that it
always returns True.

Here's the updated script:

.. code-block:: python

    import yaml

    services = {
            'enabled': ['nginx', 'webapp'],
            'disabled': ['postgres'],
    }

    data = {'classes': {'services': services}, 'parameters': services}

    noalias_dumper = yaml.dumper.SafeDumper
    noalias_dumper.ignore_aliases = lambda self, data: True
    print yaml.dump(data, default_flow_style=False, Dumper=noalias_dumper)

All the changes are in the last three lines.  First I create an object which
is a copy of the SafeDumper class.  Then I patch the ignore_aliases method on
that object to always return True.  Finally I use dump instead of safe_dump
(safe_dump just calls dump, but specifies the SafeDumper) and pass along the
newly created dumper which is based on SafeDumper.  In the end, this is the
new output:

.. code-block:: yaml

    classes:
      services:
        disabled:
        - postgres
        enabled:
        - nginx
        - webapp
    parameters:
      disabled:
      - postgres
      enabled:
      - nginx
      - webapp

And now puppet can read it just fine.

.. _`external node classifier`: http://docs.puppetlabs.com/guides/external_nodes.html
.. _`Puppet`: https://puppetlabs.com/
.. _`PyYAML`: http://pyyaml.org/


.. author:: default
.. categories:: python, yaml, puppet
.. tags:: none
.. comments::
