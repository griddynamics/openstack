Writing Tests
=============

Bunch test scenarios are basically ``Lettuce`` feature files which are organized in special order. See Lettuce_ web site for details.

.. _Lettuce: http://lettuce.it


Organizing Test Scenarios
-------------------------

Tests are grouped into bunches. All tests in a bunch are considered to be independent and may be executed in any order or even in parallel.

``Test`` is a group of Lettuce scenarios of three types located in the same directory. These types are differentiated by file extensions:

* `<name>.test` is main scenario for test `<name>`
* `<name>.<configuration_name>.setup` is setup fixture for test `<name>` intended for test configuration `<configuration_name>`
* `<name>.<configuration_name>.teardown` is teardown fixture for test `<name>` intended for test configuration `<configuration_name>`

``Bunch`` consists  of ``tests`` and ``config.yaml`` file. All bunch items are located in the same directory, i.e. all Lettuce scenarios, Python files and configuration data.

``config.yaml`` is bunch-wide configuration file containing parameter values for scenario templates.

Test parametrization
---------------------

Every Lettuce scenarios in a bunch is considered as template. That means that scenarios may contain ``Jinja2`` template expressions. The template expression are expanded with values taken from ``config.yaml`` file. See examples below.

.. highlight:: yaml

If we have user item in the ``config.yaml``::

        user:
          name: admin1

        openstack_services:
         - openstack_services:
         - openstack-nova-api
         - openstack-nova-direct-api
         - openstack-nova-compute
         - openstack-nova-network
         - openstack-nova-scheduler
         - openstack-glance-api
         - openstack-glance-registry

.. highlight:: gherkin

Then we can use it in the ``users.setup``::

        Feature: Make preparations for sample test
                Scenario: Create admin user
                    Given every service is running:
                        | ServiceName   |
                        {% for service in openstack_services %}
                        | {{ service }} |
                        {% endfor %}
                    When I create nova admin user "{{user.name}}"
                    Then nova user "{{user.name}}" exists

.. highlight:: bash
The presence of bunch config and templates is not necessary. If omitted then no parametrization is performed. It is also possible to use some global config file for each bunch execution::

        user@machine:~/tests$bunch -c /pathto/config  bunch_tests_dir results_dir

The global config file can be used along with local per-bunch configs. In that case global variables are loaded first then local variables are loaded on bunch execution. The variables with the same names get overriden with local values.


Test Fixtures and Dependencies
------------------------------
.. highlight:: gherkin
By default all tests are dependent from their fixtures if any. But it is possible to specify that test requires extra setup performed prior its execution. The dependency specification is performed via special Lettuce steps in \*.test scenario::

        Scenario: Setup prerequisites
            Require setup: "<list of setup fixtures required>"

And ::

        Scenario: Setup prerequisites
            Require external setup: "<list of setup fixtures from other bunches>"

The `<list of setup fixtures required>` is the white-space or newline separated list of fixture names which should be executed and return successful result before test run. Setup scripts may be executed in parallel and started in the order specified in the "Require setup:" statement. Often setup actions rely on each other and so should be executed sequentially. For that reason syncronization quantifier may be used. Just put exclamation mark between fixture names, to specify the point of syncronization::

        setup1 ! setup2 setup3 ! setup_final

This splits fixtures into groups. All fixtures within single group are executed in parallel. Also it is possible to make it run sequentially in specific order::

        setup1 ! setup2 ! setup3 ! setup_final

The corresponding teardown scripts are executed after test is finished, so there is no need to specify that particular teardown is required.


Sample tests
------------

See more sample test bunches in the "samples" directory under source code root.
