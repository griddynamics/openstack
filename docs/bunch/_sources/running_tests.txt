Running Tests
=============

.. highlight:: python

Tests may be executed via `bunch` console script or via `bunch.cli.main()` function::

        $bunch  samples/dummy results

The `results` directory will contain parameterized scenarios and test results.

Test environment specification
------------------------------

Use option '-e' to specify test environment for test execution. This option controls the set of test fixtures which should be used during test run::

        $bunch -e clean samples/openstack-smoke/ rez2

Execution parallelism
----------------------

The `-b` command option controls bunch execution concurrency. It may have the following values:

* `serial` - tests are executed in single thread
* `unlimited` - all tests may be executed in parallel if possible
* `limited<n>` - tests are executed within <n> threads
* `auto` - tests are executed according to machine capabilities and the common sense
