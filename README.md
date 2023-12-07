MousePaw Media: Development Documentation
============================================

Development at MousePaw Media takes place on a carefully designed
technology stack, engineered for efficiency and ease-of-use. To ensure
that all developers are familiar with these technologies and methodologies,
we maintain this documentation.

Our Development Documentation is not a replacement for any official
documentation. Our focus here is to describe our particular instances
and usages of our stack's technologies.

This documentation is built using Sphinx.

Building
----------------------------------

You must have Python 3 installed on your system, and you need to create a
virtual environment. On UNIX-based systems, you can run:

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r source/requirements.txt
```

Then you can run one of the following `make` commands:

```bash
make html # Create HTML version.
make latex # Create LaTEX version.
make latexpdf # Create PDF via LaTEX.
make man # Create man pages.
make epub # Create e-book.
```

There are additional make targets as well. To see the complete list, simply run...

```bash
make
```

License
---------------

The MousePaw Media Development Documentation is made available via the
Creative Commons Attribution-ShareAlike ([CC BY-SA 4.0][2]) license.

The project is owned and maintained by [MousePaw Media][1].

[1]: https://www.mousepawmedia.com/developers
[2]: https://creativecommons.org/licenses/by-sa/4.0/
