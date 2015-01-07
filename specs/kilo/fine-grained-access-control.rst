..

This work is licensed under a Creative Commons Attribution 3.0 Unported License.
http://creativecommons.org/licenses/by/3.0/legalcode

=============================
 Fine Grained Access Control
=============================

https://blueprints.launchpad.net/designate/+spec/fine-grained-access-control

Introducing fine grained access control for Designate resources using policy.json.

Problem description
===================

Presently, in Designate, we can control access only at action level ('action' meaning 'create_domain' or 'create_record'). But in reality, particularly in private clouds, we need more fine grained access control for the resources.

For example, one might want certain tenants only be able to manage CNAME records, not the other record types. 

Proposed change
===============

In designate/policy.py, 'check' function must take a one more parameter called 'type' or 'extension', which will be appended to the rule, and will be used for the lookup for access control.

Inside the check funtion, we need to add the logic to append the 'type' parameter so that the lookup happens the right way. We use the separator ":", as done in nova.

::

  if type is not None:
    rule = '%s:%s' % (rule, type)

In designate/central/service.py, we need to pass 'type' parameter wherever fine grained access is mandated.

Changes in will be in designate/central.


API Changes
-----------

None.


Central Changes
---------------

None.

Storage Changes
---------------

None.

Other Changes
-------------

Policy check is done at more fine grained level. In this blueprint, it is suggested to include 'record type' as an extension while matching the access rights.
CRUD operations on records and recordsets need modifications on the policy checking.

Alternatives
------------

None.

Implementation
==============

Assignee(s)
-----------

Primary assignee:
  https://launchpad.net/~prabhakhar

Milestones
----------

Target Milestone for completion:
  Kilo-2

Work Items
----------

1. Modify designate/policy.py 'check' funtion to optionally get 'type' or 'extension' parameter.
   Use that parameter for rule lookup, if passed. use ":" for separator.

2. designate/central/service.py needs modifications for the resources that need fine grained access control.
   In Kilo-2, the 'record' resource would gain fine grained access control based on the type of the record.

3. policy.json needs an example of ":" separated rule for users ti understand the feature.


Dependencies
============

None.
