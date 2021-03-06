=========================
kids.test
=========================

.. image:: http://img.shields.io/pypi/v/kids.test.svg?style=flat
   :target: https://pypi.python.org/pypi/kids.test/
   :alt: Latest PyPI version

.. image:: http://img.shields.io/pypi/dm/kids.test.svg?style=flat
   :target: https://pypi.python.org/pypi/kids.test/
   :alt: Number of PyPI downloads

.. image:: http://img.shields.io/travis/0k/kids.test/master.svg?style=flat
   :target: https://travis-ci.org/0k/kids.test/
   :alt: Travis CI build status

.. image:: http://img.shields.io/coveralls/0k/kids.test/master.svg?style=flat
   :target: https://coveralls.io/r/0k/kids.test
   :alt: Test coverage



``kids.test`` is a Python library providing helpers when writing tests
in python. It's part of 'Kids' (for Keep It Dead Simple) library.


Maturity
========

This is alpha release. It's only a repository with a few recipes and holds very
few interests. It might be more consistent in the future.

It lacks any sensible documentation and has nothing ground breaking in its core.


Features
========

using ``kids.test``:

- You can use a replacement ``unittest`` class that ensure that you have some
  python3 additions like ``assertContains`` or ``assertRegex``.
- You can use ``BaseTmpDirTest`` that creates a temporary directory before
  executing tests, and delete it at the end.


Installation
============

You don't need to download the GIT version of the code as ``kids.test`` is
available on the PyPI. So you should be able to run::

    pip install kids.test

If you have downloaded the GIT sources, then you could add install
the current version via traditional::

    python setup.py install

And if you don't have the GIT sources but would like to get the latest
master or branch from github, you could also::

    pip install git+https://github.com/0k/kids.test

Or even select a specific revision (branch/tag/commit)::

    pip install git+https://github.com/0k/kids.test@master


Usage
=====

Test
----

``Test`` is the base class for all unittest in ``kids.*`` framework. It provides
some missing ``.assert*()`` method to python 2::

    >>> from kids.test import Test, run

    >>> class MyTest(Test):
    ...    def test_foo(self):
    ...        self.assertRegex('foo', 'fo+')
    ...        self.assertContains('bar foo hop', 'foo')
    ...        self.assertNotContains('bar fou hop', 'foo')
    ...    def test_failing_regex(self):
    ...        self.assertRegex('foo', 'xfo+')
    ...    def test_failing_contains(self):
    ...        self.assertContains('bar fou hop', 'foo')
    ...    def test_failing_not_contains(self):
    ...        self.assertNotContains('bar foo hop', 'foo')

    >>> run(MyTest)
    FFF.
    ...
    AssertionError: ...'bar fou hop' should contain 'foo'.
    ...
    AssertionError: ...'bar foo hop' should not contain 'foo'.
    ...
    AssertionError: ...'foo' should match regex 'xfo+'.
    ...
    Ran 4 tests in ...s
    <BLANKLINE>
    FAILED (failures=3)
    <BLANKLINE>


BaseTmpDirTest
--------------

This unittest class will ensure that the current work dir (CWD) before
executing test, is an empty temporary directory. And it'll ensure this directory
will be deleted at the end::

    >>> from kids.test import BaseTmpDirTest, run

    >>> class MyTest(BaseTmpDirTest):
    ...    def test_foo(self):
    ...        import glob
    ...        self.assertEqual(len(glob.glob('*')), 0)

    >>> run(MyTest)
    .
    ----------------------------------------------------------------------
    Ran 1 test in ...s
    <BLANKLINE>
    OK
    <BLANKLINE>


Here's what happens behing the scene, let's use minimock to show the
creation and deletion of directory::

    >>> import minimock

    >>> import tempfile, shutil, os

    >>> minimock.mock('tempfile.mkdtemp', returns='/tmp/tempdir')
    >>> minimock.mock('os.chdir', returns='/tmp/tempdir')
    >>> minimock.mock('shutil.rmtree')

    >>> class MyTest(BaseTmpDirTest):
    ...    def test_foo(self):
    ...        print("running test")
    ...        self.assertTrue(True)

    >>> run(MyTest)
    Called tempfile.mkdtemp()
    Called os.chdir('/tmp/tempdir')
    running test
    Called os.chdir('...')
    Called shutil.rmtree('/tmp/tempdir')
    .
    ----------------------------------------------------------------------
    Ran 1 test in ...s
    <BLANKLINE>
    OK
    <BLANKLINE>

    >>> minimock.restore()

Contributing
============

Any suggestion or issue is welcome. Push request are very welcome,
please check out the guidelines.


Push Request Guidelines
-----------------------

You can send any code. I'll look at it and will integrate it myself in
the code base and leave you as the author. This process can take time and
it'll take less time if you follow the following guidelines:

- check your code with PEP8 or pylint. Try to stick to 80 columns wide.
- separate your commits per smallest concern.
- each commit should pass the tests (to allow easy bisect)
- each functionality/bugfix commit should contain the code, tests,
  and doc.
- prior minor commit with typographic or code cosmetic changes are
  very welcome. These should be tagged in their commit summary with
  ``!minor``.
- the commit message should follow gitchangelog rules (check the git
  log to get examples)
- if the commit fixes an issue or finished the implementation of a
  feature, please mention it in the summary.

If you have some questions about guidelines which is not answered here,
please check the current ``git log``, you might find previous commit that
would show you how to deal with your issue.


License
=======

Copyright (c) 2015 Valentin Lab.

Licensed under the `BSD License`_.

.. _BSD License: http://raw.github.com/0k/kids.test/master/LICENSE
