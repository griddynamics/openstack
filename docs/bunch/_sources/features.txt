========
Features
========

Implemented features
====================

* Test parameterization via Jinja2 templates and YAML configs
* Test fixture separation
* Fixture grouping by test configuration. That will help writing environment agnostic tests which require different fixture versions for each environment
* Dependencies for test fixtures: setup and teardown scripts associated with test may be shared within test bunch and between different bunches
* xUnit XML reports. This is handy for using Bunch with CI tools

Planned features
================
* Parallel test scenario execution
