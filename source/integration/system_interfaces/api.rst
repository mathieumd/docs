.. _api:

============
XML-RPC API
============

This reference documentation describes the xml-rpc methods exposed by OpenNebula. Each description consists of the method name and the input and output values.

All xml-rpc responses share a common structure.

+--------+-------------+-------------------------------------------------+
| Type   | Data Type   | Description                                     |
+========+=============+=================================================+
| OUT    | Boolean     | True or false whenever is successful or not.    |
+--------+-------------+-------------------------------------------------+
| OUT    | String      | If an error occurs this is the error message.   |
+--------+-------------+-------------------------------------------------+
| OUT    | Int         | Error code.                                     |
+--------+-------------+-------------------------------------------------+

The output will always consist of three values. The first and third ones are fixed, but the second one will contain the String error message only in case of failure. If the method is successful, the returned value may be of another Data Type.

The Error Code will contain one of the following values:

+--------+----------------+-----------------------------------------------------------------------+
| Value  |      Code      |                                Meaning                                |
+========+================+=======================================================================+
| 0x0000 | SUCCESS        | Success response.                                                     |
+--------+----------------+-----------------------------------------------------------------------+
| 0x0100 | AUTHENTICATION | User could not be authenticated.                                      |
+--------+----------------+-----------------------------------------------------------------------+
| 0x0200 | AUTHORIZATION  | User is not authorized to perform the requested action.               |
+--------+----------------+-----------------------------------------------------------------------+
| 0x0400 | NO\_EXISTS     | The requested resource does not exist.                                |
+--------+----------------+-----------------------------------------------------------------------+
| 0x0800 | ACTION         | Wrong state to perform action.                                        |
+--------+----------------+-----------------------------------------------------------------------+
| 0x1000 | XML\_RPC\_API  | Wrong parameters, e.g. param should be -1 or -2, but -3 was received. |
+--------+----------------+-----------------------------------------------------------------------+
| 0x2000 | INTERNAL       | Internal error, e.g. the resource could not be loaded from the DB.    |
+--------+----------------+-----------------------------------------------------------------------+

.. warning:: All methods expect a session string associated to the connected user as the first parameter. It has to be formed with the contents of the ONE\_AUTH file, which will be ``<username>:<password>`` with the default 'core' auth driver.

.. warning:: Each XML-RPC request has to be authenticated and authorized. See the :ref:`Auth Subsystem documentation <auth_overview>` for more information.

The information strings returned by the ``one.*.info`` methods are XML-formatted. The complete XML Schemas (XSD) reference is included at the end of this page. We encourage you to use the ``-x`` option of the :ref:`command line interface <cli>` to collect sample outputs from your own infrastructure.

The methods that accept XML templates require the root element to be TEMPLATE. For instance, this template:

.. code::

    NAME = abc
    MEMORY = 1024
    ATT1 = value1

Can be also given to OpenNebula with the following XML:

.. code::

    <TEMPLATE>
      <NAME>abc</NAME>
      <MEMORY>1024</MEMORY>
      <ATT1>value1</ATT1>
    </TEMPLATE>

Authorization Requests Reference
================================

For each XML-RPC request, the session token is authenticated, and after that the Request Manager generates an authorization request that can include more than one operation. The following tables document these requests.

onevm
-----

+----------------------+---------------------------+-------------------+
|    onevm command     |     XML-RPC Method        |   Auth. Request   |
+======================+===========================+===================+
| deploy               | one.vm.deploy             | VM:ADMIN          |
|                      |                           | HOST:MANAGE       |
+----------------------+---------------------------+-------------------+
| delete               | one.vm.action             | VM:MANAGE         |
| boot                 |                           |                   |
| shutdown             |                           |                   |
| suspend              |                           |                   |
| hold                 |                           |                   |
| stop                 |                           |                   |
| resume               |                           |                   |
| release              |                           |                   |
| poweroff             |                           |                   |
| reboot               |                           |                   |
+----------------------+---------------------------+-------------------+
| resched              | one.vm.action             | VM:ADMIN          |
| unresched            |                           |                   |
+----------------------+---------------------------+-------------------+
| migrate              | one.vm.migrate            | VM:ADMIN          |
|                      |                           | HOST:MANAGE       |
+----------------------+---------------------------+-------------------+
| disk-saveas          | one.vm.disksaveas         | VM:MANAGE         |
|                      |                           | IMAGE:CREATE      |
+----------------------+---------------------------+-------------------+
| disk-snapshot-create | one.vm.disksnapshotcreate | VM:MANAGE         |
|                      |                           | IMAGE:MANAGE      |
+----------------------+---------------------------+-------------------+
| disk-snapshot-delete | one.vm.disksnapshotdelete | VM:MANAGE         |
|                      |                           | IMAGE:MANAGE      |
+----------------------+---------------------------+-------------------+
| disk-snapshot-revert | one.vm.disksnapshotrevert | VM:MANAGE         |
+----------------------+---------------------------+-------------------+
| disk-attach          | one.vm.attach             | VM:MANAGE         |
|                      |                           | IMAGE:USE         |
+----------------------+---------------------------+-------------------+
| disk-detach          | one.vm.detach             | VM:MANAGE         |
+----------------------+---------------------------+-------------------+
| nic-attach           | one.vm.attachnic          | VM:MANAGE         |
|                      |                           | NET:USE           |
+----------------------+---------------------------+-------------------+
| nic-detach           | one.vm.detachnic          | VM:MANAGE         |
+----------------------+---------------------------+-------------------+
| create               | one.vm.allocate           | VM:CREATE         |
|                      |                           | IMAGE:USE         |
|                      |                           | NET:USE           |
+----------------------+---------------------------+-------------------+
| show                 | one.vm.info               | VM:USE            |
+----------------------+---------------------------+-------------------+
| chown                | one.vm.chown              | VM:MANAGE         |
| chgrp                |                           | [USER:MANAGE]     |
|                      |                           | [GROUP:USE]       |
+----------------------+---------------------------+-------------------+
| chmod                | one.vm.chmod              | VM:<MANAGE\ADMIN> |
+----------------------+---------------------------+-------------------+
| rename               | one.vm.rename             | VM:MANAGE         |
+----------------------+---------------------------+-------------------+
| snapshot-create      | one.vm.snapshotcreate     | VM:MANAGE         |
+----------------------+---------------------------+-------------------+
| snapshot-delete      | one.vm.snapshotdelete     | VM:MANAGE         |
+----------------------+---------------------------+-------------------+
| snapshot-revert      | one.vm.snapshotrevert     | VM:MANAGE         |
+----------------------+---------------------------+-------------------+
| resize               | one.vm.resize             | VM:MANAGE         |
+----------------------+---------------------------+-------------------+
| update               | one.vm.update             | VM:MANAGE         |
+----------------------+---------------------------+-------------------+
| recover              | one.vm.recover            | VM:ADMIN          |
+----------------------+---------------------------+-------------------+
| save                 | -- (ruby method)          | VM:MANAGE         |
|                      |                           | IMAGE:CREATE      |
|                      |                           | TEMPLATE:CREATE   |
+----------------------+---------------------------+-------------------+
| list                 | one.vmpool.info           | VM:USE            |
| top                  |                           |                   |
+----------------------+---------------------------+-------------------+

.. warning:: The deploy action requires the user issuing the command to have VM:ADMIN rights. This user will usually be the scheduler with the oneadmin credentials.

The scheduler deploys VMs to the Hosts over which the VM owner has MANAGE rights.

onetemplate
-----------

+---------------------+--------------------------+------------------------+
| onetemplate command |      XML-RPC Method      |     Auth. Request      |
+=====================+==========================+========================+
| update              | one.template.update      | TEMPLATE:MANAGE        |
+---------------------+--------------------------+------------------------+
| instantiate         | one.template.instantiate | TEMPLATE:USE           |
|                     |                          | [IMAGE:USE]            |
|                     |                          | [NET:USE]              |
+---------------------+--------------------------+------------------------+
| create              | one.template.allocate    | TEMPLATE:CREATE        |
+---------------------+--------------------------+------------------------+
| clone               | one.template.clone       | TEMPLATE:CREATE        |
|                     |                          | TEMPLATE:USE           |
+---------------------+--------------------------+------------------------+
| delete              | one.template.delete      | TEMPLATE:MANAGE        |
+---------------------+--------------------------+------------------------+
| show                | one.template.info        | TEMPLATE:USE           |
+---------------------+--------------------------+------------------------+
| chown               | one.template.chown       | TEMPLATE:MANAGE        |
| chgrp               |                          | [USER:MANAGE]          |
|                     |                          | [GROUP:USE]            |
+---------------------+--------------------------+------------------------+
| chmod               | one.template.chmod       | TEMPLATE:<MANAGE\ADMIN |
+---------------------+--------------------------+------------------------+
| rename              | one.template.rename      | TEMPLATE:MANAGE        |
+---------------------+--------------------------+------------------------+
| list                | one.templatepool.info    | TEMPLATE:USE           |
| top                 |                          |                        |
+---------------------+--------------------------+------------------------+

onehost
-------

+-----------------+-------------------+---------------+
| onehost command |   XML-RPC Method  | Auth. Request |
+=================+===================+===============+
| enable          | one.host.enable   | HOST:ADMIN    |
| disable         |                   |               |
+-----------------+-------------------+---------------+
| update          | one.host.update   | HOST:ADMIN    |
+-----------------+-------------------+---------------+
| create          | one.host.allocate | HOST:CREATE   |
+-----------------+-------------------+---------------+
| delete          | one.host.delete   | HOST:ADMIN    |
+-----------------+-------------------+---------------+
| rename          | one.host.rename   | HOST:ADMIN    |
+-----------------+-------------------+---------------+
| show            | one.host.info     | HOST:USE      |
+-----------------+-------------------+---------------+
| list            | one.hostpool.info | HOST:USE      |
| top             |                   |               |
+-----------------+-------------------+---------------+

.. warning:: onehost sync is not performed by the core, it is done by the ruby command onehost.

onecluster
----------

+--------------------+--------------------------+-----------------+
| onecluster command |      XML-RPC Method      |  Auth. Request  |
+====================+==========================+=================+
| create             | one.cluster.allocate     | CLUSTER:CREATE  |
+--------------------+--------------------------+-----------------+
| delete             | one.cluster.delete       | CLUSTER:ADMIN   |
+--------------------+--------------------------+-----------------+
| update             | one.cluster.update       | CLUSTER:MANAGE  |
+--------------------+--------------------------+-----------------+
| addhost            | one.cluster.addhost      | CLUSTER:ADMIN   |
|                    |                          | HOST:ADMIN      |
+--------------------+--------------------------+-----------------+
| delhost            | one.cluster.delhost      | CLUSTER:ADMIN   |
|                    |                          | HOST:ADMIN      |
+--------------------+--------------------------+-----------------+
| adddatastore       | one.cluster.adddatastore | CLUSTER:ADMIN   |
|                    |                          | DATASTORE:ADMIN |
+--------------------+--------------------------+-----------------+
| deldatastore       | one.cluster.deldatastore | CLUSTER:ADMIN   |
|                    |                          | DATASTORE:ADMIN |
+--------------------+--------------------------+-----------------+
| addvnet            | one.cluster.addvnet      | CLUSTER:ADMIN   |
|                    |                          | NET:ADMIN       |
+--------------------+--------------------------+-----------------+
| delvnet            | one.cluster.delvnet      | CLUSTER:ADMIN   |
|                    |                          | NET:ADMIN       |
+--------------------+--------------------------+-----------------+
| rename             | one.cluster.rename       | CLUSTER:MANAGE  |
+--------------------+--------------------------+-----------------+
| show               | one.cluster.info         | CLUSTER:USE     |
+--------------------+--------------------------+-----------------+
| list               | one.clusterpool.info     | CLUSTER:USE     |
+--------------------+--------------------------+-----------------+

onegroup
--------

+------------------+-----------------------+-----------------------------------------+
| onegroup command |     XML-RPC Method    |              Auth. Request              |
+==================+=======================+=========================================+
| create           | one.group.allocate    | GROUP:CREATE                            |
+------------------+-----------------------+-----------------------------------------+
| delete           | one.group.delete      | GROUP:ADMIN                             |
+------------------+-----------------------+-----------------------------------------+
| show             | one.group.info        | GROUP:USE                               |
+------------------+-----------------------+-----------------------------------------+
| update           | one.group.update      | GROUP:MANAGE                            |
+------------------+-----------------------+-----------------------------------------+
| addadmin         | one.group.addadmin    | GROUP:MANAGE                            |
|                  |                       |                                         |
|                  |                       | USER:MANAGE                             |
+------------------+-----------------------+-----------------------------------------+
| deladmin         | one.group.deladmin    | GROUP:MANAGE                            |
|                  |                       |                                         |
|                  |                       | USER:MANAGE                             |
+------------------+-----------------------+-----------------------------------------+
| quota            | one.group.quota       | GROUP:ADMIN                             |
+------------------+-----------------------+-----------------------------------------+
| list             | one.grouppool.info    | GROUP:USE                               |
+------------------+-----------------------+-----------------------------------------+
| --               | one.groupquota.info   | --                                      |
+------------------+-----------------------+-----------------------------------------+
| defaultquota     | one.groupquota.update | Ony for users in the ``oneadmin`` group |
+------------------+-----------------------+-----------------------------------------+

onevdc
--------

+----------------+----------------------+-----------------+
| onevdc command |    XML-RPC Method    |  Auth. Request  |
+================+======================+=================+
| create         | one.vdc.allocate     | VDC:CREATE      |
+----------------+----------------------+-----------------+
| rename         | one.vdc.rename       | VDC:MANAGE      |
+----------------+----------------------+-----------------+
| delete         | one.vdc.delete       | VDC:ADMIN       |
+----------------+----------------------+-----------------+
| update         | one.vdc.update       | VDC:MANAGE      |
+----------------+----------------------+-----------------+
| show           | one.vdc.info         | VDC:USE         |
+----------------+----------------------+-----------------+
| list           | one.vdcpool.info     | VDC:USE         |
+----------------+----------------------+-----------------+
| addgroup       | one.vdc.addgroup     | VDC:ADMIN       |
|                |                      |                 |
|                |                      | GROUP:ADMIN     |
+----------------+----------------------+-----------------+
| delgroup       | one.vdc.delgroup     | VDC:ADMIN       |
|                |                      |                 |
|                |                      | GROUP:ADMIN     |
+----------------+----------------------+-----------------+
| addcluster     | one.vdc.addcluster   | VDC:ADMIN       |
|                |                      |                 |
|                |                      | CLUSTER:ADMIN   |
|                |                      |                 |
|                |                      | ZONE:ADMIN      |
+----------------+----------------------+-----------------+
| delcluster     | one.vdc.delcluster   | VDC:ADMIN       |
|                |                      |                 |
|                |                      | CLUSTER:ADMIN   |
|                |                      |                 |
|                |                      | ZONE:ADMIN      |
+----------------+----------------------+-----------------+
| addhost        | one.vdc.addhost      | VDC:ADMIN       |
|                |                      |                 |
|                |                      | HOST:ADMIN      |
|                |                      |                 |
|                |                      | ZONE:ADMIN      |
+----------------+----------------------+-----------------+
| delhost        | one.vdc.delhost      | VDC:ADMIN       |
|                |                      |                 |
|                |                      | HOST:ADMIN      |
|                |                      |                 |
|                |                      | ZONE:ADMIN      |
+----------------+----------------------+-----------------+
| adddatastore   | one.vdc.adddatastore | VDC:ADMIN       |
|                |                      |                 |
|                |                      | DATASTORE:ADMIN |
|                |                      |                 |
|                |                      | ZONE:ADMIN      |
+----------------+----------------------+-----------------+
| deldatastore   | one.vdc.deldatastore | VDC:ADMIN       |
|                |                      |                 |
|                |                      | DATASTORE:ADMIN |
|                |                      |                 |
|                |                      | ZONE:ADMIN      |
+----------------+----------------------+-----------------+
| addvnet        | one.vdc.addvnet      | VDC:ADMIN       |
|                |                      |                 |
|                |                      | NET:ADMIN       |
|                |                      |                 |
|                |                      | ZONE:ADMIN      |
+----------------+----------------------+-----------------+
| delvnet        | one.vdc.delvnet      | VDC:ADMIN       |
|                |                      |                 |
|                |                      | NET:ADMIN       |
|                |                      |                 |
|                |                      | ZONE:ADMIN      |
+----------------+----------------------+-----------------+

onevnet
-------

+-----------------+------------------+--------------------+
| onevnet command |  XML-RPC Method  |   Auth. Request    |
+=================+==================+====================+
| addar           | one.vn.add_ar    | NET:ADMIN          |
+-----------------+------------------+--------------------+
| rmar            | one.vn.rm_ar     | NET:ADMIN          |
+-----------------+------------------+--------------------+
| free            | one.vn.free_ar   | NET:MANAGE         |
+-----------------+------------------+--------------------+
| reserve         | one.vn.reserve   | NET:USE            |
+-----------------+------------------+--------------------+
| updatear        | one.vn.update_ar | NET:MANAGE         |
+-----------------+------------------+--------------------+
| hold            | one.vn.hold      | NET:MANAGE         |
+-----------------+------------------+--------------------+
| release         | one.vn.release   | NET:MANAGE         |
+-----------------+------------------+--------------------+
| update          | one.vn.update    | NET:MANAGE         |
+-----------------+------------------+--------------------+
| create          | one.vn.allocate  | NET:CREATE         |
+-----------------+------------------+--------------------+
| delete          | one.vn.delete    | NET:MANAGE         |
+-----------------+------------------+--------------------+
| show            | one.vn.info      | NET:USE            |
+-----------------+------------------+--------------------+
| chown           | one.vn.chown     | NET:MANAGE         |
| chgrp           |                  | [USER:MANAGE]      |
|                 |                  | [GROUP:USE]        |
+-----------------+------------------+--------------------+
| chmod           | one.vn.chmod     | NET:<MANAGE\ADMIN> |
+-----------------+------------------+--------------------+
| rename          | one.vn.rename    | NET:MANAGE         |
+-----------------+------------------+--------------------+
| list            | one.vnpool.info  | NET:USE            |
+-----------------+------------------+--------------------+

oneuser
-------

+-----------------+----------------------+-----------------------------------------+
| oneuser command |    XML-RPC Method    |              Auth. Request              |
+=================+======================+=========================================+
| create          | one.user.allocate    | USER:CREATE                             |
+-----------------+----------------------+-----------------------------------------+
| delete          | one.user.delete      | USER:ADMIN                              |
+-----------------+----------------------+-----------------------------------------+
| show            | one.user.info        | USER:USE                                |
+-----------------+----------------------+-----------------------------------------+
| passwd          | one.user.passwd      | USER:MANAGE                             |
+-----------------+----------------------+-----------------------------------------+
| login           | one.user.login       | USER:MANAGE                             |
+-----------------+----------------------+-----------------------------------------+
| update          | one.user.update      | USER:MANAGE                             |
+-----------------+----------------------+-----------------------------------------+
| chauth          | one.user.chauth      | USER:ADMIN                              |
+-----------------+----------------------+-----------------------------------------+
| quota           | one.user.quota       | USER:ADMIN                              |
+-----------------+----------------------+-----------------------------------------+
| chgrp           | one.user.chgrp       | USER:MANAGE                             |
|                 |                      | GROUP:USE                               |
+-----------------+----------------------+-----------------------------------------+
| addgroup        | one.user.addgroup    | USER:MANAGE                             |
|                 |                      | GROUP:MANAGE                            |
+-----------------+----------------------+-----------------------------------------+
| delgroup        | one.user.delgroup    | USER:MANAGE                             |
|                 |                      | GROUP:MANAGE                            |
+-----------------+----------------------+-----------------------------------------+
| encode          | --                   | --                                      |
+-----------------+----------------------+-----------------------------------------+
| list            | one.userpool.info    | USER:USE                                |
+-----------------+----------------------+-----------------------------------------+
| --              | one.userquota.info   | --                                      |
+-----------------+----------------------+-----------------------------------------+
| defaultquota    | one.userquota.update | Ony for users in the ``oneadmin`` group |
+-----------------+----------------------+-----------------------------------------+

onedatastore
------------

+----------------------+------------------------+----------------------------+
| onedatastore command |     XML-RPC Method     |       Auth. Request        |
+======================+========================+============================+
| create               | one.datastore.allocate | DATASTORE:CREATE           |
+----------------------+------------------------+----------------------------+
| delete               | one.datastore.delete   | DATASTORE:ADMIN            |
+----------------------+------------------------+----------------------------+
| show                 | one.datastore.info     | DATASTORE:USE              |
+----------------------+------------------------+----------------------------+
| update               | one.datastore.update   | DATASTORE:MANAGE           |
+----------------------+------------------------+----------------------------+
| rename               | one.datastore.rename   | DATASTORE:MANAGE           |
+----------------------+------------------------+----------------------------+
| chown                | one.datastore.chown    | DATASTORE:MANAGE           |
|                      |                        |                            |
|                      |                        | [USER:MANAGE]              |
|                      |                        |                            |
| chgrp                |                        | [GROUP:USE]                |
+----------------------+------------------------+----------------------------+
| chmod                | one.datastore.chmod    | DATASTORE:<MANAGE \ ADMIN> |
+----------------------+------------------------+----------------------------+
| enable               | one.datastore.enable   | DATASTORE:MANAGE           |
|                      |                        |                            |
| disable              |                        |                            |
+----------------------+------------------------+----------------------------+
| list                 | one.datastorepool.info | DATASTORE:USE              |
+----------------------+------------------------+----------------------------+

oneimage
--------

+------------------+---------------------------+------------------------+
| oneimage command |    XML-RPC Method         |     Auth. Request      |
+==================+===========================+========================+
| persistent       | one.image.persistent      | IMAGE:MANAGE           |
| nonpersistent    |                           |                        |
+------------------+---------------------------+------------------------+
| enable           | one.image.enable          | IMAGE:MANAGE           |
|                  |                           |                        |
| disable          |                           |                        |
+------------------+---------------------------+------------------------+
| chtype           | one.image.chtype          | IMAGE:MANAGE           |
+------------------+---------------------------+------------------------+
| snapshot-delete  | one.image.snapshotdelete  | IMAGE:MANAGE           |
+------------------+---------------------------+------------------------+
| snapshot-revert  | one.image.snapshotrevert  | IMAGE:MANAGE           |
+------------------+---------------------------+------------------------+
| snapshot-flatten | one.image.snapshotflatten | IMAGE:MANAGE           |
+------------------+---------------------------+------------------------+
| update           | one.image.update          | IMAGE:MANAGE           |
+------------------+---------------------------+------------------------+
| create           | one.image.allocate        | IMAGE:CREATE           |
|                  |                           | DATASTORE:USE          |
+------------------+---------------------------+------------------------+
| clone            | one.image.clone           | IMAGE:CREATE           |
|                  |                           | IMAGE:USE              |
|                  |                           | DATASTORE:USE          |
+------------------+---------------------------+------------------------+
| delete           | one.image.delete          | IMAGE:MANAGE           |
+------------------+---------------------------+------------------------+
| show             | one.image.info            | IMAGE:USE              |
+------------------+---------------------------+------------------------+
| chown            | one.image.chown           | IMAGE:MANAGE           |
| chgrp            |                           | [USER:MANAGE]          |
|                  |                           | [GROUP:USE]            |
+------------------+---------------------------+------------------------+
| chmod            | one.image.chmod           | IMAGE:<MANAGE \ ADMIN> |
+------------------+---------------------------+------------------------+
| rename           | one.image.rename          | IMAGE:MANAGE           |
+------------------+---------------------------+------------------------+
| list             | one.imagepool.info        | IMAGE:USE              |
| top              |                           |                        |
+------------------+---------------------------+------------------------+

onezone
--------

+-----------------+-------------------+---------------+
| onezone command |   XML-RPC Method  | Auth. Request |
+=================+===================+===============+
| create          | one.zone.allocate | ZONE:CREATE   |
+-----------------+-------------------+---------------+
| rename          | one.zone.rename   | ZONE:MANAGE   |
+-----------------+-------------------+---------------+
| update          | one.zone.update   | ZONE:MANAGE   |
+-----------------+-------------------+---------------+
| delete          | one.zone.delete   | ZONE:ADMIN    |
+-----------------+-------------------+---------------+
| show            | one.zone.info     | ZONE:USE      |
+-----------------+-------------------+---------------+
| list            | one.zonepool.info | ZONE:USE      |
+-----------------+-------------------+---------------+
| set             | --                | ZONE:USE      |
+-----------------+-------------------+---------------+

onesecgroup
--------------------------------------------------------------------------------

+---------------------+-----------------------+---------------------------+
| onesecgroup command |     XML-RPC Method    |       Auth. Request       |
+=====================+=======================+===========================+
| create              | one.secgroup.allocate | SECGROUP:CREATE           |
+---------------------+-----------------------+---------------------------+
| clone               | one.secgroup.clone    | SECGROUP:CREATE           |
|                     |                       | SECGROUP:USE              |
+---------------------+-----------------------+---------------------------+
| delete              | one.secgroup.delete   | SECGROUP:MANAGE           |
+---------------------+-----------------------+---------------------------+
| chown               | one.secgroup.chown    | SECGROUP:MANAGE           |
| chgrp               |                       | [USER:MANAGE]             |
|                     |                       | [GROUP:USE]               |
+---------------------+-----------------------+---------------------------+
| chmod               | one.secgroup.chmod    | SECGROUP:<MANAGE \ ADMIN> |
+---------------------+-----------------------+---------------------------+
| update              | one.secgroup.update   | SECGROUP:MANAGE           |
+---------------------+-----------------------+---------------------------+
| rename              | one.secgroup.rename   | SECGROUP:MANAGE           |
+---------------------+-----------------------+---------------------------+
| show                | one.secgroup.info     | SECGROUP:USE              |
+---------------------+-----------------------+---------------------------+
| list                | one.secgrouppool.info | SECGROUP:USE              |
+---------------------+-----------------------+---------------------------+

oneacl
------

+----------------+-----------------+---------------+
| oneacl command |  XML-RPC Method | Auth. Request |
+================+=================+===============+
| create         | one.acl.addrule | ACL:MANAGE    |
+----------------+-----------------+---------------+
| delete         | one.acl.delrule | ACL:MANAGE    |
+----------------+-----------------+---------------+
| list           | one.acl.info    | ACL:MANAGE    |
+----------------+-----------------+---------------+

oneacct
-------

+---------+-----------------------+---------------+
| command |     XML-RPC Method    | Auth. Request |
+=========+=======================+===============+
| oneacct | one.vmpool.accounting | VM:USE        |
+---------+-----------------------+---------------+

oneshowback
--------------------------------------------------------------------------------

+-----------+------------------------------+------------------------+
|  command  |        XML-RPC Method        |     Auth. Request      |
+===========+==============================+========================+
| list      | one.vmpool.showback          | VM:USE                 |
+-----------+------------------------------+------------------------+
| calculate | one.vmpool.calculateshowback | Only for oneadmin group|
+-----------+------------------------------+------------------------+

.. _document_api:

documents
---------

+-----------------------+---------------------------+
|     XML-RPC Method    |       Auth. Request       |
+=======================+===========================+
| one.document.update   | DOCUMENT:MANAGE           |
+-----------------------+---------------------------+
| one.document.allocate | DOCUMENT:CREATE           |
+-----------------------+---------------------------+
| one.document.delete   | DOCUMENT:MANAGE           |
+-----------------------+---------------------------+
| one.document.info     | DOCUMENT:USE              |
+-----------------------+---------------------------+
| one.document.chown    | DOCUMENT:MANAGE           |
|                       | [USER:MANAGE]             |
|                       | [GROUP:USE]               |
+-----------------------+---------------------------+
| one.document.chmod    | DOCUMENT:<MANAGE \ ADMIN> |
+-----------------------+---------------------------+
| one.document.rename   | DOCUMENT:MANAGE           |
+-----------------------+---------------------------+
| one.document.lock     | DOCUMENT:MANAGE           |
+-----------------------+---------------------------+
| one.document.unlock   | DOCUMENT:MANAGE           |
+-----------------------+---------------------------+
| one.documentpool.info | DOCUMENT:USE              |
+-----------------------+---------------------------+

system
------

+---------+--------------------+-----------------------------------------+
| command |   XML-RPC Method   |              Auth. Request              |
+=========+====================+=========================================+
| --      | one.system.version | --                                      |
+---------+--------------------+-----------------------------------------+
| --      | one.system.config  | Ony for users in the ``oneadmin`` group |
+---------+--------------------+-----------------------------------------+

Actions for Templates Management
================================

one.template.allocate
---------------------

-  **Description**: Allocates a new template in OpenNebula.
-  **Parameters**

+------+------------+------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                          Description                                           |
+======+============+================================================================================================+
| IN   | String     | The session string.                                                                            |
+------+------------+------------------------------------------------------------------------------------------------+
| IN   | String     | A string containing the template contents. Syntax can be the usual ``attribute=value`` or XML. |
+------+------------+------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                    |
+------+------------+------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The allocated resource ID / The error string.                                                  |
+------+------------+------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                    |
+------+------------+------------------------------------------------------------------------------------------------+

one.template.clone
------------------

-  **Description**: Clones an existing virtual machine template.
-  **Parameters**

+--------+--------------+-----------------------------------------------+
| Type   | Data Type    | Description                                   |
+========+==============+===============================================+
| IN     | String       | The session string.                           |
+--------+--------------+-----------------------------------------------+
| IN     | Int          | The ID of the template to be cloned.          |
+--------+--------------+-----------------------------------------------+
| IN     | String       | Name for the new template.                    |
+--------+--------------+-----------------------------------------------+
| OUT    | Boolean      | true or false whenever is successful or not   |
+--------+--------------+-----------------------------------------------+
| OUT    | Int/String   | The new template ID / The error string.       |
+--------+--------------+-----------------------------------------------+
| OUT    | Int          | Error code.                                   |
+--------+--------------+-----------------------------------------------+

one.template.delete
-------------------

-  **Description**: Deletes the given template from the pool.
-  **Parameters**

+--------+--------------+-----------------------------------------------+
| Type   | Data Type    | Description                                   |
+========+==============+===============================================+
| IN     | String       | The session string.                           |
+--------+--------------+-----------------------------------------------+
| IN     | Int          | The object ID.                                |
+--------+--------------+-----------------------------------------------+
| OUT    | Boolean      | true or false whenever is successful or not   |
+--------+--------------+-----------------------------------------------+
| OUT    | Int/String   | The resource ID / The error string.           |
+--------+--------------+-----------------------------------------------+
| OUT    | Int          | Error code.                                   |
+--------+--------------+-----------------------------------------------+

one.template.instantiate
------------------------

-  **Description**: Instantiates a new virtual machine from a template.
-  **Parameters**

+------+------------+----------------------------------------------------------------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                                                       Description                                                                        |
+======+============+==========================================================================================================================================================+
| IN   | String     | The session string.                                                                                                                                      |
+------+------------+----------------------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                                                                                           |
+------+------------+----------------------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | String     | Name for the new VM instance. If it is an empty string, OpenNebula will assign one automatically.                                                        |
+------+------------+----------------------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | Boolean    | False to create the VM on pending (default), True to create it on hold.                                                                                  |
+------+------------+----------------------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | String     | A string containing an extra template to be merged with the one being instantiated. It can be empty. Syntax can be the usual ``attribute=value`` or XML. |
+------+------------+----------------------------------------------------------------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                                                                              |
+------+------------+----------------------------------------------------------------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The new virtual machine ID / The error string.                                                                                                           |
+------+------------+----------------------------------------------------------------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                                                                              |
+------+------------+----------------------------------------------------------------------------------------------------------------------------------------------------------+

one.template.update
-------------------

-  **Description**: Replaces the template contents.
-  **Parameters**

+------+------------+--------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                           Description                                            |
+======+============+==================================================================================================+
| IN   | String     | The session string.                                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | String     | The new template contents. Syntax can be the usual ``attribute=value`` or XML.                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | Update type: **0**: replace the whole template. **1**: Merge new template with the existing one. |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+

one.template.chmod
------------------

-  **Description**: Changes the permission bits of a template.
-  **Parameters**

+------+------------+-----------------------------------------------------+
| Type | Data Type  |                     Description                     |
+======+============+=====================================================+
| IN   | String     | The session string.                                 |
+------+------------+-----------------------------------------------------+
| IN   | Int        | The object ID.                                      |
+------+------------+-----------------------------------------------------+
| IN   | Int        | USER USE bit. If set to -1, it will not change.     |
+------+------------+-----------------------------------------------------+
| IN   | Int        | USER MANAGE bit. If set to -1, it will not change.  |
+------+------------+-----------------------------------------------------+
| IN   | Int        | USER ADMIN bit. If set to -1, it will not change.   |
+------+------------+-----------------------------------------------------+
| IN   | Int        | GROUP USE bit. If set to -1, it will not change.    |
+------+------------+-----------------------------------------------------+
| IN   | Int        | GROUP MANAGE bit. If set to -1, it will not change. |
+------+------------+-----------------------------------------------------+
| IN   | Int        | GROUP ADMIN bit. If set to -1, it will not change.  |
+------+------------+-----------------------------------------------------+
| IN   | Int        | OTHER USE bit. If set to -1, it will not change.    |
+------+------------+-----------------------------------------------------+
| IN   | Int        | OTHER MANAGE bit. If set to -1, it will not change. |
+------+------------+-----------------------------------------------------+
| IN   | Int        | OTHER ADMIN bit. If set to -1, it will not change.  |
+------+------------+-----------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not         |
+------+------------+-----------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                 |
+------+------------+-----------------------------------------------------+
| OUT  | Int        | Error code.                                         |
+------+------------+-----------------------------------------------------+

one.template.chown
------------------

-  **Description**: Changes the ownership of a template.
-  **Parameters**

+------+------------+------------------------------------------------------------------------+
| Type | Data Type  |                              Description                               |
+======+============+========================================================================+
| IN   | String     | The session string.                                                    |
+------+------------+------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                         |
+------+------------+------------------------------------------------------------------------+
| IN   | Int        | The User ID of the new owner. If set to -1, the owner is not changed.  |
+------+------------+------------------------------------------------------------------------+
| IN   | Int        | The Group ID of the new group. If set to -1, the group is not changed. |
+------+------------+------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                            |
+------+------------+------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                    |
+------+------------+------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                            |
+------+------------+------------------------------------------------------------------------+

one.template.rename
-------------------

-  **Description**: Renames a template.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| IN   | String     | The new name.                               |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The VM ID / The error string.               |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.template.info
-----------------

-  **Description**: Retrieves information for the template.
-  **Parameters**

+------+-----------+--------------------------------------------------------------------------------------------------------+
| Type | Data Type |                                              Description                                               |
+======+===========+========================================================================================================+
| IN   | String    | The session string.                                                                                    |
+------+-----------+--------------------------------------------------------------------------------------------------------+
| IN   | Int       | The object ID.                                                                                         |
+------+-----------+--------------------------------------------------------------------------------------------------------+
| IN   | Boolean   | optional flag to process the template and include extended information, such as the SIZE for each DISK |
+------+-----------+--------------------------------------------------------------------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not                                                            |
+------+-----------+--------------------------------------------------------------------------------------------------------+
| OUT  | String    | The information string / The error string.                                                             |
+------+-----------+--------------------------------------------------------------------------------------------------------+
| OUT  | Int       | Error code.                                                                                            |
+------+-----------+--------------------------------------------------------------------------------------------------------+

one.templatepool.info
---------------------

-  **Description**: Retrieves information for all or part of the Resources in the pool.
-  **Parameters**

+------+-----------+-----------------------------------------------------------------------+
| Type | Data Type |                              Description                              |
+======+===========+=======================================================================+
| IN   | String    | The session string.                                                   |
+------+-----------+-----------------------------------------------------------------------+
| IN   | Int       | Filter flag                                                           |
|      |           | **- < = -3**: Connected user's resources                              |
|      |           | **- -2**: All resources                                               |
|      |           | **- -1**: Connected user's and his group's resources                  |
|      |           | **- > = 0**: UID User's Resources                                     |
+------+-----------+-----------------------------------------------------------------------+
| IN   | Int       | When the next parameter is >= -1 this is the Range start ID.          |
|      |           | Can be -1. For smaller values this is the offset used for pagination. |
+------+-----------+-----------------------------------------------------------------------+
| IN   | Int       | For values >= -1 this is the Range end ID. Can be -1 to get until the |
|      |           | last ID. For values < -1 this is the page size used for pagination.   |
+------+-----------+-----------------------------------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not                           |
+------+-----------+-----------------------------------------------------------------------+
| OUT  | String    | The information string / The error string.                            |
+------+-----------+-----------------------------------------------------------------------+
| OUT  | Int       | Error code.                                                           |
+------+-----------+-----------------------------------------------------------------------+

The range can be used to retrieve a subset of the pool, from the 'start' to the 'end' ID. To retrieve the complete pool, use ``(-1, -1)``; to retrieve all the pool from a specific ID to the last one, use ``(<id>, -1)``, and to retrieve the first elements up to an ID, use ``(0, <id>)``.

Actions for Virtual Machine Management
======================================

one.vm.allocate
---------------

-  **Description**: Allocates a new virtual machine in OpenNebula.
-  **Parameters**

+------+------------+--------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                           Description                                            |
+======+============+==================================================================================================+
| IN   | String     | The session string.                                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | String     | A string containing the template for the vm. Syntax can be the usual ``attribute=value`` or XML. |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Boolean    | False to create the VM on pending (default), True to create it on hold.                          |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The allocated resource ID / The error string.                                                    |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+

.. _api_xonevmdeploy:

one.vm.deploy
-------------

-  **Description**: initiates the instance of the given vmid on the target host.
-  **Parameters**

+------+------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                                                         Description                                                                         |
+======+============+=============================================================================================================================================================+
| IN   | String     | The session string.                                                                                                                                         |
+------+------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                                                                                              |
+------+------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | Int        | The Host ID of the target host where the VM will be deployed.                                                                                               |
+------+------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | Boolean    | true to enforce the Host capacity is not overcommitted.                                                                                                     |
+------+------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | Int        | The Datastore ID of the target system datastore where the VM will be deployed. It is optional, and can be set to -1 to let OpenNebula choose the datastore. |
+------+------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                                                                                 |
+------+------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The VM ID / The error string.                                                                                                                               |
+------+------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                                                                                 |
+------+------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+

one.vm.action
-------------

-  **Description**: submits an action to be performed on a virtual machine.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | String     | the action name to be performed, see below. |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The VM ID / The error string.               |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

The action String must be one of the following:

* **shutdown**
* **shutdown-hard**
* **hold**
* **release**
* **stop**
* **suspend**
* **resume**
* **delete**
* **delete-recreate**
* **reboot**
* **reboot-hard**
* **resched**
* **unresched**
* **poweroff**
* **poweroff-hard**
* **undeploy**
* **undeploy-hard**

one.vm.migrate
--------------

-  **Description**: migrates one virtual machine (vid) to the target host (hid).
-  **Parameters**

+------+------------+------------------------------------------------------------------------+
| Type | Data Type  |                              Description                               |
+======+============+========================================================================+
| IN   | String     | The session string.                                                    |
+------+------------+------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                         |
+------+------------+------------------------------------------------------------------------+
| IN   | Int        | the target host id (hid) where we want to migrate the vm.              |
+------+------------+------------------------------------------------------------------------+
| IN   | Boolean    | if true we are indicating that we want livemigration, otherwise false. |
+------+------------+------------------------------------------------------------------------+
| IN   | Boolean    | true to enforce the Host capacity is not overcommitted.                |
+------+------------+------------------------------------------------------------------------+
| IN   | Int        | the target system DS id where we want to migrate the vm.               |
+------+------------+------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                            |
+------+------------+------------------------------------------------------------------------+
| OUT  | Int/String | The VM ID / The error string.                                          |
+------+------------+------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                            |
+------+------------+------------------------------------------------------------------------+

one.vm.disksaveas
-----------------

-  **Description**: Sets the disk to be saved in the given image.
-  **Parameters**

+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                                                                      Description                                                                                      |
+======+============+=======================================================================================================================================================================================+
| IN   | String     | The session string.                                                                                                                                                                   |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                                                                                                                        |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | Int        | Disk ID of the disk we want to save.                                                                                                                                                  |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | String     | Name for the new Image where the disk will be saved.                                                                                                                                  |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | String     | Type for the new Image. If it is an empty string, then :ref:`the default one <oned_conf>` will be used. See the existing types in the :ref:`Image template reference <img_template>`. |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | Int        | Id of the snapshot to export, if -1 the current image state will be used.                                                                                                             |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                                                                                                           |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The new allocated Image ID / The error string.                                                                                                                                        |
|      |            |                                                                                                                                                                                       |
|      |            | If the Template was cloned, the new Template ID is not returned. The Template can be found by name: "<image_name>-<image_id>"                                                         |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                                                                                                           |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

one.vm.disksnapshotcreate
-------------------------

-  **Description**: Takes a new snapshot of the disk image
-  **Parameters**

+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                                                                      Description                                                                                      |
+======+============+=======================================================================================================================================================================================+
| IN   | String     | The session string.                                                                                                                                                                   |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                                                                                                                        |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | Int        | Disk ID of the disk we want to save.                                                                                                                                                  |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | String     | Description for the snapshot.                                                                                                                                                         |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                                                                                                           |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The new snapshot ID / The error string.                                                                                                                                               |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                                                                                                           |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

one.vm.disksnapshotdelete
-------------------------

-  **Description**: Deletes a disk snapshot
-  **Parameters**

+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                                                                      Description                                                                                      |
+======+============+=======================================================================================================================================================================================+
| IN   | String     | The session string.                                                                                                                                                                   |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                                                                                                                        |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | Int        | Disk ID of the disk we want to save.                                                                                                                                                  |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | Int        | ID of the snapshot to be deleted.                                                                                                                                                     |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                                                                                                           |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The ID of the snapshot deleted/ The error string.                                                                                                                                     |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                                                                                                           |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

one.vm.disksnapshotrevert
-------------------------

-  **Description**: Reverts disk state to a previously taken snapshot
-  **Parameters**

+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                                                                      Description                                                                                      |
+======+============+=======================================================================================================================================================================================+
| IN   | String     | The session string.                                                                                                                                                                   |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                                                                                                                        |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | Int        | Disk ID of the disk to revert its state.                                                                                                                                              |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | Int        | Snapshot ID to revert the disk state to.                                                                                                                                              |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                                                                                                           |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The snapshot ID used / The error string.                                                                                                                                              |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                                                                                                           |
+------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

one.vm.attach
-------------

-  **Description**: Attaches a new disk to the virtual machine
-  **Parameters**

+------+------------+---------------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                               Description                                               |
+======+============+=========================================================================================================+
| IN   | String     | The session string.                                                                                     |
+------+------------+---------------------------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                                          |
+------+------------+---------------------------------------------------------------------------------------------------------+
| IN   | String     | A string containing a single DISK vector attribute. Syntax can be the usual ``attribute=value`` or XML. |
+------+------------+---------------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                             |
+------+------------+---------------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The VM ID / The error string.                                                                           |
+------+------------+---------------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                             |
+------+------------+---------------------------------------------------------------------------------------------------------+

Sample DISK vector attribute:

.. code::

    DISK=[IMAGE_ID=42, TYPE=RBD, DEV_PREFIX=vd, SIZE=123456, TARGET=vdc]

one.vm.detach
-------------

-  **Description**: Detaches a disk from a virtual machine
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| IN   | Int        | The disk ID.                                |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The VM ID / The error string.               |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vm.attachnic
----------------

-  **Description**: Attaches a new network interface to the virtual machine
-  **Parameters**

+------+------------+--------------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                              Description                                               |
+======+============+========================================================================================================+
| IN   | String     | The session string.                                                                                    |
+------+------------+--------------------------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                                         |
+------+------------+--------------------------------------------------------------------------------------------------------+
| IN   | String     | A string containing a single NIC vector attribute. Syntax can be the usual ``attribute=value`` or XML. |
+------+------------+--------------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                            |
+------+------------+--------------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The VM ID / The error string.                                                                          |
+------+------------+--------------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                            |
+------+------------+--------------------------------------------------------------------------------------------------------+

one.vm.detachnic
----------------

-  **Description**: Detaches a network interface from a virtual machine
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| IN   | Int        | The nic ID.                                 |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The VM ID / The error string.               |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vm.chmod
------------

-  **Description**: Changes the permission bits of a virtual machine.
-  **Parameters**

+------+------------+-----------------------------------------------------+
| Type | Data Type  |                     Description                     |
+======+============+=====================================================+
| IN   | String     | The session string.                                 |
+------+------------+-----------------------------------------------------+
| IN   | Int        | The object ID.                                      |
+------+------------+-----------------------------------------------------+
| IN   | Int        | USER USE bit. If set to -1, it will not change.     |
+------+------------+-----------------------------------------------------+
| IN   | Int        | USER MANAGE bit. If set to -1, it will not change.  |
+------+------------+-----------------------------------------------------+
| IN   | Int        | USER ADMIN bit. If set to -1, it will not change.   |
+------+------------+-----------------------------------------------------+
| IN   | Int        | GROUP USE bit. If set to -1, it will not change.    |
+------+------------+-----------------------------------------------------+
| IN   | Int        | GROUP MANAGE bit. If set to -1, it will not change. |
+------+------------+-----------------------------------------------------+
| IN   | Int        | GROUP ADMIN bit. If set to -1, it will not change.  |
+------+------------+-----------------------------------------------------+
| IN   | Int        | OTHER USE bit. If set to -1, it will not change.    |
+------+------------+-----------------------------------------------------+
| IN   | Int        | OTHER MANAGE bit. If set to -1, it will not change. |
+------+------------+-----------------------------------------------------+
| IN   | Int        | OTHER ADMIN bit. If set to -1, it will not change.  |
+------+------------+-----------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not         |
+------+------------+-----------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                 |
+------+------------+-----------------------------------------------------+
| OUT  | Int        | Error code.                                         |
+------+------------+-----------------------------------------------------+

one.vm.chown
------------

-  **Description**: Changes the ownership of a virtual machine.
-  **Parameters**

+------+------------+------------------------------------------------------------------------+
| Type | Data Type  |                              Description                               |
+======+============+========================================================================+
| IN   | String     | The session string.                                                    |
+------+------------+------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                         |
+------+------------+------------------------------------------------------------------------+
| IN   | Int        | The User ID of the new owner. If set to -1, the owner is not changed.  |
+------+------------+------------------------------------------------------------------------+
| IN   | Int        | The Group ID of the new group. If set to -1, the group is not changed. |
+------+------------+------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                            |
+------+------------+------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                    |
+------+------------+------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                            |
+------+------------+------------------------------------------------------------------------+

one.vm.rename
-------------

-  **Description**: Renames a virtual machine
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| IN   | String     | The new name.                               |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The VM ID / The error string.               |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vm.snapshotcreate
---------------------

-  **Description**: Creates a new virtual machine snapshot
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| IN   | String     | The new snapshot name. It can be empty.     |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The new snapshot ID / The error string.     |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vm.snapshotrevert
---------------------

-  **Description**: Reverts a virtual machine to a snapshot
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| IN   | Int        | The snapshot ID.                            |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The VM ID / The error string.               |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vm.snapshotdelete
---------------------

-  **Description**: Deletes a virtual machine snapshot
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| IN   | Int        | The snapshot ID.                            |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The VM ID / The error string.               |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vm.resize
-------------

-  **Description**: Changes the capacity of the virtual machine
-  **Parameters**

+------+------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                                                                     Description                                                                                      |
+======+============+======================================================================================================================================================================================+
| IN   | String     | The session string.                                                                                                                                                                  |
+------+------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                                                                                                                       |
+------+------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | String     | Template containing the new capacity elements CPU, VCPU, MEMORY. If one of them is not present, or its value is 0, it will not be resized.                                           |
+------+------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | Boolean    | true to enforce the Host capacity is not overcommitted. This parameter is only acknoledged for users in the oneadmin group, Host capacity will be always enforced for regular users. |
+------+------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                                                                                                          |
+------+------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The VM ID / The error string.                                                                                                                                                        |
+------+------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                                                                                                          |
+------+------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

one.vm.update
-------------

-  **Description**: Replaces the **user template** contents.
-  **Parameters**

+------+------------+--------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                           Description                                            |
+======+============+==================================================================================================+
| IN   | String     | The session string.                                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | String     | The new user template contents. Syntax can be the usual ``attribute=value`` or XML.              |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | Update type: **0**: Replace the whole template. **1**: Merge new template with the existing one. |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+

one.vm.recover
--------------

-  **Description**: Recovers a stuck VM that is waiting for a driver operation. The recovery may be done by failing or succeeding the pending operation. You need to manually check the vm status on the host, to decide if the operation was successful or not.
-  **Parameters**

+------+------------+---------------------------------------------------------------------------+
| Type | Data Type  |                                Description                                |
+======+============+===========================================================================+
| IN   | String     | The session string.                                                       |
+------+------------+---------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                            |
+------+------------+---------------------------------------------------------------------------+
| IN   | Int        | Recover operation: success (1), failure (0) or retry (2)                  |
+------+------------+---------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                               |
+------+------------+---------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                       |
+------+------------+---------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                               |
+------+------------+---------------------------------------------------------------------------+

one.vm.info
-----------

-  **Description**: Retrieves information for the virtual machine.
-  **Parameters**

+------+-----------+---------------------------------------------+
| Type | Data Type |                 Description                 |
+======+===========+=============================================+
| IN   | String    | The session string.                         |
+------+-----------+---------------------------------------------+
| IN   | Int       | The object ID.                              |
+------+-----------+---------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not |
+------+-----------+---------------------------------------------+
| OUT  | String    | The information string / The error string.  |
+------+-----------+---------------------------------------------+
| OUT  | Int       | Error code.                                 |
+------+-----------+---------------------------------------------+

.. _api_onevmmonitoring:

one.vm.monitoring
-----------------

-  **Description**: Returns the virtual machine monitoring records.
-  **Parameters**

+------+-----------+-------------------------------------------------------+
| Type | Data Type |                      Description                      |
+======+===========+=======================================================+
| IN   | String    | The session string.                                   |
+------+-----------+-------------------------------------------------------+
| IN   | Int       | The object ID.                                        |
+------+-----------+-------------------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not           |
+------+-----------+-------------------------------------------------------+
| OUT  | String    | The monitoring information string / The error string. |
+------+-----------+-------------------------------------------------------+
| OUT  | Int       | Error code.                                           |
+------+-----------+-------------------------------------------------------+

The monitoring information returned is a list of VM elements. Each VM element contains the complete xml of the VM with the updated information returned by the poll action.

For example:

.. code::

    <MONITORING_DATA>
        <VM>
            ...
            <LAST_POLL>123</LAST_POLL>
            ...
        </VM>
        <VM>
            ...
            <LAST_POLL>456</LAST_POLL>
            ...
        </VM>
    </MONITORING_DATA>

one.vmpool.info
---------------

-  **Description**: Retrieves information for all or part of the VMs in the pool.
-  **Parameters**

+------+-----------+-----------------------------------------------------------------------+
| Type | Data Type |                              Description                              |
+======+===========+=======================================================================+
| IN   | String    | The session string.                                                   |
+------+-----------+-----------------------------------------------------------------------+
| IN   | Int       | Filter flag                                                           |
|      |           | **- < = -3**: Connected user's resources                              |
|      |           | **- -2**: All resources                                               |
|      |           | **- -1**: Connected user's and his group's resources                  |
|      |           | **- > = 0**: UID User's Resources                                     |
+------+-----------+-----------------------------------------------------------------------+
| IN   | Int       | When the next parameter is >= -1 this is the Range start ID.          |
|      |           | Can be -1. For smaller values this is the offset used for pagination. |
+------+-----------+-----------------------------------------------------------------------+
| IN   | Int       | For values >= -1 this is the Range end ID. Can be -1 to get until the |
|      |           | last ID. For values < -1 this is the page size used for pagination.   |
+------+-----------+-----------------------------------------------------------------------+
| IN   | Int       | VM state to filter by.                                                |
+------+-----------+-----------------------------------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not                           |
+------+-----------+-----------------------------------------------------------------------+
| OUT  | String    | The information string / The error string.                            |
+------+-----------+-----------------------------------------------------------------------+
| OUT  | Int       | Error code.                                                           |
+------+-----------+-----------------------------------------------------------------------+

The range can be used to retrieve a subset of the pool, from the 'start' to the 'end' ID. To retrieve the complete pool, use ``(-1, -1)``; to retrieve all the pool from a specific ID to the last one, use ``(<id>, -1)``, and to retrieve the first elements up to an ID, use ``(0, <id>)``.

The state filter can be one of the following:

+-------+---------------------------+
| Value |           State           |
+=======+===========================+
|    -2 | Any state, including DONE |
+-------+---------------------------+
|    -1 | Any state, except DONE    |
+-------+---------------------------+
|     0 | INIT                      |
+-------+---------------------------+
|     1 | PENDING                   |
+-------+---------------------------+
|     2 | HOLD                      |
+-------+---------------------------+
|     3 | ACTIVE                    |
+-------+---------------------------+
|     4 | STOPPED                   |
+-------+---------------------------+
|     5 | SUSPENDED                 |
+-------+---------------------------+
|     6 | DONE                      |
+-------+---------------------------+
|     8 | POWEROFF                  |
+-------+---------------------------+
|     9 | UNDEPLOYED                |
+-------+---------------------------+

.. warning::

  Value 7 is reserved for FAILED VMs for compatibility reasons.

one.vmpool.monitoring
---------------------

-  **Description**: Returns all the virtual machine monitoring records.
-  **Parameters**

+------+-----------+------------------------------------------------------+
| Type | Data Type |                     Description                      |
+======+===========+======================================================+
| IN   | String    | The session string.                                  |
+------+-----------+------------------------------------------------------+
| IN   | Int       | Filter flag                                          |
|      |           | **- < = -3**: Connected user's resources             |
|      |           | **- -2**: All resources                              |
|      |           | **- -1**: Connected user's and his group's resources |
|      |           | **- > = 0**: UID User's Resources                    |
+------+-----------+------------------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not          |
+------+-----------+------------------------------------------------------+
| OUT  | String    | The information string / The error string.           |
+------+-----------+------------------------------------------------------+
| OUT  | Int       | Error code.                                          |
+------+-----------+------------------------------------------------------+

See :ref:`one.vm.monitoring <api_onevmmonitoring>`.

Sample output:

.. code::

    <MONITORING_DATA>
        <VM>
            <ID>0</ID>
            <LAST_POLL>123</LAST_POLL>
            ...
        </VM>
        <VM>
            <ID>0</ID>
            <LAST_POLL>456</LAST_POLL>
            ...
        </VM>
        <VM>
            <ID>3</ID>
            <LAST_POLL>123</LAST_POLL>
            ...
        </VM>
        <VM>
            <ID>3</ID>
            <LAST_POLL>456</LAST_POLL>
            ...
        </VM>
    </MONITORING_DATA>

one.vmpool.accounting
---------------------

-  **Description**: Returns the virtual machine history records.
-  **Parameters**

+------+-----------+----------------------------------------------------------------------------------------------------------+
| Type | Data Type |                                               Description                                                |
+======+===========+==========================================================================================================+
| IN   | String    | The session string.                                                                                      |
+------+-----------+----------------------------------------------------------------------------------------------------------+
| IN   | Int       | Filter flag                                                                                              |
|      |           | **- < = -3**: Connected user's resources                                                                 |
|      |           | **- -2**: All resources                                                                                  |
|      |           | **- -1**: Connected user's and his group's resources                                                     |
|      |           | **- > = 0**: UID User's Resources                                                                        |
+------+-----------+----------------------------------------------------------------------------------------------------------+
| IN   | Int       | Start time for the time interval. Can be -1, in which case the time interval won't have a left boundary. |
+------+-----------+----------------------------------------------------------------------------------------------------------+
| IN   | Int       | End time for the time interval. Can be -1, in which case the time interval won't have a right boundary.  |
+------+-----------+----------------------------------------------------------------------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not                                                              |
+------+-----------+----------------------------------------------------------------------------------------------------------+
| OUT  | String    | The information string / The error string.                                                               |
+------+-----------+----------------------------------------------------------------------------------------------------------+
| OUT  | Int       | Error code.                                                                                              |
+------+-----------+----------------------------------------------------------------------------------------------------------+

The XML output is explained in detail in the :ref:`''oneacct'' guide <accounting>`.

one.vmpool.showback
---------------------

-  **Description**: Returns the virtual machine showback records
-  **Parameters**

+------+-----------+-------------------------------------------------------------------------------------------------------------------------+
| Type | Data Type |                                                       Description                                                       |
+======+===========+=========================================================================================================================+
| IN   | String    | The session string.                                                                                                     |
+------+-----------+-------------------------------------------------------------------------------------------------------------------------+
| IN   | Int       | First month for the time interval. January is 1. Can be -1, in which case the time interval won't have a left boundary. |
+------+-----------+-------------------------------------------------------------------------------------------------------------------------+
| IN   | Int       | First year for the time interval. Can be -1, in which case the time interval won't have a left boundary.                |
+------+-----------+-------------------------------------------------------------------------------------------------------------------------+
| IN   | Int       | Last month for the time interval. January is 1. Can be -1, in which case the time interval won't have a right boundary. |
+------+-----------+-------------------------------------------------------------------------------------------------------------------------+
| IN   | Int       | Last year for the time interval. Can be -1, in which case the time interval won't have a right boundary.                |
+------+-----------+-------------------------------------------------------------------------------------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not                                                                             |
+------+-----------+-------------------------------------------------------------------------------------------------------------------------+
| OUT  | String    | The information string / The error string.                                                                              |
+------+-----------+-------------------------------------------------------------------------------------------------------------------------+
| OUT  | Int       | Error code.                                                                                                             |
+------+-----------+-------------------------------------------------------------------------------------------------------------------------+

The XML output will be similar to this one:

.. code::

    <SHOWBACK_RECORDS>

      <SHOWBACK>
        <VMID>4315</VMID>
        <VMNAME>vm_4315</VMNAME>
        <UID>2467</UID>
        <GID>102</GID>
        <UNAME>cloud_user</UNAME>
        <GNAME>vdc-test</GNAME>
        <YEAR>2014</YEAR>
        <MONTH>11</MONTH>
        <CPU_COST>13</CPU_COST>
        <MEMORY_COST>21</MEMORY_COST>
        <DISK_COST>7</DISK_COST>
        <TOTAL_COST>41</TOTAL_COST>
        <HOURS>10</HOURS>
      </SHOWBACK>

      <SHOWBACK>
        ...
      </SHOWBACK>

      ...
    </SHOWBACK_RECORDS>

one.vmpool.calculateshowback
--------------------------------------------------------------------------------

-  **Description**: Processes all the history records, and stores the monthly cost for each VM
-  **Parameters**

+------+-----------+-------------------------------------------------------------------------------------------------------------------------+
| Type | Data Type |                                                       Description                                                       |
+======+===========+=========================================================================================================================+
| IN   | String    | The session string.                                                                                                     |
+------+-----------+-------------------------------------------------------------------------------------------------------------------------+
| IN   | Int       | First month for the time interval. January is 1. Can be -1, in which case the time interval won't have a left boundary. |
+------+-----------+-------------------------------------------------------------------------------------------------------------------------+
| IN   | Int       | First year for the time interval. Can be -1, in which case the time interval won't have a left boundary.                |
+------+-----------+-------------------------------------------------------------------------------------------------------------------------+
| IN   | Int       | Last month for the time interval. January is 1. Can be -1, in which case the time interval won't have a right boundary. |
+------+-----------+-------------------------------------------------------------------------------------------------------------------------+
| IN   | Int       | Last year for the time interval. Can be -1, in which case the time interval won't have a right boundary.                |
+------+-----------+-------------------------------------------------------------------------------------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not                                                                             |
+------+-----------+-------------------------------------------------------------------------------------------------------------------------+
| OUT  | String    | Empty / The error string.                                                                                               |
+------+-----------+-------------------------------------------------------------------------------------------------------------------------+
| OUT  | Int       | Error code.                                                                                                             |
+------+-----------+-------------------------------------------------------------------------------------------------------------------------+

Actions for Hosts Management
============================

one.host.allocate
-----------------

-  **Description**: Allocates a new host in OpenNebula
-  **Parameters**

+------+------------+----------------------------------------------------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                                                 Description                                                                  |
+======+============+==============================================================================================================================================+
| IN   | String     | The session string.                                                                                                                          |
+------+------------+----------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | String     | Hostname of the machine we want to add                                                                                                       |
+------+------------+----------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | String     | The name of the information manager (im\_mad\_name), this values are taken from the oned.conf with the tag name IM\_MAD (name)               |
+------+------------+----------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | String     | The name of the virtual machine manager mad name (vmm\_mad\_name), this values are taken from the oned.conf with the tag name VM\_MAD (name) |
+------+------------+----------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | String     | The name of the virtual network manager mad name (vnm\_mad\_name), see the :ref:`Networking Subsystem documentation <nm>`                    |
+------+------------+----------------------------------------------------------------------------------------------------------------------------------------------+
| IN   | Int        | The cluster ID. If it is -1, this host won't be added to any cluster.                                                                        |
+------+------------+----------------------------------------------------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                                                                  |
+------+------------+----------------------------------------------------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The allocated Host ID / The error string.                                                                                                    |
+------+------------+----------------------------------------------------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                                                                  |
+------+------------+----------------------------------------------------------------------------------------------------------------------------------------------+

one.host.delete
---------------

-  **Description**: Deletes the given host from the pool
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.host.enable
---------------

-  **Description**: Enables or disables the given host
-  **Parameters**

+------+------------+------------------------------------------------------------+
| Type | Data Type  |                        Description                         |
+======+============+============================================================+
| IN   | String     | The session string.                                        |
+------+------------+------------------------------------------------------------+
| IN   | Int        | The Host ID.                                               |
+------+------------+------------------------------------------------------------+
| IN   | Boolean    | Set it to true/false to enable or disable the target Host. |
+------+------------+------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                |
+------+------------+------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                        |
+------+------------+------------------------------------------------------------+
| OUT  | Int        | Error code.                                                |
+------+------------+------------------------------------------------------------+

one.host.update
---------------

-  **Description**: Replaces the host's template contents.
-  **Parameters**

+------+------------+--------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                           Description                                            |
+======+============+==================================================================================================+
| IN   | String     | The session string.                                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | String     | The new template contents. Syntax can be the usual ``attribute=value`` or XML.                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | Update type: **0**: Replace the whole template. **1**: Merge new template with the existing one. |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+

one.host.rename
---------------

-  **Description**: Renames a host.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| IN   | String     | The new name.                               |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The VM ID / The error string.               |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.host.info
-------------

-  **Description**: Retrieves information for the host.
-  **Parameters**

+------+-----------+---------------------------------------------+
| Type | Data Type |                 Description                 |
+======+===========+=============================================+
| IN   | String    | The session string.                         |
+------+-----------+---------------------------------------------+
| IN   | Int       | The object ID.                              |
+------+-----------+---------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not |
+------+-----------+---------------------------------------------+
| OUT  | String    | The information string / The error string.  |
+------+-----------+---------------------------------------------+
| OUT  | Int       | Error code.                                 |
+------+-----------+---------------------------------------------+

.. _api_onehostmonitoring:

one.host.monitoring
-------------------

-  **Description**: Returns the host monitoring records.
-  **Parameters**

+------+-----------+-------------------------------------------------------+
| Type | Data Type |                      Description                      |
+======+===========+=======================================================+
| IN   | String    | The session string.                                   |
+------+-----------+-------------------------------------------------------+
| IN   | Int       | The object ID.                                        |
+------+-----------+-------------------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not           |
+------+-----------+-------------------------------------------------------+
| OUT  | String    | The monitoring information string / The error string. |
+------+-----------+-------------------------------------------------------+
| OUT  | Int       | Error code.                                           |
+------+-----------+-------------------------------------------------------+

The monitoring information returned is a list of HOST elements. Each HOST element contains the complete xml of the host with the updated information returned by the poll action.

For example:

.. code::

    <MONITORING_DATA>
        <HOST>
            ...
            <LAST_MON_TIME>123</LAST_MON_TIME>
            ...
        </HOST>
        <HOST>
            ...
            <LAST_MON_TIME>456</LAST_MON_TIME>
            ...
        </HOST>
    </MONITORING_DATA>

one.hostpool.info
-----------------

-  **Description**: Retrieves information for all the hosts in the pool.
-  **Parameters**

+------+-----------+---------------------------------------------+
| Type | Data Type |                 Description                 |
+======+===========+=============================================+
| IN   | String    | The session string.                         |
+------+-----------+---------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not |
+------+-----------+---------------------------------------------+
| OUT  | String    | The information string / The error string.  |
+------+-----------+---------------------------------------------+
| OUT  | Int       | Error code.                                 |
+------+-----------+---------------------------------------------+

one.hostpool.monitoring
-----------------------

-  **Description**: Returns all the host monitoring records.
-  **Parameters**

+------+-----------+---------------------------------------------+
| Type | Data Type |                 Description                 |
+======+===========+=============================================+
| IN   | String    | The session string.                         |
+------+-----------+---------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not |
+------+-----------+---------------------------------------------+
| OUT  | String    | The information string / The error string.  |
+------+-----------+---------------------------------------------+
| OUT  | Int       | Error code.                                 |
+------+-----------+---------------------------------------------+

Sample output:

.. code::

    <MONITORING_DATA>
        <HOST>
            <ID>0</ID>
            <LAST_MON_TIME>123</LAST_MON_TIME>
            ...
        </HOST>
        <HOST>
            <ID>0</ID>
            <LAST_MON_TIME>456</LAST_MON_TIME>
            ...
        </HOST>
        <HOST>
            <ID>3</ID>
            <LAST_MON_TIME>123</LAST_MON_TIME>
            ...
        </HOST>
        <HOST>
            <ID>3</ID>
            <LAST_MON_TIME>456</LAST_MON_TIME>
            ...
        </HOST>
    </MONITORING_DATA>

Actions for Cluster Management
==============================

one.cluster.allocate
--------------------

-  **Description**: Allocates a new cluster in OpenNebula.
-  **Parameters**

+------+------------+----------------------------------------------+
| Type | Data Type  |                 Description                  |
+======+============+==============================================+
| IN   | String     | The session string.                          |
+------+------------+----------------------------------------------+
| IN   | String     | Name for the new cluster.                    |
+------+------------+----------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not  |
+------+------------+----------------------------------------------+
| OUT  | Int/String | The allocated cluster ID / The error string. |
+------+------------+----------------------------------------------+
| OUT  | Int        | Error code.                                  |
+------+------------+----------------------------------------------+

one.cluster.delete
------------------

-  **Description**: Deletes the given cluster from the pool.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.cluster.update
------------------

-  **Description**: Replaces the cluster template contents.
-  **Parameters**

+------+------------+--------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                           Description                                            |
+======+============+==================================================================================================+
| IN   | String     | The session string.                                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | String     | The new template contents. Syntax can be the usual ``attribute=value`` or XML.                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | Update type: **0**: Replace the whole template. **1**: Merge new template with the existing one. |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+

one.cluster.addhost
-------------------

-  **Description**: Adds a host to the given cluster.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The cluster ID.                             |
+------+------------+---------------------------------------------+
| IN   | Int        | The host ID.                                |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.cluster.delhost
-------------------

-  **Description**: Removes a host from the given cluster.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The cluster ID.                             |
+------+------------+---------------------------------------------+
| IN   | Int        | The host ID.                                |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.cluster.adddatastore
------------------------

-  **Description**: Adds a datastore to the given cluster.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The cluster ID.                             |
+------+------------+---------------------------------------------+
| IN   | Int        | The datastore ID.                           |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.cluster.deldatastore
------------------------

-  **Description**: Removes a datastore from the given cluster.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The cluster ID.                             |
+------+------------+---------------------------------------------+
| IN   | Int        | The datastore ID.                           |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.cluster.addvnet
-------------------

-  **Description**: Adds a vnet to the given cluster.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The cluster ID.                             |
+------+------------+---------------------------------------------+
| IN   | Int        | The vnet ID.                                |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.cluster.delvnet
-------------------

-  **Description**: Removes a vnet from the given cluster.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The cluster ID.                             |
+------+------------+---------------------------------------------+
| IN   | Int        | The vnet ID.                                |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.cluster.rename
------------------

-  **Description**: Renames a cluster.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| IN   | String     | The new name.                               |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The VM ID / The error string.               |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.cluster.info
----------------

-  **Description**: Retrieves information for the cluster.
-  **Parameters**

+------+-----------+---------------------------------------------+
| Type | Data Type |                 Description                 |
+======+===========+=============================================+
| IN   | String    | The session string.                         |
+------+-----------+---------------------------------------------+
| IN   | Int       | The object ID.                              |
+------+-----------+---------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not |
+------+-----------+---------------------------------------------+
| OUT  | String    | The information string / The error string.  |
+------+-----------+---------------------------------------------+
| OUT  | Int       | Error code.                                 |
+------+-----------+---------------------------------------------+

one.clusterpool.info
--------------------

-  **Description**: Retrieves information for all the clusters in the pool.
-  **Parameters**

+------+-----------+---------------------------------------------+
| Type | Data Type |                 Description                 |
+======+===========+=============================================+
| IN   | String    | The session string.                         |
+------+-----------+---------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not |
+------+-----------+---------------------------------------------+
| OUT  | String    | The information string / The error string.  |
+------+-----------+---------------------------------------------+
| OUT  | Int       | Error code.                                 |
+------+-----------+---------------------------------------------+

Actions for Virtual Network Management
======================================

one.vn.allocate
---------------

-  **Description**: Allocates a new virtual network in OpenNebula.
-  **Parameters**

+------+------------+--------------------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                                 Description                                                  |
+======+============+==============================================================================================================+
| IN   | String     | The session string.                                                                                          |
+------+------------+--------------------------------------------------------------------------------------------------------------+
| IN   | String     | A string containing the template of the virtual network. Syntax can be the usual ``attribute=value`` or XML. |
+------+------------+--------------------------------------------------------------------------------------------------------------+
| IN   | Int        | The cluster ID. If it is -1, this virtual network won't be added to any cluster.                             |
+------+------------+--------------------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                                  |
+------+------------+--------------------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The allocated resource ID / The error string.                                                                |
+------+------------+--------------------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                                  |
+------+------------+--------------------------------------------------------------------------------------------------------------+

one.vn.delete
-------------

-  **Description**: Deletes the given virtual network from the pool.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+


one.vn.add_ar
--------------------------------------------------------------------------------

-  **Description**: Adds address ranges to a virtual network.
-  **Parameters**

+------+------------+-------------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                              Description                                              |
+======+============+=======================================================================================================+
| IN   | String     | The session string.                                                                                   |
+------+------------+-------------------------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                                        |
+------+------------+-------------------------------------------------------------------------------------------------------+
| IN   | String     | template of the address ranges to add. Syntax can be the usual ``attribute=value`` or XML, see below. |
+------+------------+-------------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                           |
+------+------------+-------------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                                                   |
+------+------------+-------------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                           |
+------+------------+-------------------------------------------------------------------------------------------------------+

Examples of valid templates:

.. code::

    AR = [
        TYPE = IP4,
        IP = 192.168.0.5,
        SIZE = 10 ]

.. code::

    <TEMPLATE>
      <AR>
        <TYPE>IP4</TYPE>
        <IP>192.168.0.5</IP>
        <SIZE>10</SIZE>
      </AR>
    </TEMPLATE>

one.vn.rm_ar
---------------

-  **Description**: Removes an address range from a virtual network.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| IN   | Int        | ID of the address range to remove.          |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+


one.vn.update_ar
--------------------------------------------------------------------------------

-  **Description**: Updates the attributes of an address range.
-  **Parameters**

+------+------------+----------------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                               Description                                                |
+======+============+==========================================================================================================+
| IN   | String     | The session string.                                                                                      |
+------+------------+----------------------------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                                           |
+------+------------+----------------------------------------------------------------------------------------------------------+
| IN   | String     | template of the address ranges to update. Syntax can be the usual ``attribute=value`` or XML, see below. |
+------+------------+----------------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                              |
+------+------------+----------------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                                                      |
+------+------------+----------------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                              |
+------+------------+----------------------------------------------------------------------------------------------------------+

Examples of valid templates:

.. code::

    AR = [
        AR_ID = 7,
        GATEWAY = "192.168.30.2",
        EXTRA_ATT = "VALUE",
        SIZE = 10 ]

.. code::

    <TEMPLATE>
      <AR>
        <AR_ID>7</AR_ID>
        <GATEWAY>192.168.30.2</GATEWAY>
        <EXTRA_ATT>VALUE</EXTRA_ATT>
        <SIZE>10</SIZE>
      </AR>
    </TEMPLATE>


one.vn.reserve
--------------------------------------------------------------------------------

-  **Description**: Reserve network addresses.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The virtual network to reserve from.        |
+------+------------+---------------------------------------------+
| IN   | String     | Template, see below.                        |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

The third parameter must be an OpenNebula ATTRIBUTE=VALUE template, with these values:

+------------+------------------------------------------------------------------------------------------------------------------------+-----------+
| Attribute  |                                                      Description                                                       | Mandatory |
+============+========================================================================================================================+===========+
| SIZE       | Size of the reservation                                                                                                | YES       |
+------------+------------------------------------------------------------------------------------------------------------------------+-----------+
| NAME       | If set, the reservation will be created in a new Virtual Network with this name                                        | NO        |
+------------+------------------------------------------------------------------------------------------------------------------------+-----------+
| AR_ID      | ID of the AR from where to take the addresses                                                                          | NO        |
+------------+------------------------------------------------------------------------------------------------------------------------+-----------+
| NETWORK_ID | Instead of creating a new Virtual Network, the reservation will be added to the existing virtual network with this ID. | NO        |
+------------+------------------------------------------------------------------------------------------------------------------------+-----------+
| MAC        | First MAC address to start the reservation range [MAC, MAC+SIZE)                                                       | NO        |
+------------+------------------------------------------------------------------------------------------------------------------------+-----------+
| IP         | First IPv4 address to start the reservation range [IP, IP+SIZE)                                                        | NO        |
+------------+------------------------------------------------------------------------------------------------------------------------+-----------+

one.vn.free_ar
---------------

-  **Description**: Frees a reserved address range from a virtual network.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| IN   | Int        | ID of the address range to free.            |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+



one.vn.hold
-----------

-  **Description**: Holds a virtual network Lease as used.
-  **Parameters**

+------+------------+------------------------------------------------------------------+
| Type | Data Type  |                           Description                            |
+======+============+==================================================================+
| IN   | String     | The session string.                                              |
+------+------------+------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                   |
+------+------------+------------------------------------------------------------------+
| IN   | String     | template of the lease to hold, e.g. ``LEASES=[IP=192.168.0.5]``. |
+------+------------+------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                      |
+------+------------+------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                              |
+------+------------+------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                      |
+------+------------+------------------------------------------------------------------+

one.vn.release
--------------

-  **Description**: Releases a virtual network Lease on hold.
-  **Parameters**

+------+------------+---------------------------------------------------------------------+
| Type | Data Type  |                             Description                             |
+======+============+=====================================================================+
| IN   | String     | The session string.                                                 |
+------+------------+---------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                      |
+------+------------+---------------------------------------------------------------------+
| IN   | String     | template of the lease to release, e.g. ``LEASES=[IP=192.168.0.5]``. |
+------+------------+---------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                         |
+------+------------+---------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                 |
+------+------------+---------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                         |
+------+------------+---------------------------------------------------------------------+

one.vn.update
-------------

-  **Description**: Replaces the virtual network template contents.
-  **Parameters**

+------+------------+--------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                           Description                                            |
+======+============+==================================================================================================+
| IN   | String     | The session string.                                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | String     | The new template contents. Syntax can be the usual ``attribute=value`` or XML.                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | Update type: **0**: Replace the whole template. **1**: Merge new template with the existing one. |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+

one.vn.chmod
------------

-  **Description**: Changes the permission bits of a virtual network.
-  **Parameters**

+------+------------+-----------------------------------------------------+
| Type | Data Type  |                     Description                     |
+======+============+=====================================================+
| IN   | String     | The session string.                                 |
+------+------------+-----------------------------------------------------+
| IN   | Int        | The object ID.                                      |
+------+------------+-----------------------------------------------------+
| IN   | Int        | USER USE bit. If set to -1, it will not change.     |
+------+------------+-----------------------------------------------------+
| IN   | Int        | USER MANAGE bit. If set to -1, it will not change.  |
+------+------------+-----------------------------------------------------+
| IN   | Int        | USER ADMIN bit. If set to -1, it will not change.   |
+------+------------+-----------------------------------------------------+
| IN   | Int        | GROUP USE bit. If set to -1, it will not change.    |
+------+------------+-----------------------------------------------------+
| IN   | Int        | GROUP MANAGE bit. If set to -1, it will not change. |
+------+------------+-----------------------------------------------------+
| IN   | Int        | GROUP ADMIN bit. If set to -1, it will not change.  |
+------+------------+-----------------------------------------------------+
| IN   | Int        | OTHER USE bit. If set to -1, it will not change.    |
+------+------------+-----------------------------------------------------+
| IN   | Int        | OTHER MANAGE bit. If set to -1, it will not change. |
+------+------------+-----------------------------------------------------+
| IN   | Int        | OTHER ADMIN bit. If set to -1, it will not change.  |
+------+------------+-----------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not         |
+------+------------+-----------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                 |
+------+------------+-----------------------------------------------------+
| OUT  | Int        | Error code.                                         |
+------+------------+-----------------------------------------------------+

one.vn.chown
------------

-  **Description**: Changes the ownership of a virtual network.
-  **Parameters**

+------+------------+------------------------------------------------------------------------+
| Type | Data Type  |                              Description                               |
+======+============+========================================================================+
| IN   | String     | The session string.                                                    |
+------+------------+------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                         |
+------+------------+------------------------------------------------------------------------+
| IN   | Int        | The User ID of the new owner. If set to -1, the owner is not changed.  |
+------+------------+------------------------------------------------------------------------+
| IN   | Int        | The Group ID of the new group. If set to -1, the group is not changed. |
+------+------------+------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                            |
+------+------------+------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                    |
+------+------------+------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                            |
+------+------------+------------------------------------------------------------------------+

one.vn.rename
-------------

-  **Description**: Renames a virtual network.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| IN   | String     | The new name.                               |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The VM ID / The error string.               |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vn.info
-----------

-  **Description**: Retrieves information for the virtual network.
-  **Parameters**

+------+-----------+---------------------------------------------+
| Type | Data Type |                 Description                 |
+======+===========+=============================================+
| IN   | String    | The session string.                         |
+------+-----------+---------------------------------------------+
| IN   | Int       | The object ID.                              |
+------+-----------+---------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not |
+------+-----------+---------------------------------------------+
| OUT  | String    | The information string / The error string.  |
+------+-----------+---------------------------------------------+
| OUT  | Int       | Error code.                                 |
+------+-----------+---------------------------------------------+

.. note:: The ACL rules do not apply to VNET reserveations in the same way as they do to normal VNETs and other objects. Read more in the :ref:`ACL documentation guide <manage_acl_vnet_reservations>`.

one.vnpool.info
---------------

-  **Description**: Retrieves information for all or part of the virtual networks in the pool.
-  **Parameters**

+------+-----------+-----------------------------------------------------------------------+
| Type | Data Type |                              Description                              |
+======+===========+=======================================================================+
| IN   | String    | The session string.                                                   |
+------+-----------+-----------------------------------------------------------------------+
| IN   | Int       | Filter flag                                                           |
|      |           | **- < = -3**: Connected user's resources                              |
|      |           | **- -2**: All resources                                               |
|      |           | **- -1**: Connected user's and his group's resources                  |
|      |           | **- > = 0**: UID User's Resources                                     |
+------+-----------+-----------------------------------------------------------------------+
| IN   | Int       | When the next parameter is >= -1 this is the Range start ID.          |
|      |           | Can be -1. For smaller values this is the offset used for pagination. |
+------+-----------+-----------------------------------------------------------------------+
| IN   | Int       | For values >= -1 this is the Range end ID. Can be -1 to get until the |
|      |           | last ID. For values < -1 this is the page size used for pagination.   |
+------+-----------+-----------------------------------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not                           |
+------+-----------+-----------------------------------------------------------------------+
| OUT  | String    | The information string / The error string.                            |
+------+-----------+-----------------------------------------------------------------------+
| OUT  | Int       | Error code.                                                           |
+------+-----------+-----------------------------------------------------------------------+

The range can be used to retrieve a subset of the pool, from the 'start' to the 'end' ID. To retrieve the complete pool, use ``(-1, -1)``; to retrieve all the pool from a specific ID to the last one, use ``(<id>, -1)``, and to retrieve the first elements up to an ID, use ``(0, <id>)``.

.. note:: The ACL rules do not apply to VNET reserveations in the same way as they do to normal VNETs and other objects. Read more in the :ref:`ACL documentation guide <manage_acl_vnet_reservations>`.

Actions for Security Group Management
================================================================================

one.secgroup.allocate
--------------------------------------------------------------------------------

-  **Description**: Allocates a new security group in OpenNebula.
-  **Parameters**

+------+------------+-------------------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                                 Description                                                 |
+======+============+=============================================================================================================+
| IN   | String     | The session string.                                                                                         |
+------+------------+-------------------------------------------------------------------------------------------------------------+
| IN   | String     | A string containing the template of the security group. Syntax can be the usual ``attribute=value`` or XML. |
+------+------------+-------------------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                                 |
+------+------------+-------------------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The allocated resource ID / The error string.                                                               |
+------+------------+-------------------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                                 |
+------+------------+-------------------------------------------------------------------------------------------------------------+

one.secgroup.clone
--------------------------------------------------------------------------------

-  **Description**: Clones an existing security group.
-  **Parameters**

+------+------------+------------------------------------------------------------------------------------+
| Type | Data Type  |                                    Description                                     |
+======+============+====================================================================================+
| IN   | String     | The session string.                                                                |
+------+------------+------------------------------------------------------------------------------------+
| IN   | Int        | The ID of the security group to be cloned.                                         |
+------+------------+------------------------------------------------------------------------------------+
| IN   | String     | Name for the new security group.                                                   |
+------+------------+------------------------------------------------------------------------------------+
| IN   | Int        | The ID of the target datastore. Optional, can be set to -1 to use the current one. |
+------+------------+------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                        |
+------+------------+------------------------------------------------------------------------------------+
| OUT  | Int/String | The new security group ID / The error string.                                      |
+------+------------+------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                        |
+------+------------+------------------------------------------------------------------------------------+

one.secgroup.delete
--------------------------------------------------------------------------------

-  **Description**: Deletes the given security group from the pool.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.secgroup.update
--------------------------------------------------------------------------------

-  **Description**: Replaces the security group template contents.
-  **Parameters**

+------+------------+--------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                           Description                                            |
+======+============+==================================================================================================+
| IN   | String     | The session string.                                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | String     | The new template contents. Syntax can be the usual ``attribute=value`` or XML.                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | Update type: **0**: Replace the whole template. **1**: Merge new template with the existing one. |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+

one.secgroup.chmod
--------------------------------------------------------------------------------

-  **Description**: Changes the permission bits of a security group.
-  **Parameters**

+------+------------+-----------------------------------------------------+
| Type | Data Type  |                     Description                     |
+======+============+=====================================================+
| IN   | String     | The session string.                                 |
+------+------------+-----------------------------------------------------+
| IN   | Int        | The object ID.                                      |
+------+------------+-----------------------------------------------------+
| IN   | Int        | USER USE bit. If set to -1, it will not change.     |
+------+------------+-----------------------------------------------------+
| IN   | Int        | USER MANAGE bit. If set to -1, it will not change.  |
+------+------------+-----------------------------------------------------+
| IN   | Int        | USER ADMIN bit. If set to -1, it will not change.   |
+------+------------+-----------------------------------------------------+
| IN   | Int        | GROUP USE bit. If set to -1, it will not change.    |
+------+------------+-----------------------------------------------------+
| IN   | Int        | GROUP MANAGE bit. If set to -1, it will not change. |
+------+------------+-----------------------------------------------------+
| IN   | Int        | GROUP ADMIN bit. If set to -1, it will not change.  |
+------+------------+-----------------------------------------------------+
| IN   | Int        | OTHER USE bit. If set to -1, it will not change.    |
+------+------------+-----------------------------------------------------+
| IN   | Int        | OTHER MANAGE bit. If set to -1, it will not change. |
+------+------------+-----------------------------------------------------+
| IN   | Int        | OTHER ADMIN bit. If set to -1, it will not change.  |
+------+------------+-----------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not         |
+------+------------+-----------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                 |
+------+------------+-----------------------------------------------------+
| OUT  | Int        | Error code.                                         |
+------+------------+-----------------------------------------------------+

one.secgroup.chown
--------------------------------------------------------------------------------

-  **Description**: Changes the ownership of a security group.
-  **Parameters**

+------+------------+------------------------------------------------------------------------+
| Type | Data Type  |                              Description                               |
+======+============+========================================================================+
| IN   | String     | The session string.                                                    |
+------+------------+------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                         |
+------+------------+------------------------------------------------------------------------+
| IN   | Int        | The User ID of the new owner. If set to -1, the owner is not changed.  |
+------+------------+------------------------------------------------------------------------+
| IN   | Int        | The Group ID of the new group. If set to -1, the group is not changed. |
+------+------------+------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                            |
+------+------------+------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                    |
+------+------------+------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                            |
+------+------------+------------------------------------------------------------------------+

one.secgroup.rename
--------------------------------------------------------------------------------

-  **Description**: Renames a security group.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| IN   | String     | The new name.                               |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The VM ID / The error string.               |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.secgroup.info
--------------------------------------------------------------------------------

-  **Description**: Retrieves information for the security group.
-  **Parameters**

+------+-----------+---------------------------------------------+
| Type | Data Type |                 Description                 |
+======+===========+=============================================+
| IN   | String    | The session string.                         |
+------+-----------+---------------------------------------------+
| IN   | Int       | The object ID.                              |
+------+-----------+---------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not |
+------+-----------+---------------------------------------------+
| OUT  | String    | The information string / The error string.  |
+------+-----------+---------------------------------------------+
| OUT  | Int       | Error code.                                 |
+------+-----------+---------------------------------------------+

one.secgrouppool.info
--------------------------------------------------------------------------------

-  **Description**: Retrieves information for all or part of the security groups in the pool.
-  **Parameters**

+------+-----------+-----------------------------------------------------------------------+
| Type | Data Type |                              Description                              |
+======+===========+=======================================================================+
| IN   | String    | The session string.                                                   |
+------+-----------+-----------------------------------------------------------------------+
| IN   | Int       | Filter flag                                                           |
|      |           | **- < = -3**: Connected user's resources                              |
|      |           | **- -2**: All resources                                               |
|      |           | **- -1**: Connected user's and his group's resources                  |
|      |           | **- > = 0**: UID User's Resources                                     |
+------+-----------+-----------------------------------------------------------------------+
| IN   | Int       | When the next parameter is >= -1 this is the Range start ID.          |
|      |           | Can be -1. For smaller values this is the offset used for pagination. |
+------+-----------+-----------------------------------------------------------------------+
| IN   | Int       | For values >= -1 this is the Range end ID. Can be -1 to get until the |
|      |           | last ID. For values < -1 this is the page size used for pagination.   |
+------+-----------+-----------------------------------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not                           |
+------+-----------+-----------------------------------------------------------------------+
| OUT  | String    | The information string / The error string.                            |
+------+-----------+-----------------------------------------------------------------------+
| OUT  | Int       | Error code.                                                           |
+------+-----------+-----------------------------------------------------------------------+

The range can be used to retrieve a subset of the pool, from the 'start' to the 'end' ID. To retrieve the complete pool, use ``(-1, -1)``; to retrieve all the pool from a specific ID to the last one, use ``(<id>, -1)``, and to retrieve the first elements up to an ID, use ``(0, <id>)``.

Actions for Datastore Management
================================

one.datastore.allocate
----------------------

-  **Description**: Allocates a new datastore in OpenNebula.
-  **Parameters**

+------+------------+--------------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                              Description                                               |
+======+============+========================================================================================================+
| IN   | String     | The session string.                                                                                    |
+------+------------+--------------------------------------------------------------------------------------------------------+
| IN   | String     | A string containing the template of the datastore. Syntax can be the usual ``attribute=value`` or XML. |
+------+------------+--------------------------------------------------------------------------------------------------------+
| IN   | Int        | The cluster ID. If it is -1, this datastore won't be added to any cluster.                             |
+------+------------+--------------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                            |
+------+------------+--------------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The allocated resource ID / The error string.                                                          |
+------+------------+--------------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                            |
+------+------------+--------------------------------------------------------------------------------------------------------+

one.datastore.delete
--------------------

-  **Description**: Deletes the given datastore from the pool.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.datastore.update
--------------------

-  **Description**: Replaces the datastore template contents.
-  **Parameters**

+------+------------+--------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                           Description                                            |
+======+============+==================================================================================================+
| IN   | String     | The session string.                                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | String     | The new template contents. Syntax can be the usual ``attribute=value`` or XML.                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | Update type: **0**: Replace the whole template. **1**: Merge new template with the existing one. |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+

one.datastore.chmod
-------------------

-  **Description**: Changes the permission bits of a datastore.
-  **Parameters**

+------+------------+-----------------------------------------------------+
| Type | Data Type  |                     Description                     |
+======+============+=====================================================+
| IN   | String     | The session string.                                 |
+------+------------+-----------------------------------------------------+
| IN   | Int        | The object ID.                                      |
+------+------------+-----------------------------------------------------+
| IN   | Int        | USER USE bit. If set to -1, it will not change.     |
+------+------------+-----------------------------------------------------+
| IN   | Int        | USER MANAGE bit. If set to -1, it will not change.  |
+------+------------+-----------------------------------------------------+
| IN   | Int        | USER ADMIN bit. If set to -1, it will not change.   |
+------+------------+-----------------------------------------------------+
| IN   | Int        | GROUP USE bit. If set to -1, it will not change.    |
+------+------------+-----------------------------------------------------+
| IN   | Int        | GROUP MANAGE bit. If set to -1, it will not change. |
+------+------------+-----------------------------------------------------+
| IN   | Int        | GROUP ADMIN bit. If set to -1, it will not change.  |
+------+------------+-----------------------------------------------------+
| IN   | Int        | OTHER USE bit. If set to -1, it will not change.    |
+------+------------+-----------------------------------------------------+
| IN   | Int        | OTHER MANAGE bit. If set to -1, it will not change. |
+------+------------+-----------------------------------------------------+
| IN   | Int        | OTHER ADMIN bit. If set to -1, it will not change.  |
+------+------------+-----------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not         |
+------+------------+-----------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                 |
+------+------------+-----------------------------------------------------+
| OUT  | Int        | Error code.                                         |
+------+------------+-----------------------------------------------------+

one.datastore.chown
-------------------

-  **Description**: Changes the ownership of a datastore.
-  **Parameters**

+------+------------+------------------------------------------------------------------------+
| Type | Data Type  |                              Description                               |
+======+============+========================================================================+
| IN   | String     | The session string.                                                    |
+------+------------+------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                         |
+------+------------+------------------------------------------------------------------------+
| IN   | Int        | The User ID of the new owner. If set to -1, the owner is not changed.  |
+------+------------+------------------------------------------------------------------------+
| IN   | Int        | The Group ID of the new group. If set to -1, the group is not changed. |
+------+------------+------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                            |
+------+------------+------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                    |
+------+------------+------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                            |
+------+------------+------------------------------------------------------------------------+

one.datastore.rename
--------------------

-  **Description**: Renames a datastore.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| IN   | String     | The new name.                               |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+


one.datastore.enable
--------------------------------------------------------------------------------

-  **Description**: Enables or disables a datastore.
-  **Parameters**

+------+------------+----------------------------------------------+
| Type | Data Type  |                 Description                  |
+======+============+==============================================+
| IN   | String     | The session string.                          |
+------+------------+----------------------------------------------+
| IN   | Int        | The object ID.                               |
+------+------------+----------------------------------------------+
| IN   | Boolean    | True for enabling, false for disabling.      |
+------+------------+----------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not. |
+------+------------+----------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.          |
+------+------------+----------------------------------------------+
| OUT  | Int        | Error code.                                  |
+------+------------+----------------------------------------------+


one.datastore.info
------------------

-  **Description**: Retrieves information for the datastore.
-  **Parameters**

+------+-----------+---------------------------------------------+
| Type | Data Type |                 Description                 |
+======+===========+=============================================+
| IN   | String    | The session string.                         |
+------+-----------+---------------------------------------------+
| IN   | Int       | The object ID.                              |
+------+-----------+---------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not |
+------+-----------+---------------------------------------------+
| OUT  | String    | The information string / The error string.  |
+------+-----------+---------------------------------------------+
| OUT  | Int       | Error code.                                 |
+------+-----------+---------------------------------------------+

one.datastorepool.info
----------------------

-  **Description**: Retrieves information for all or part of the datastores in the pool.
-  **Parameters**

+------+-----------+---------------------------------------------+
| Type | Data Type |                 Description                 |
+======+===========+=============================================+
| IN   | String    | The session string.                         |
+------+-----------+---------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not |
+------+-----------+---------------------------------------------+
| OUT  | String    | The information string / The error string.  |
+------+-----------+---------------------------------------------+
| OUT  | Int       | Error code.                                 |
+------+-----------+---------------------------------------------+

Actions for Image Management
============================

one.image.allocate
------------------

-  **Description**: Allocates a new image in OpenNebula.
-  **Parameters**

+------+------------+----------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                            Description                                             |
+======+============+====================================================================================================+
| IN   | String     | The session string.                                                                                |
+------+------------+----------------------------------------------------------------------------------------------------+
| IN   | String     | A string containing the template of the image. Syntax can be the usual ``attribute=value`` or XML. |
+------+------------+----------------------------------------------------------------------------------------------------+
| IN   | Int        | The datastore ID.                                                                                  |
+------+------------+----------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                        |
+------+------------+----------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The allocated resource ID / The error string.                                                      |
+------+------------+----------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                        |
+------+------------+----------------------------------------------------------------------------------------------------+

one.image.clone
---------------

-  **Description**: Clones an existing image.
-  **Parameters**

+------+------------+------------------------------------------------------------------------------------+
| Type | Data Type  |                                    Description                                     |
+======+============+====================================================================================+
| IN   | String     | The session string.                                                                |
+------+------------+------------------------------------------------------------------------------------+
| IN   | Int        | The ID of the image to be cloned.                                                  |
+------+------------+------------------------------------------------------------------------------------+
| IN   | String     | Name for the new image.                                                            |
+------+------------+------------------------------------------------------------------------------------+
| IN   | Int        | The ID of the target datastore. Optional, can be set to -1 to use the current one. |
+------+------------+------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                        |
+------+------------+------------------------------------------------------------------------------------+
| OUT  | Int/String | The new image ID / The error string.                                               |
+------+------------+------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                        |
+------+------------+------------------------------------------------------------------------------------+

one.image.delete
----------------

-  **Description**: Deletes the given image from the pool.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.image.enable
----------------

-  **Description**: Enables or disables an image.
-  **Parameters**

+------+------------+----------------------------------------------+
| Type | Data Type  |                 Description                  |
+======+============+==============================================+
| IN   | String     | The session string.                          |
+------+------------+----------------------------------------------+
| IN   | Int        | The Image ID.                                |
+------+------------+----------------------------------------------+
| IN   | Boolean    | True for enabling, false for disabling.      |
+------+------------+----------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not. |
+------+------------+----------------------------------------------+
| OUT  | Int/String | The Image ID / The error string.             |
+------+------------+----------------------------------------------+
| OUT  | Int        | Error code.                                  |
+------+------------+----------------------------------------------+

one.image.persistent
--------------------

-  **Description**: Sets the Image as persistent or not persistent.
-  **Parameters**

+------+------------+-----------------------------------------------+
| Type | Data Type  |                  Description                  |
+======+============+===============================================+
| IN   | String     | The session string.                           |
+------+------------+-----------------------------------------------+
| IN   | Int        | The Image ID.                                 |
+------+------------+-----------------------------------------------+
| IN   | Boolean    | True for persistent, false for non-persisent. |
+------+------------+-----------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not.  |
+------+------------+-----------------------------------------------+
| OUT  | Int/String | The Image ID / The error string.              |
+------+------------+-----------------------------------------------+
| OUT  | Int        | Error code.                                   |
+------+------------+-----------------------------------------------+

one.image.chtype
----------------

-  **Description**: Changes the type of an Image.
-  **Parameters**

+------+------------+-------------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                              Description                                              |
+======+============+=======================================================================================================+
| IN   | String     | The session string.                                                                                   |
+------+------------+-------------------------------------------------------------------------------------------------------+
| IN   | Int        | The Image ID.                                                                                         |
+------+------------+-------------------------------------------------------------------------------------------------------+
| IN   | String     | New type for the Image. See the existing types in the :ref:`Image template reference <img_template>`. |
+------+------------+-------------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not.                                                          |
+------+------------+-------------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The Image ID / The error string.                                                                      |
+------+------------+-------------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                           |
+------+------------+-------------------------------------------------------------------------------------------------------+

one.image.update
----------------

-  **Description**: Replaces the image template contents.
-  **Parameters**

+------+------------+--------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                           Description                                            |
+======+============+==================================================================================================+
| IN   | String     | The session string.                                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | String     | The new template contents. Syntax can be the usual ``attribute=value`` or XML.                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | Update type: **0**: Replace the whole template. **1**: Merge new template with the existing one. |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+

one.image.chmod
---------------

-  **Description**: Changes the permission bits of an image.
-  **Parameters**

+------+------------+-----------------------------------------------------+
| Type | Data Type  |                     Description                     |
+======+============+=====================================================+
| IN   | String     | The session string.                                 |
+------+------------+-----------------------------------------------------+
| IN   | Int        | The object ID.                                      |
+------+------------+-----------------------------------------------------+
| IN   | Int        | USER USE bit. If set to -1, it will not change.     |
+------+------------+-----------------------------------------------------+
| IN   | Int        | USER MANAGE bit. If set to -1, it will not change.  |
+------+------------+-----------------------------------------------------+
| IN   | Int        | USER ADMIN bit. If set to -1, it will not change.   |
+------+------------+-----------------------------------------------------+
| IN   | Int        | GROUP USE bit. If set to -1, it will not change.    |
+------+------------+-----------------------------------------------------+
| IN   | Int        | GROUP MANAGE bit. If set to -1, it will not change. |
+------+------------+-----------------------------------------------------+
| IN   | Int        | GROUP ADMIN bit. If set to -1, it will not change.  |
+------+------------+-----------------------------------------------------+
| IN   | Int        | OTHER USE bit. If set to -1, it will not change.    |
+------+------------+-----------------------------------------------------+
| IN   | Int        | OTHER MANAGE bit. If set to -1, it will not change. |
+------+------------+-----------------------------------------------------+
| IN   | Int        | OTHER ADMIN bit. If set to -1, it will not change.  |
+------+------------+-----------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not         |
+------+------------+-----------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                 |
+------+------------+-----------------------------------------------------+
| OUT  | Int        | Error code.                                         |
+------+------------+-----------------------------------------------------+

one.image.chown
---------------

-  **Description**: Changes the ownership of an image.
-  **Parameters**

+------+------------+------------------------------------------------------------------------+
| Type | Data Type  |                              Description                               |
+======+============+========================================================================+
| IN   | String     | The session string.                                                    |
+------+------------+------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                         |
+------+------------+------------------------------------------------------------------------+
| IN   | Int        | The User ID of the new owner. If set to -1, the owner is not changed.  |
+------+------------+------------------------------------------------------------------------+
| IN   | Int        | The Group ID of the new group. If set to -1, the group is not changed. |
+------+------------+------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                            |
+------+------------+------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                    |
+------+------------+------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                            |
+------+------------+------------------------------------------------------------------------+

one.image.rename
----------------

-  **Description**: Renames an image.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| IN   | String     | The new name.                               |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The VM ID / The error string.               |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.image.snapshotdelete
------------------------

-  **Description**: Deletes a snapshot from the image
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| IN   | Int        | ID of the snapshot to delete                |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | ID of the deleted snapshot/The error string.|
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.image.snapshotrevert
------------------------

-  **Description**: Reverts image state to a previous snapshot
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| IN   | Int        | ID of the snapshot to revert to             |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | ID of the snapshot/The error string.        |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.image.snapshotflatten
-------------------------

-  **Description**: Flatten the snapshot of image and discards others
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| IN   | Int        | ID of the snapshot to flatten               |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | ID of the snapshot/The error string.        |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.image.info
--------------

-  **Description**: Retrieves information for the image.
-  **Parameters**

+------+-----------+---------------------------------------------+
| Type | Data Type |                 Description                 |
+======+===========+=============================================+
| IN   | String    | The session string.                         |
+------+-----------+---------------------------------------------+
| IN   | Int       | The object ID.                              |
+------+-----------+---------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not |
+------+-----------+---------------------------------------------+
| OUT  | String    | The information string / The error string.  |
+------+-----------+---------------------------------------------+
| OUT  | Int       | Error code.                                 |
+------+-----------+---------------------------------------------+

one.imagepool.info
------------------

-  **Description**: Retrieves information for all or part of the images in the pool.
-  **Parameters**

+------+-----------+-----------------------------------------------------------------------+
| Type | Data Type |                              Description                              |
+======+===========+=======================================================================+
| IN   | String    | The session string.                                                   |
+------+-----------+-----------------------------------------------------------------------+
| IN   | Int       | Filter flag                                                           |
|      |           | **- < = -3**: Connected user's resources                              |
|      |           | **- -2**: All resources                                               |
|      |           | **- -1**: Connected user's and his group's resources                  |
|      |           | **- > = 0**: UID User's Resources                                     |
+------+-----------+-----------------------------------------------------------------------+
| IN   | Int       | When the next parameter is >= -1 this is the Range start ID.          |
|      |           | Can be -1. For smaller values this is the offset used for pagination. |
+------+-----------+-----------------------------------------------------------------------+
| IN   | Int       | For values >= -1 this is the Range end ID. Can be -1 to get until the |
|      |           | last ID. For values < -1 this is the page size used for pagination.   |
+------+-----------+-----------------------------------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not                           |
+------+-----------+-----------------------------------------------------------------------+
| OUT  | String    | The information string / The error string.                            |
+------+-----------+-----------------------------------------------------------------------+
| OUT  | Int       | Error code.                                                           |
+------+-----------+-----------------------------------------------------------------------+

The range can be used to retrieve a subset of the pool, from the 'start' to the 'end' ID. To retrieve the complete pool, use ``(-1, -1)``; to retrieve all the pool from a specific ID to the last one, use ``(<id>, -1)``, and to retrieve the first elements up to an ID, use ``(0, <id>)``.

Actions for User Management
===========================

one.user.allocate
-----------------

-  **Description**: Allocates a new user in OpenNebula
-  **Parameters**

+------+------------+-----------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                             Description                                             |
+======+============+=====================================================================================================+
| IN   | String     | The session string.                                                                                 |
+------+------------+-----------------------------------------------------------------------------------------------------+
| IN   | String     | username for the new user                                                                           |
+------+------------+-----------------------------------------------------------------------------------------------------+
| IN   | String     | password for the new user                                                                           |
+------+------------+-----------------------------------------------------------------------------------------------------+
| IN   | String     | authentication driver for the new user. If it is an empty string, then the default ('core') is used |
+------+------------+-----------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                         |
+------+------------+-----------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The allocated User ID / The error string.                                                           |
+------+------------+-----------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                         |
+------+------------+-----------------------------------------------------------------------------------------------------+

one.user.delete
---------------

-  **Description**: Deletes the given user from the pool.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.user.passwd
---------------

-  **Description**: Changes the password for the given user.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| IN   | String     | The new password                            |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The User ID / The error string.             |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.user.login
---------------

-  **Description**: Generates or sets a login token.
-  **Parameters**

+------+------------+----------------------------------------------------------------------------+
| Type | Data Type  |                 Description                                                |
+======+============+============================================================================+
| IN   | String     | The session string.                                                        |
+------+------------+----------------------------------------------------------------------------+
| IN   | String     | The user name to generate the token for                                    |
+------+------------+----------------------------------------------------------------------------+
| IN   | String     | The token, if empty oned will generate one                                 |
+------+------------+----------------------------------------------------------------------------+
| IN   | Int        | Valid period in seconds; 0 reset the token and -1 for a non-expiring token.|
+------+------------+----------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                |
+------+------------+----------------------------------------------------------------------------+
| OUT  | String     | The new token / The error string.                                          |
+------+------------+----------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                |
+------+------------+----------------------------------------------------------------------------+

one.user.update
---------------

-  **Description**: Replaces the user template contents.
-  **Parameters**

+------+------------+--------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                           Description                                            |
+======+============+==================================================================================================+
| IN   | String     | The session string.                                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | String     | The new template contents. Syntax can be the usual ``attribute=value`` or XML.                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | Update type: **0**: Replace the whole template. **1**: Merge new template with the existing one. |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+

one.user.chauth
---------------

-  **Description**: Changes the authentication driver and the password for the given user.
-  **Parameters**

+------+------------+--------------------------------------------------------------------------+
| Type | Data Type  |                               Description                                |
+======+============+==========================================================================+
| IN   | String     | The session string.                                                      |
+------+------------+--------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                           |
+------+------------+--------------------------------------------------------------------------+
| IN   | String     | The new authentication driver.                                           |
+------+------------+--------------------------------------------------------------------------+
| IN   | String     | The new password. If it is an empty string, the password is not changed. |
+------+------------+--------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                              |
+------+------------+--------------------------------------------------------------------------+
| OUT  | Int/String | The User ID / The error string.                                          |
+------+------------+--------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                              |
+------+------------+--------------------------------------------------------------------------+

one.user.quota
--------------

-  **Description**: Sets the user quota limits.
-  **Parameters**

+------+------------+--------------------------------------------------------------------------------------+
| Type | Data Type  |                                     Description                                      |
+======+============+======================================================================================+
| IN   | String     | The session string.                                                                  |
+------+------------+--------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                       |
+------+------------+--------------------------------------------------------------------------------------+
| IN   | String     | The new quota template contents. Syntax can be the usual ``attribute=value`` or XML. |
+------+------------+--------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                          |
+------+------------+--------------------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                                  |
+------+------------+--------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                          |
+------+------------+--------------------------------------------------------------------------------------+

one.user.chgrp
--------------

-  **Description**: Changes the group of the given user.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The User ID.                                |
+------+------------+---------------------------------------------+
| IN   | Int        | The Group ID of the new group.              |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The User ID / The error string.             |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.user.addgroup
-----------------

-  **Description**: Adds the User to a secondary group.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The User ID.                                |
+------+------------+---------------------------------------------+
| IN   | Int        | The Group ID of the new group.              |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The User ID / The error string.             |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.user.delgroup
-----------------

-  **Description**: Removes the User from a secondary group
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The User ID.                                |
+------+------------+---------------------------------------------+
| IN   | Int        | The Group ID.                               |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The User ID / The error string.             |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.user.info
-------------

-  **Description**: Retrieves information for the user.
-  **Parameters**

+------+-----------+---------------------------------------------------------------------------------+
| Type | Data Type |                                   Description                                   |
+======+===========+=================================================================================+
| IN   | String    | The session string.                                                             |
+------+-----------+---------------------------------------------------------------------------------+
| IN   | Int       | The object ID. If it is -1, then the connected user's own info info is returned |
+------+-----------+---------------------------------------------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not                                     |
+------+-----------+---------------------------------------------------------------------------------+
| OUT  | String    | The information string / The error string.                                      |
+------+-----------+---------------------------------------------------------------------------------+
| OUT  | Int       | Error code.                                                                     |
+------+-----------+---------------------------------------------------------------------------------+

one.userpool.info
-----------------

-  **Description**: Retrieves information for all the users in the pool.
-  **Parameters**

+------+-----------+---------------------------------------------+
| Type | Data Type |                 Description                 |
+======+===========+=============================================+
| IN   | String    | The session string.                         |
+------+-----------+---------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not |
+------+-----------+---------------------------------------------+
| OUT  | String    | The information string / The error string.  |
+------+-----------+---------------------------------------------+
| OUT  | Int       | Error code.                                 |
+------+-----------+---------------------------------------------+

one.userquota.info
------------------

-  **Description**: Returns the default user quota limits.
-  **Parameters**

+------+-----------+-------------------------------------------------+
| Type | Data Type |                   Description                   |
+======+===========+=================================================+
| IN   | String    | The session string.                             |
+------+-----------+-------------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not     |
+------+-----------+-------------------------------------------------+
| OUT  | String    | The quota template contents / The error string. |
+------+-----------+-------------------------------------------------+
| OUT  | Int       | Error code.                                     |
+------+-----------+-------------------------------------------------+

one.userquota.update
--------------------

-  **Description**: Updates the default user quota limits.
-  **Parameters**

+------+-----------+--------------------------------------------------------------------------------------+
| Type | Data Type |                                     Description                                      |
+======+===========+======================================================================================+
| IN   | String    | The session string.                                                                  |
+------+-----------+--------------------------------------------------------------------------------------+
| IN   | String    | The new quota template contents. Syntax can be the usual ``attribute=value`` or XML. |
+------+-----------+--------------------------------------------------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not                                          |
+------+-----------+--------------------------------------------------------------------------------------+
| OUT  | String    | The quota template contents / The error string.                                      |
+------+-----------+--------------------------------------------------------------------------------------+
| OUT  | Int       | Error code.                                                                          |
+------+-----------+--------------------------------------------------------------------------------------+

Actions for Group Management
============================

one.group.allocate
------------------

-  **Description**: Allocates a new group in OpenNebula.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | String     | Name for the new group.                     |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The allocated Group ID / The error string.  |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.group.delete
----------------

-  **Description**: Deletes the given group from the pool.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.group.info
--------------

-  **Description**: Retrieves information for the group.
-  **Parameters**

+------+-----------+-----------------------------------------------------------------------------------+
| Type | Data Type |                                    Description                                    |
+======+===========+===================================================================================+
| IN   | String    | The session string.                                                               |
+------+-----------+-----------------------------------------------------------------------------------+
| IN   | Int       | The object ID. If it is -1, then the connected user's group info info is returned |
+------+-----------+-----------------------------------------------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not                                       |
+------+-----------+-----------------------------------------------------------------------------------+
| OUT  | String    | The information string / The error string.                                        |
+------+-----------+-----------------------------------------------------------------------------------+
| OUT  | Int       | Error code.                                                                       |
+------+-----------+-----------------------------------------------------------------------------------+

one.group.update
------------------

-  **Description**: Replaces the group template contents.
-  **Parameters**

+------+------------+--------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                           Description                                            |
+======+============+==================================================================================================+
| IN   | String     | The session string.                                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | String     | The new template contents. Syntax can be the usual ``attribute=value`` or XML.                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | Update type: **0**: Replace the whole template. **1**: Merge new template with the existing one. |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+

one.group.addadmin
------------------

-  **Description**: Adds a User to the Group administrators set
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The group ID.                               |
+------+------------+---------------------------------------------+
| IN   | Int        | The user ID.                                |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.group.deladmin
------------------

-  **Description**: Removes a User from the Group administrators set
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The group ID.                               |
+------+------------+---------------------------------------------+
| IN   | Int        | The user ID.                                |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.group.quota
---------------

-  **Description**: Sets the group quota limits.
-  **Parameters**

+------+------------+--------------------------------------------------------------------------------------+
| Type | Data Type  |                                     Description                                      |
+======+============+======================================================================================+
| IN   | String     | The session string.                                                                  |
+------+------------+--------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                       |
+------+------------+--------------------------------------------------------------------------------------+
| IN   | String     | The new quota template contents. Syntax can be the usual ``attribute=value`` or XML. |
+------+------------+--------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                          |
+------+------------+--------------------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                                  |
+------+------------+--------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                          |
+------+------------+--------------------------------------------------------------------------------------+

one.grouppool.info
------------------

-  **Description**: Retrieves information for all the groups in the pool.
-  **Parameters**

+------+-----------+---------------------------------------------+
| Type | Data Type |                 Description                 |
+======+===========+=============================================+
| IN   | String    | The session string.                         |
+------+-----------+---------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not |
+------+-----------+---------------------------------------------+
| OUT  | String    | The information string / The error string.  |
+------+-----------+---------------------------------------------+
| OUT  | Int       | Error code.                                 |
+------+-----------+---------------------------------------------+

one.groupquota.info
-------------------

-  **Description**: Returns the default group quota limits.
-  **Parameters**

+------+-----------+-------------------------------------------------+
| Type | Data Type |                   Description                   |
+======+===========+=================================================+
| IN   | String    | The session string.                             |
+------+-----------+-------------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not     |
+------+-----------+-------------------------------------------------+
| OUT  | String    | The quota template contents / The error string. |
+------+-----------+-------------------------------------------------+
| OUT  | Int       | Error code.                                     |
+------+-----------+-------------------------------------------------+

one.groupquota.update
---------------------

-  **Description**: Updates the default group quota limits.
-  **Parameters**

+------+-----------+--------------------------------------------------------------------------------------+
| Type | Data Type |                                     Description                                      |
+======+===========+======================================================================================+
| IN   | String    | The session string.                                                                  |
+------+-----------+--------------------------------------------------------------------------------------+
| IN   | String    | The new quota template contents. Syntax can be the usual ``attribute=value`` or XML. |
+------+-----------+--------------------------------------------------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not                                          |
+------+-----------+--------------------------------------------------------------------------------------+
| OUT  | String    | The quota template contents / The error string.                                      |
+------+-----------+--------------------------------------------------------------------------------------+
| OUT  | Int       | Error code.                                                                          |
+------+-----------+--------------------------------------------------------------------------------------+


Actions for VDC Management
============================

one.vdc.allocate
--------------------------------------------------------------------------------

-  **Description**: Allocates a new VDC in OpenNebula.
-  **Parameters**

+------+------------+--------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                           Description                                            |
+======+============+==================================================================================================+
| IN   | String     | The session string.                                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | String     | A string containing the template of the VDC. Syntax can be the usual ``attribute=value`` or XML. |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | The cluster ID. If it is -1, this virtual network won't be added to any cluster.                 |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The allocated resource ID / The error string.                                                    |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+

one.vdc.delete
--------------------------------------------------------------------------------

-  **Description**: Deletes the given VDC from the pool.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vdc.update
------------------

-  **Description**: Replaces the VDC template contents.
-  **Parameters**

+------+------------+--------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                           Description                                            |
+======+============+==================================================================================================+
| IN   | String     | The session string.                                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | String     | The new template contents. Syntax can be the usual ``attribute=value`` or XML.                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | Update type: **0**: Replace the whole template. **1**: Merge new template with the existing one. |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+


one.vdc.rename
--------------------------------------------------------------------------------

-  **Description**: Renames a VDC.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| IN   | String     | The new name.                               |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The VM ID / The error string.               |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vdc.info
--------------------------------------------------------------------------------

-  **Description**: Retrieves information for the VDC.
-  **Parameters**

+------+-----------+---------------------------------------------------------------------------------+
| Type | Data Type |                                   Description                                   |
+======+===========+=================================================================================+
| IN   | String    | The session string.                                                             |
+------+-----------+---------------------------------------------------------------------------------+
| IN   | Int       | The object ID. If it is -1, then the connected user's VDC info info is returned |
+------+-----------+---------------------------------------------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not                                     |
+------+-----------+---------------------------------------------------------------------------------+
| OUT  | String    | The information string / The error string.                                      |
+------+-----------+---------------------------------------------------------------------------------+
| OUT  | Int       | Error code.                                                                     |
+------+-----------+---------------------------------------------------------------------------------+

one.vdcpool.info
--------------------------------------------------------------------------------

-  **Description**: Retrieves information for all the VDCs in the pool.
-  **Parameters**

+------+-----------+---------------------------------------------+
| Type | Data Type |                 Description                 |
+======+===========+=============================================+
| IN   | String    | The session string.                         |
+------+-----------+---------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not |
+------+-----------+---------------------------------------------+
| OUT  | String    | The information string / The error string.  |
+------+-----------+---------------------------------------------+
| OUT  | Int       | Error code.                                 |
+------+-----------+---------------------------------------------+

one.vdc.addgroup
--------------------------------------------------------------------------------

-  **Description**: Adds a group to the VDC
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The VDC ID.                                 |
+------+------------+---------------------------------------------+
| IN   | Int        | The group ID.                               |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vdc.delgroup
--------------------------------------------------------------------------------

-  **Description**: Deletes a group from the VDC
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The VDC ID.                                 |
+------+------------+---------------------------------------------+
| IN   | Int        | The group ID.                               |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vdc.addcluster
--------------------------------------------------------------------------------

-  **Description**: Adds a cluster to the VDC
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The VDC ID.                                 |
+------+------------+---------------------------------------------+
| IN   | Int        | The Zone ID.                                |
+------+------------+---------------------------------------------+
| IN   | Int        | The Cluster ID.                             |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vdc.delcluster
--------------------------------------------------------------------------------

-  **Description**: Deletes a cluster from the VDC
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The VDC ID.                                 |
+------+------------+---------------------------------------------+
| IN   | Int        | The Zone ID.                                |
+------+------------+---------------------------------------------+
| IN   | Int        | The Cluster ID.                             |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vdc.addhost
--------------------------------------------------------------------------------

-  **Description**: Adds a host to the VDC
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The VDC ID.                                 |
+------+------------+---------------------------------------------+
| IN   | Int        | The Zone ID.                                |
+------+------------+---------------------------------------------+
| IN   | Int        | The Host ID.                                |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vdc.delhost
--------------------------------------------------------------------------------

-  **Description**: Deletes a host from the VDC
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The VDC ID.                                 |
+------+------------+---------------------------------------------+
| IN   | Int        | The Zone ID.                                |
+------+------------+---------------------------------------------+
| IN   | Int        | The Host ID.                                |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vdc.adddatastore
--------------------------------------------------------------------------------

-  **Description**: Adds a datastore to the VDC
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The VDC ID.                                 |
+------+------------+---------------------------------------------+
| IN   | Int        | The Zone ID.                                |
+------+------------+---------------------------------------------+
| IN   | Int        | The Datastore ID.                           |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vdc.deldatastore
--------------------------------------------------------------------------------

-  **Description**: Deletes a datastore from the VDC
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The VDC ID.                                 |
+------+------------+---------------------------------------------+
| IN   | Int        | The Zone ID.                                |
+------+------------+---------------------------------------------+
| IN   | Int        | The Datastore ID.                           |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vdc.addvnet
--------------------------------------------------------------------------------

-  **Description**: Adds a vnet to the VDC
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The VDC ID.                                 |
+------+------------+---------------------------------------------+
| IN   | Int        | The Zone ID.                                |
+------+------------+---------------------------------------------+
| IN   | Int        | The Vnet ID.                                |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vdc.delvnet
--------------------------------------------------------------------------------

-  **Description**: Deletes a vnet from the VDC
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The VDC ID.                                 |
+------+------------+---------------------------------------------+
| IN   | Int        | The Zone ID.                                |
+------+------------+---------------------------------------------+
| IN   | Int        | The Vnet ID.                                |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

Actions for VDC Management
============================

one.vdc.allocate
--------------------------------------------------------------------------------

-  **Description**: Allocates a new VDC in OpenNebula.
-  **Parameters**

+------+------------+--------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                           Description                                            |
+======+============+==================================================================================================+
| IN   | String     | The session string.                                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | String     | A string containing the template of the VDC. Syntax can be the usual ``attribute=value`` or XML. |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The allocated resource ID / The error string.                                                    |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+

one.vdc.delete
--------------------------------------------------------------------------------

-  **Description**: Deletes the given VDC from the pool.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vdc.update
------------------

-  **Description**: Replaces the VDC template contents.
-  **Parameters**

+------+------------+--------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                           Description                                            |
+======+============+==================================================================================================+
| IN   | String     | The session string.                                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | String     | The new template contents. Syntax can be the usual ``attribute=value`` or XML.                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | Update type: **0**: Replace the whole template. **1**: Merge new template with the existing one. |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+


one.vdc.rename
--------------------------------------------------------------------------------

-  **Description**: Renames a VDC.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| IN   | String     | The new name.                               |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The VM ID / The error string.               |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vdc.info
--------------------------------------------------------------------------------

-  **Description**: Retrieves information for the VDC.
-  **Parameters**

+------+-----------+---------------------------------------------------------------------------------+
| Type | Data Type |                                   Description                                   |
+======+===========+=================================================================================+
| IN   | String    | The session string.                                                             |
+------+-----------+---------------------------------------------------------------------------------+
| IN   | Int       | The object ID. If it is -1, then the connected user's VDC info info is returned |
+------+-----------+---------------------------------------------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not                                     |
+------+-----------+---------------------------------------------------------------------------------+
| OUT  | String    | The information string / The error string.                                      |
+------+-----------+---------------------------------------------------------------------------------+
| OUT  | Int       | Error code.                                                                     |
+------+-----------+---------------------------------------------------------------------------------+

one.vdcpool.info
--------------------------------------------------------------------------------

-  **Description**: Retrieves information for all the VDCs in the pool.
-  **Parameters**

+------+-----------+---------------------------------------------+
| Type | Data Type |                 Description                 |
+======+===========+=============================================+
| IN   | String    | The session string.                         |
+------+-----------+---------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not |
+------+-----------+---------------------------------------------+
| OUT  | String    | The information string / The error string.  |
+------+-----------+---------------------------------------------+
| OUT  | Int       | Error code.                                 |
+------+-----------+---------------------------------------------+

one.vdc.addgroup
--------------------------------------------------------------------------------

-  **Description**: Adds a group to the VDC
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The VDC ID.                                 |
+------+------------+---------------------------------------------+
| IN   | Int        | The group ID.                               |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vdc.delgroup
--------------------------------------------------------------------------------

-  **Description**: Deletes a group from the VDC
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The VDC ID.                                 |
+------+------------+---------------------------------------------+
| IN   | Int        | The group ID.                               |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vdc.addcluster
--------------------------------------------------------------------------------

-  **Description**: Adds a cluster to the VDC
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The VDC ID.                                 |
+------+------------+---------------------------------------------+
| IN   | Int        | The Zone ID.                                |
+------+------------+---------------------------------------------+
| IN   | Int        | The Cluster ID.                             |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vdc.delcluster
--------------------------------------------------------------------------------

-  **Description**: Deletes a cluster from the VDC
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The VDC ID.                                 |
+------+------------+---------------------------------------------+
| IN   | Int        | The Zone ID.                                |
+------+------------+---------------------------------------------+
| IN   | Int        | The Cluster ID.                             |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vdc.addhost
--------------------------------------------------------------------------------

-  **Description**: Adds a host to the VDC
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The VDC ID.                                 |
+------+------------+---------------------------------------------+
| IN   | Int        | The Zone ID.                                |
+------+------------+---------------------------------------------+
| IN   | Int        | The Host ID.                                |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vdc.delhost
--------------------------------------------------------------------------------

-  **Description**: Deletes a host from the VDC
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The VDC ID.                                 |
+------+------------+---------------------------------------------+
| IN   | Int        | The Zone ID.                                |
+------+------------+---------------------------------------------+
| IN   | Int        | The Host ID.                                |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vdc.adddatastore
--------------------------------------------------------------------------------

-  **Description**: Adds a datastore to the VDC
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The VDC ID.                                 |
+------+------------+---------------------------------------------+
| IN   | Int        | The Zone ID.                                |
+------+------------+---------------------------------------------+
| IN   | Int        | The Datastore ID.                           |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vdc.deldatastore
--------------------------------------------------------------------------------

-  **Description**: Deletes a datastore from the VDC
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The VDC ID.                                 |
+------+------------+---------------------------------------------+
| IN   | Int        | The Zone ID.                                |
+------+------------+---------------------------------------------+
| IN   | Int        | The Datastore ID.                           |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vdc.addvnet
--------------------------------------------------------------------------------

-  **Description**: Adds a vnet to the VDC
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The VDC ID.                                 |
+------+------------+---------------------------------------------+
| IN   | Int        | The Zone ID.                                |
+------+------------+---------------------------------------------+
| IN   | Int        | The Vnet ID.                                |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.vdc.delvnet
--------------------------------------------------------------------------------

-  **Description**: Deletes a vnet from the VDC
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The VDC ID.                                 |
+------+------------+---------------------------------------------+
| IN   | Int        | The Zone ID.                                |
+------+------------+---------------------------------------------+
| IN   | Int        | The Vnet ID.                                |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+


Actions for Zone Management
============================

one.zone.allocate
------------------

-  **Description**: Allocates a new zone in OpenNebula.
-  **Parameters**

+------+------------+---------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                            Description                                            |
+======+============+===================================================================================================+
| IN   | String     | The session string.                                                                               |
+------+------------+---------------------------------------------------------------------------------------------------+
| IN   | String     | A string containing the template of the zone. Syntax can be the usual ``attribute=value`` or XML. |
+------+------------+---------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                       |
+------+------------+---------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The allocated resource ID / The error string.                                                     |
+------+------------+---------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                       |
+------+------------+---------------------------------------------------------------------------------------------------+

one.zone.delete
----------------

-  **Description**: Deletes the given zone from the pool.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.zone.update
----------------

-  **Description**: Replaces the zone template contents.
-  **Parameters**

+------+------------+--------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                           Description                                            |
+======+============+==================================================================================================+
| IN   | String     | The session string.                                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | String     | The new template contents. Syntax can be the usual ``attribute=value`` or XML.                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | Update type: **0**: Replace the whole template. **1**: Merge new template with the existing one. |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+

one.zone.rename
----------------

-  **Description**: Renames a zone.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| IN   | String     | The new name.                               |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.zone.info
--------------

-  **Description**: Retrieves information for the zone.
-  **Parameters**

+------+-----------+---------------------------------------------+
| Type | Data Type |                 Description                 |
+======+===========+=============================================+
| IN   | String    | The session string.                         |
+------+-----------+---------------------------------------------+
| IN   | Int       | The object ID.                              |
+------+-----------+---------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not |
+------+-----------+---------------------------------------------+
| OUT  | String    | The information string / The error string.  |
+------+-----------+---------------------------------------------+
| OUT  | Int       | Error code.                                 |
+------+-----------+---------------------------------------------+

one.zonepool.info
------------------

-  **Description**: Retrieves information for all the zones in the pool.
-  **Parameters**

+------+-----------+---------------------------------------------+
| Type | Data Type |                 Description                 |
+======+===========+=============================================+
| IN   | String    | The session string.                         |
+------+-----------+---------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not |
+------+-----------+---------------------------------------------+
| OUT  | String    | The information string / The error string.  |
+------+-----------+---------------------------------------------+
| OUT  | Int       | Error code.                                 |
+------+-----------+---------------------------------------------+

Actions for ACL Rules Management
================================

one.acl.addrule
---------------

-  **Description**: Adds a new ACL rule.
-  **Parameters**

+------+------------+-----------------------------------------------------------------------+
| Type | Data Type  |                              Description                              |
+======+============+=======================================================================+
| IN   | String     | The session string.                                                   |
+------+------------+-----------------------------------------------------------------------+
| IN   | String     | User component of the new rule. A string containing a hex number.     |
+------+------------+-----------------------------------------------------------------------+
| IN   | String     | Resource component of the new rule. A string containing a hex number. |
+------+------------+-----------------------------------------------------------------------+
| IN   | String     | Rights component of the new rule. A string containing a hex number.   |
+------+------------+-----------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                           |
+------+------------+-----------------------------------------------------------------------+
| OUT  | Int/String | The allocated ACL rule ID / The error string.                         |
+------+------------+-----------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                           |
+------+------------+-----------------------------------------------------------------------+

To build the hex. numbers required to create a new rule we recommend you to read the `ruby <http://dev.opennebula.org/projects/opennebula/repository/revisions/master/entry/src/oca/ruby/OpenNebula/Acl.rb>`__ or `java <http://dev.opennebula.org/projects/opennebula/repository/revisions/master/entry/src/oca/java/src/org/opennebula/client/acl/Acl.java>`__ code.

one.acl.delrule
---------------

-  **Description**: Deletes an ACL rule.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | ACL rule ID.                                |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The ACL rule ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.acl.info
------------

-  **Description**: Returns the complete ACL rule set.
-  **Parameters**

+------+-----------+---------------------------------------------+
| Type | Data Type |                 Description                 |
+======+===========+=============================================+
| IN   | String    | The session string.                         |
+------+-----------+---------------------------------------------+
| IN   | Int       | ACL rule ID.                                |
+------+-----------+---------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not |
+------+-----------+---------------------------------------------+
| OUT  | String    | The information string / The error string.  |
+------+-----------+---------------------------------------------+
| OUT  | Int       | Error code.                                 |
+------+-----------+---------------------------------------------+

Actions for Document Management
===============================

one.document.allocate
---------------------

-  **Description**: Allocates a new document in OpenNebula.
-  **Parameters**

+------+------------+---------------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                               Description                                               |
+======+============+=========================================================================================================+
| IN   | String     | The session string.                                                                                     |
+------+------------+---------------------------------------------------------------------------------------------------------+
| IN   | String     | A string containing the document template contents. Syntax can be the usual ``attribute=value`` or XML. |
+------+------------+---------------------------------------------------------------------------------------------------------+
| IN   | Int        | The document type (\*).                                                                                 |
+------+------------+---------------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                             |
+------+------------+---------------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The allocated resource ID / The error string.                                                           |
+------+------------+---------------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                             |
+------+------------+---------------------------------------------------------------------------------------------------------+

(\*) Type is an integer value used to allow dynamic pools compartmentalization.

Let's say you want to store documents representing Chef recipes, and EC2 security groups; you would allocate documents of each kind with a different type. This type is then used in the one.documentpool.info method to filter the results.

one.document.clone
------------------

-  **Description**: Clones an existing virtual machine document.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The ID of the document to be cloned.        |
+------+------------+---------------------------------------------+
| IN   | String     | Name for the new document.                  |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The new document ID / The error string.     |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.document.delete
-------------------

-  **Description**: Deletes the given document from the pool.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.         |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.document.update
-------------------

-  **Description**: Replaces the document template contents.
-  **Parameters**

+------+------------+--------------------------------------------------------------------------------------------------+
| Type | Data Type  |                                           Description                                            |
+======+============+==================================================================================================+
| IN   | String     | The session string.                                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                                                   |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | String     | The new document template contents. Syntax can be the usual ``attribute=value`` or XML.          |
+------+------------+--------------------------------------------------------------------------------------------------+
| IN   | Int        | Update type: **0**: Replace the whole template. **1**: Merge new template with the existing one. |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                                              |
+------+------------+--------------------------------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                                                      |
+------+------------+--------------------------------------------------------------------------------------------------+

one.document.chmod
------------------

-  **Description**: Changes the permission bits of a document.
-  **Parameters**

+------+------------+-----------------------------------------------------+
| Type | Data Type  |                     Description                     |
+======+============+=====================================================+
| IN   | String     | The session string.                                 |
+------+------------+-----------------------------------------------------+
| IN   | Int        | The object ID.                                      |
+------+------------+-----------------------------------------------------+
| IN   | Int        | USER USE bit. If set to -1, it will not change.     |
+------+------------+-----------------------------------------------------+
| IN   | Int        | USER MANAGE bit. If set to -1, it will not change.  |
+------+------------+-----------------------------------------------------+
| IN   | Int        | USER ADMIN bit. If set to -1, it will not change.   |
+------+------------+-----------------------------------------------------+
| IN   | Int        | GROUP USE bit. If set to -1, it will not change.    |
+------+------------+-----------------------------------------------------+
| IN   | Int        | GROUP MANAGE bit. If set to -1, it will not change. |
+------+------------+-----------------------------------------------------+
| IN   | Int        | GROUP ADMIN bit. If set to -1, it will not change.  |
+------+------------+-----------------------------------------------------+
| IN   | Int        | OTHER USE bit. If set to -1, it will not change.    |
+------+------------+-----------------------------------------------------+
| IN   | Int        | OTHER MANAGE bit. If set to -1, it will not change. |
+------+------------+-----------------------------------------------------+
| IN   | Int        | OTHER ADMIN bit. If set to -1, it will not change.  |
+------+------------+-----------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not         |
+------+------------+-----------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                 |
+------+------------+-----------------------------------------------------+
| OUT  | Int        | Error code.                                         |
+------+------------+-----------------------------------------------------+

one.document.chown
------------------

-  **Description**: Changes the ownership of a document.
-  **Parameters**

+------+------------+------------------------------------------------------------------------+
| Type | Data Type  |                              Description                               |
+======+============+========================================================================+
| IN   | String     | The session string.                                                    |
+------+------------+------------------------------------------------------------------------+
| IN   | Int        | The object ID.                                                         |
+------+------------+------------------------------------------------------------------------+
| IN   | Int        | The User ID of the new owner. If set to -1, the owner is not changed.  |
+------+------------+------------------------------------------------------------------------+
| IN   | Int        | The Group ID of the new group. If set to -1, the group is not changed. |
+------+------------+------------------------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not                            |
+------+------------+------------------------------------------------------------------------+
| OUT  | Int/String | The resource ID / The error string.                                    |
+------+------------+------------------------------------------------------------------------+
| OUT  | Int        | Error code.                                                            |
+------+------------+------------------------------------------------------------------------+

one.document.rename
-------------------

-  **Description**: Renames a document.
-  **Parameters**

+------+------------+---------------------------------------------+
| Type | Data Type  |                 Description                 |
+======+============+=============================================+
| IN   | String     | The session string.                         |
+------+------------+---------------------------------------------+
| IN   | Int        | The object ID.                              |
+------+------------+---------------------------------------------+
| IN   | String     | The new name.                               |
+------+------------+---------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not |
+------+------------+---------------------------------------------+
| OUT  | Int/String | The VM ID / The error string.               |
+------+------------+---------------------------------------------+
| OUT  | Int        | Error code.                                 |
+------+------------+---------------------------------------------+

one.document.lock
-------------------

-  **Description**: Locks the document at the api level. The lock automatically expires after 2 minutes.
-  **Parameters**

+------+----------------+----------------------------------------------------------------------------------------------+
| Type |   Data Type    |                                         Description                                          |
+======+================+==============================================================================================+
| IN   | String         | The session string.                                                                          |
+------+----------------+----------------------------------------------------------------------------------------------+
| IN   | Int            | The object ID.                                                                               |
+------+----------------+----------------------------------------------------------------------------------------------+
| IN   | String         | String to identify the application requesting the lock.                                      |
+------+----------------+----------------------------------------------------------------------------------------------+
| OUT  | Boolean        | true or false whenever is successful or not                                                  |
+------+----------------+----------------------------------------------------------------------------------------------+
| OUT  | Boolean/String | True if the lock was granted, and false if the object is already locked. / The error string. |
+------+----------------+----------------------------------------------------------------------------------------------+
| OUT  | Int            | Error code.                                                                                  |
+------+----------------+----------------------------------------------------------------------------------------------+

one.document.unlock
-------------------

-  **Description**: Unlocks the document at the api level.
-  **Parameters**

+------+------------+---------------------------------------------------------+
| Type | Data Type  |                       Description                       |
+======+============+=========================================================+
| IN   | String     | The session string.                                     |
+------+------------+---------------------------------------------------------+
| IN   | Int        | The object ID.                                          |
+------+------------+---------------------------------------------------------+
| IN   | String     | String to identify the application requesting the lock. |
+------+------------+---------------------------------------------------------+
| OUT  | Boolean    | true or false whenever is successful or not             |
+------+------------+---------------------------------------------------------+
| OUT  | Int/String | The object ID / The error string.                       |
+------+------------+---------------------------------------------------------+
| OUT  | Int        | Error code.                                             |
+------+------------+---------------------------------------------------------+

one.document.info
-----------------

-  **Description**: Retrieves information for the document.
-  **Parameters**

+------+-----------+---------------------------------------------+
| Type | Data Type |                 Description                 |
+======+===========+=============================================+
| IN   | String    | The session string.                         |
+------+-----------+---------------------------------------------+
| IN   | Int       | The object ID.                              |
+------+-----------+---------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not |
+------+-----------+---------------------------------------------+
| OUT  | String    | The information string / The error string.  |
+------+-----------+---------------------------------------------+
| OUT  | Int       | Error code.                                 |
+------+-----------+---------------------------------------------+

one.documentpool.info
---------------------

-  **Description**: Retrieves information for all or part of the Resources in the pool.
-  **Parameters**

+------+-----------+-----------------------------------------------------------------------+
| Type | Data Type |                              Description                              |
+======+===========+=======================================================================+
| IN   | String    | The session string.                                                   |
+------+-----------+-----------------------------------------------------------------------+
| IN   | Int       | Filter flag                                                           |
|      |           | **- < = -3**: Connected user's resources                              |
|      |           | **- -2**: All resources                                               |
|      |           | **- -1**: Connected user's and his group's resources                  |
|      |           | **- > = 0**: UID User's Resources                                     |
+------+-----------+-----------------------------------------------------------------------+
| IN   | Int       | When the next parameter is >= -1 this is the Range start ID.          |
|      |           | Can be -1. For smaller values this is the offset used for pagination. |
+------+-----------+-----------------------------------------------------------------------+
| IN   | Int       | For values >= -1 this is the Range end ID. Can be -1 to get until the |
|      |           | last ID. For values < -1 this is the page size used for pagination.   |
+------+-----------+-----------------------------------------------------------------------+
| IN   | Int       | The document type.                                                    |
+------+-----------+-----------------------------------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not                           |
+------+-----------+-----------------------------------------------------------------------+
| OUT  | String    | The information string / The error string.                            |
+------+-----------+-----------------------------------------------------------------------+
| OUT  | Int       | Error code.                                                           |
+------+-----------+-----------------------------------------------------------------------+

The range can be used to retrieve a subset of the pool, from the 'start' to the 'end' ID. To retrieve the complete pool, use ``(-1, -1)``; to retrieve all the pool from a specific ID to the last one, use ``(<id>, -1)``, and to retrieve the first elements up to an ID, use ``(0, <id>)``.

System Methods
==============

one.system.version
------------------

-  **Description**: Returns the OpenNebula core version
-  **Parameters**

+------+-----------+---------------------------------------------+
| Type | Data Type |                 Description                 |
+======+===========+=============================================+
| IN   | String    | The session string.                         |
+------+-----------+---------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not |
+------+-----------+---------------------------------------------+
| OUT  | String    | The OpenNebula version, e.g. ``4.4.0``      |
+------+-----------+---------------------------------------------+
| OUT  | Int       | Error code.                                 |
+------+-----------+---------------------------------------------+

one.system.config
-----------------

-  **Description**: Returns the OpenNebula configuration
-  **Parameters**

+------+-----------+---------------------------------------------+
| Type | Data Type |                 Description                 |
+======+===========+=============================================+
| IN   | String    | The session string.                         |
+------+-----------+---------------------------------------------+
| OUT  | Boolean   | true or false whenever is successful or not |
+------+-----------+---------------------------------------------+
| OUT  | String    | The loaded oned.conf file, in XML form      |
+------+-----------+---------------------------------------------+
| OUT  | Int       | Error code.                                 |
+------+-----------+---------------------------------------------+

.. _api_xsd_reference:

XSD Reference
=============

The XML schemas describe the XML returned by the one.\*.info methods

Schemas for Cluster
-------------------

.. code:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" elementFormDefault="qualified"
      targetNamespace="http://opennebula.org/XMLSchema" xmlns="http://opennebula.org/XMLSchema">
      <xs:element name="CLUSTER">
        <xs:complexType>
          <xs:sequence>
            <xs:element name="ID" type="xs:integer"/>
            <xs:element name="NAME" type="xs:string"/>
            <xs:element name="HOSTS">
                  <xs:complexType>
                    <xs:sequence>
                      <xs:element name="ID" type="xs:integer" minOccurs="0" maxOccurs="unbounded"/>
                    </xs:sequence>
                  </xs:complexType>
            </xs:element>
            <xs:element name="DATASTORES">
                  <xs:complexType>
                    <xs:sequence>
                      <xs:element name="ID" type="xs:integer" minOccurs="0" maxOccurs="unbounded"/>
                    </xs:sequence>
                  </xs:complexType>
            </xs:element>
            <xs:element name="VNETS">
                  <xs:complexType>
                    <xs:sequence>
                      <xs:element name="ID" type="xs:integer" minOccurs="0" maxOccurs="unbounded"/>
                    </xs:sequence>
                  </xs:complexType>
            </xs:element>
            <xs:element name="TEMPLATE" type="xs:anyType"/>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
    </xs:schema>

.. code:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" elementFormDefault="qualified"
      targetNamespace="http://opennebula.org/XMLSchema" xmlns="http://opennebula.org/XMLSchema">
      <xs:include schemaLocation="cluster.xsd"/>
      <xs:element name="CLUSTER_POOL">
        <xs:complexType>
          <xs:sequence maxOccurs="1" minOccurs="1">
            <xs:element ref="CLUSTER" maxOccurs="unbounded" minOccurs="0"/>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
    </xs:schema>

Schemas for Datastore
---------------------

.. code:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://opennebula.org/XMLSchema" elementFormDefault="qualified" targetNamespace="http://opennebula.org/XMLSchema">
      <xs:element name="DATASTORE">
        <xs:complexType>
          <xs:sequence>
            <xs:element name="ID" type="xs:integer"/>
            <xs:element name="UID" type="xs:integer"/>
            <xs:element name="GID" type="xs:integer"/>
            <xs:element name="UNAME" type="xs:string"/>
            <xs:element name="GNAME" type="xs:string"/>
            <xs:element name="NAME" type="xs:string"/>
            <xs:element name="PERMISSIONS" minOccurs="0" maxOccurs="1">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="OWNER_U" type="xs:integer"/>
                  <xs:element name="OWNER_M" type="xs:integer"/>
                  <xs:element name="OWNER_A" type="xs:integer"/>
                  <xs:element name="GROUP_U" type="xs:integer"/>
                  <xs:element name="GROUP_M" type="xs:integer"/>
                  <xs:element name="GROUP_A" type="xs:integer"/>
                  <xs:element name="OTHER_U" type="xs:integer"/>
                  <xs:element name="OTHER_M" type="xs:integer"/>
                  <xs:element name="OTHER_A" type="xs:integer"/>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
            <xs:element name="DS_MAD" type="xs:string"/>
            <xs:element name="TM_MAD" type="xs:string"/>
            <xs:element name="BASE_PATH" type="xs:string"/>
            <xs:element name="TYPE" type="xs:integer"/>
            <xs:element name="DISK_TYPE" type="xs:integer"/>
            <xs:element name="STATE" type="xs:integer"/>
            <xs:element name="CLUSTER_ID" type="xs:integer"/>
            <xs:element name="CLUSTER" type="xs:string"/>
            <xs:element name="TOTAL_MB" type="xs:integer"/>
            <xs:element name="FREE_MB" type="xs:integer"/>
            <xs:element name="USED_MB" type="xs:integer"/>
            <xs:element name="IMAGES">
                  <xs:complexType>
                    <xs:sequence>
                      <xs:element name="ID" type="xs:integer" minOccurs="0" maxOccurs="unbounded"/>
                    </xs:sequence>
                  </xs:complexType>
            </xs:element>
            <xs:element name="TEMPLATE" type="xs:anyType"/>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
    </xs:schema>

.. code:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" elementFormDefault="qualified"
      targetNamespace="http://opennebula.org/XMLSchema" xmlns="http://opennebula.org/XMLSchema">
      <xs:include schemaLocation="datastore.xsd"/>
      <xs:element name="DATASTORE_POOL">
        <xs:complexType>
          <xs:sequence maxOccurs="1" minOccurs="1">
            <xs:element ref="DATASTORE" maxOccurs="unbounded" minOccurs="0"/>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
    </xs:schema>

Schemas for Group
-----------------

.. code:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" elementFormDefault="qualified"
      targetNamespace="http://opennebula.org/XMLSchema" xmlns="http://opennebula.org/XMLSchema">
      <xs:element name="GROUP">
        <xs:complexType>
          <xs:sequence>
            <xs:element name="ID" type="xs:integer"/>
            <xs:element name="NAME" type="xs:string"/>
            <xs:element name="TEMPLATE" type="xs:anyType"/>
            <xs:element name="USERS">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="ID" type="xs:integer" minOccurs="0" maxOccurs="unbounded"/>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
            <xs:element name="ADMINS">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="ID" type="xs:integer" minOccurs="0" maxOccurs="unbounded"/>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
            <xs:element name="DATASTORE_QUOTA" minOccurs="0" maxOccurs="1">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="DATASTORE" minOccurs="0" maxOccurs="unbounded">
                  <xs:complexType>
                    <xs:sequence>
                      <xs:element name="ID" type="xs:string"/>
                      <xs:element name="IMAGES" type="xs:string"/>
                      <xs:element name="IMAGES_USED" type="xs:string"/>
                      <xs:element name="SIZE" type="xs:string"/>
                      <xs:element name="SIZE_USED" type="xs:string"/>
                    </xs:sequence>
                  </xs:complexType>
                  </xs:element>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
            <xs:element name="NETWORK_QUOTA" minOccurs="0" maxOccurs="1">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="NETWORK" minOccurs="0" maxOccurs="unbounded">
                  <xs:complexType>
                    <xs:sequence>
                      <xs:element name="ID" type="xs:string"/>
                      <xs:element name="LEASES" type="xs:string"/>
                      <xs:element name="LEASES_USED" type="xs:string"/>
                    </xs:sequence>
                  </xs:complexType>
                  </xs:element>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
            <xs:element name="VM_QUOTA" minOccurs="0" maxOccurs="1">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="VM" minOccurs="0" maxOccurs="1">
                    <xs:complexType>
                      <xs:sequence>
                        <xs:element name="CPU" type="xs:string"/>
                        <xs:element name="CPU_USED" type="xs:string"/>
                        <xs:element name="MEMORY" type="xs:string"/>
                        <xs:element name="MEMORY_USED" type="xs:string"/>
                        <xs:element name="VMS" type="xs:string"/>
                        <xs:element name="VMS_USED" type="xs:string"/>
                        <xs:element name="SYSTEM_DISK_SIZE" type="xs:string"/>
                        <xs:element name="SYSTEM_DISK_SIZE_USED" type="xs:string"/>
                      </xs:sequence>
                    </xs:complexType>
                  </xs:element>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
            <xs:element name="IMAGE_QUOTA" minOccurs="0" maxOccurs="1">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="IMAGE" minOccurs="0" maxOccurs="unbounded">
                    <xs:complexType>
                      <xs:sequence>
                        <xs:element name="ID" type="xs:string"/>
                        <xs:element name="RVMS" type="xs:string"/>
                        <xs:element name="RVMS_USED" type="xs:string"/>
                      </xs:sequence>
                    </xs:complexType>
                  </xs:element>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
            <xs:element name="DEFAULT_GROUP_QUOTAS">
              <xs:complexType>
                <xs:sequence>
                    <xs:element name="DATASTORE_QUOTA" minOccurs="0" maxOccurs="1">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="DATASTORE" minOccurs="0" maxOccurs="unbounded">
                          <xs:complexType>
                            <xs:sequence>
                              <xs:element name="ID" type="xs:string"/>
                              <xs:element name="IMAGES" type="xs:string"/>
                              <xs:element name="IMAGES_USED" type="xs:string"/>
                              <xs:element name="SIZE" type="xs:string"/>
                              <xs:element name="SIZE_USED" type="xs:string"/>
                            </xs:sequence>
                          </xs:complexType>
                          </xs:element>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                    <xs:element name="NETWORK_QUOTA" minOccurs="0" maxOccurs="1">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="NETWORK" minOccurs="0" maxOccurs="unbounded">
                          <xs:complexType>
                            <xs:sequence>
                              <xs:element name="ID" type="xs:string"/>
                              <xs:element name="LEASES" type="xs:string"/>
                              <xs:element name="LEASES_USED" type="xs:string"/>
                            </xs:sequence>
                          </xs:complexType>
                          </xs:element>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                    <xs:element name="VM_QUOTA" minOccurs="0" maxOccurs="1">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="VM" minOccurs="0" maxOccurs="1">
                            <xs:complexType>
                              <xs:sequence>
                                <xs:element name="CPU" type="xs:string"/>
                                <xs:element name="CPU_USED" type="xs:string"/>
                                <xs:element name="MEMORY" type="xs:string"/>
                                <xs:element name="MEMORY_USED" type="xs:string"/>
                                <xs:element name="VMS" type="xs:string"/>
                                <xs:element name="VMS_USED" type="xs:string"/>
                              </xs:sequence>
                            </xs:complexType>
                          </xs:element>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                    <xs:element name="IMAGE_QUOTA" minOccurs="0" maxOccurs="1">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="IMAGE" minOccurs="0" maxOccurs="unbounded">
                            <xs:complexType>
                              <xs:sequence>
                                <xs:element name="ID" type="xs:string"/>
                                <xs:element name="RVMS" type="xs:string"/>
                                <xs:element name="RVMS_USED" type="xs:string"/>
                              </xs:sequence>
                            </xs:complexType>
                          </xs:element>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
    </xs:schema>

.. code:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" elementFormDefault="qualified"
      targetNamespace="http://opennebula.org/XMLSchema" xmlns="http://opennebula.org/XMLSchema">
      <xs:element name="GROUP_POOL">
        <xs:complexType>
          <xs:sequence maxOccurs="1" minOccurs="1">
            <xs:choice maxOccurs="unbounded" minOccurs="0">
              <xs:element name="GROUP" maxOccurs="unbounded" minOccurs="0">
                <xs:complexType>
                  <xs:sequence>
                    <xs:element name="ID" type="xs:integer"/>
                    <xs:element name="NAME" type="xs:string"/>
                    <xs:element name="TEMPLATE" type="xs:anyType"/>
                    <xs:element name="USERS">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="ID" type="xs:integer" minOccurs="0" maxOccurs="unbounded"/>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                    <xs:element name="ADMINS">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="ID" type="xs:integer" minOccurs="0" maxOccurs="unbounded"/>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                  </xs:sequence>
                </xs:complexType>
              </xs:element>
              <xs:element name="QUOTAS" maxOccurs="unbounded" minOccurs="0">
                <xs:complexType>
                  <xs:sequence>
                    <xs:element name="ID" type="xs:integer"/>
                    <xs:element name="DATASTORE_QUOTA" minOccurs="0" maxOccurs="1">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="DATASTORE" minOccurs="0" maxOccurs="unbounded">
                          <xs:complexType>
                            <xs:sequence>
                              <xs:element name="ID" type="xs:string"/>
                              <xs:element name="IMAGES" type="xs:string"/>
                              <xs:element name="IMAGES_USED" type="xs:string"/>
                              <xs:element name="SIZE" type="xs:string"/>
                              <xs:element name="SIZE_USED" type="xs:string"/>
                            </xs:sequence>
                          </xs:complexType>
                          </xs:element>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                    <xs:element name="NETWORK_QUOTA" minOccurs="0" maxOccurs="1">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="NETWORK" minOccurs="0" maxOccurs="unbounded">
                          <xs:complexType>
                            <xs:sequence>
                              <xs:element name="ID" type="xs:string"/>
                              <xs:element name="LEASES" type="xs:string"/>
                              <xs:element name="LEASES_USED" type="xs:string"/>
                            </xs:sequence>
                          </xs:complexType>
                          </xs:element>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                    <xs:element name="VM_QUOTA" minOccurs="0" maxOccurs="1">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="VM" minOccurs="0" maxOccurs="1">
                            <xs:complexType>
                              <xs:sequence>
                                <xs:element name="CPU" type="xs:string"/>
                                <xs:element name="CPU_USED" type="xs:string"/>
                                <xs:element name="MEMORY" type="xs:string"/>
                                <xs:element name="MEMORY_USED" type="xs:string"/>
                                <xs:element name="VMS" type="xs:string"/>
                                <xs:element name="VMS_USED" type="xs:string"/>
                                <xs:element name="SYSTEM_DISK_SIZE" type="xs:string"/>
                                <xs:element name="SYSTEM_DISK_SIZE_USED" type="xs:string"/>
                              </xs:sequence>
                            </xs:complexType>
                          </xs:element>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                    <xs:element name="IMAGE_QUOTA" minOccurs="0" maxOccurs="1">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="IMAGE" minOccurs="0" maxOccurs="unbounded">
                            <xs:complexType>
                              <xs:sequence>
                                <xs:element name="ID" type="xs:string"/>
                                <xs:element name="RVMS" type="xs:string"/>
                                <xs:element name="RVMS_USED" type="xs:string"/>
                              </xs:sequence>
                            </xs:complexType>
                          </xs:element>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                  </xs:sequence>
                </xs:complexType>
              </xs:element>
            </xs:choice>
            <xs:element name="DEFAULT_GROUP_QUOTAS" minOccurs="1" maxOccurs="1">
              <xs:complexType>
                <xs:sequence>
                    <xs:element name="DATASTORE_QUOTA" minOccurs="0" maxOccurs="1">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="DATASTORE" minOccurs="0" maxOccurs="unbounded">
                          <xs:complexType>
                            <xs:sequence>
                              <xs:element name="ID" type="xs:string"/>
                              <xs:element name="IMAGES" type="xs:string"/>
                              <xs:element name="IMAGES_USED" type="xs:string"/>
                              <xs:element name="SIZE" type="xs:string"/>
                              <xs:element name="SIZE_USED" type="xs:string"/>
                            </xs:sequence>
                          </xs:complexType>
                          </xs:element>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                    <xs:element name="NETWORK_QUOTA" minOccurs="0" maxOccurs="1">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="NETWORK" minOccurs="0" maxOccurs="unbounded">
                          <xs:complexType>
                            <xs:sequence>
                              <xs:element name="ID" type="xs:string"/>
                              <xs:element name="LEASES" type="xs:string"/>
                              <xs:element name="LEASES_USED" type="xs:string"/>
                            </xs:sequence>
                          </xs:complexType>
                          </xs:element>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                    <xs:element name="VM_QUOTA" minOccurs="0" maxOccurs="1">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="VM" minOccurs="0" maxOccurs="1">
                            <xs:complexType>
                              <xs:sequence>
                                <xs:element name="CPU" type="xs:string"/>
                                <xs:element name="CPU_USED" type="xs:string"/>
                                <xs:element name="MEMORY" type="xs:string"/>
                                <xs:element name="MEMORY_USED" type="xs:string"/>
                                <xs:element name="VMS" type="xs:string"/>
                                <xs:element name="VMS_USED" type="xs:string"/>
                              </xs:sequence>
                            </xs:complexType>
                          </xs:element>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                    <xs:element name="IMAGE_QUOTA" minOccurs="0" maxOccurs="1">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="IMAGE" minOccurs="0" maxOccurs="unbounded">
                            <xs:complexType>
                              <xs:sequence>
                                <xs:element name="ID" type="xs:string"/>
                                <xs:element name="RVMS" type="xs:string"/>
                                <xs:element name="RVMS_USED" type="xs:string"/>
                              </xs:sequence>
                            </xs:complexType>
                          </xs:element>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
    </xs:schema>


Schemas for VDC
-----------------

.. code:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" elementFormDefault="qualified"
      targetNamespace="http://opennebula.org/XMLSchema" xmlns="http://opennebula.org/XMLSchema">
      <xs:element name="VDC">
        <xs:complexType>
          <xs:sequence>
            <xs:element name="ID" type="xs:integer"/>
            <xs:element name="NAME" type="xs:string"/>
            <xs:element name="GROUPS">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="ID" type="xs:integer" minOccurs="0" maxOccurs="unbounded"/>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
            <xs:element name="CLUSTERS">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="CLUSTER" minOccurs="0" maxOccurs="unbounded">
                    <xs:complexType>
                      <xs:sequence>
                        <xs:element name="ZONE_ID" type="xs:integer"/>
                        <xs:element name="CLUSTER_ID" type="xs:integer"/>
                      </xs:sequence>
                    </xs:complexType>
                  </xs:element>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
            <xs:element name="HOSTS">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="HOST" minOccurs="0" maxOccurs="unbounded">
                    <xs:complexType>
                      <xs:sequence>
                        <xs:element name="ZONE_ID" type="xs:integer"/>
                        <xs:element name="HOST_ID" type="xs:integer"/>
                      </xs:sequence>
                    </xs:complexType>
                  </xs:element>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
            <xs:element name="DATASTORES">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="DATASTORE" minOccurs="0" maxOccurs="unbounded">
                    <xs:complexType>
                      <xs:sequence>
                        <xs:element name="ZONE_ID" type="xs:integer"/>
                        <xs:element name="DATASTORE_ID" type="xs:integer"/>
                      </xs:sequence>
                    </xs:complexType>
                  </xs:element>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
            <xs:element name="VNETS">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="VNET" minOccurs="0" maxOccurs="unbounded">
                    <xs:complexType>
                      <xs:sequence>
                        <xs:element name="ZONE_ID" type="xs:integer"/>
                        <xs:element name="VNET_ID" type="xs:integer"/>
                      </xs:sequence>
                    </xs:complexType>
                  </xs:element>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
            <xs:element name="TEMPLATE" type="xs:anyType"/>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
    </xs:schema>

.. code:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" elementFormDefault="qualified"
      targetNamespace="http://opennebula.org/XMLSchema" xmlns="http://opennebula.org/XMLSchema">
      <xs:include schemaLocation="vdc.xsd"/>
      <xs:element name="VDC_POOL">
        <xs:complexType>
          <xs:sequence maxOccurs="1" minOccurs="1">
            <xs:element ref="VDC" maxOccurs="unbounded" minOccurs="0"/>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
    </xs:schema>

Schemas for Host
----------------

.. code:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://opennebula.org/XMLSchema" elementFormDefault="qualified" targetNamespace="http://opennebula.org/XMLSchema">
      <xs:element name="HOST">
        <xs:complexType>
          <xs:sequence>
            <xs:element name="ID" type="xs:integer"/>
            <xs:element name="NAME" type="xs:string"/>
            <!-- STATE values

              INIT                 = 0  Initial state for enabled hosts
              MONITORING_MONITORED = 1  Monitoring the host (from monitored)
              MONITORED            = 2  The host has been successfully monitored
              ERROR                = 3  An error ocurrer while monitoring the host
              DISABLED             = 4  The host is disabled won't be monitored
              MONITORING_ERROR     = 5  Monitoring the host (from error)
              MONITORING_INIT      = 6  Monitoring the host (from init)
              MONITORING_DISABLED  = 7  Monitoring the host (from disabled)
            -->
            <xs:element name="STATE" type="xs:integer"/>
            <xs:element name="IM_MAD" type="xs:string"/>
            <xs:element name="VM_MAD" type="xs:string"/>
            <xs:element name="VN_MAD" type="xs:string"/>
            <xs:element name="LAST_MON_TIME" type="xs:integer"/>
            <xs:element name="CLUSTER_ID" type="xs:integer"/>
            <xs:element name="CLUSTER" type="xs:string"/>
            <xs:element name="HOST_SHARE">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="DISK_USAGE" type="xs:integer"/>
                  <xs:element name="MEM_USAGE" type="xs:integer"/>
                  <!-- ^^ KB, Usage of MEMORY calculated by ONE as the summatory MEMORY requested by all VMs running in the host   -->
                  <xs:element name="CPU_USAGE" type="xs:integer"/>
                  <!-- ^^ Percentage, Usage of CPU calculated by ONE as the summatory CPU requested by all VMs running in the host   -->
                  <xs:element name="MAX_DISK" type="xs:integer"/>
                  <xs:element name="MAX_MEM" type="xs:integer"/>
                  <!-- ^^ KB, Total memory in the host   -->
                  <xs:element name="MAX_CPU" type="xs:integer"/>
                  <!-- ^^ Percentage, Total CPU in the host (# cores * 100)  -->
                  <xs:element name="FREE_DISK" type="xs:integer"/>
                  <xs:element name="FREE_MEM" type="xs:integer"/>
                  <!-- ^^ KB, Free MEMORY returned by the probes   -->
                  <xs:element name="FREE_CPU" type="xs:integer"/>
                  <!-- ^^ Percentage, Free CPU as returned by the probes   -->
                  <xs:element name="USED_DISK" type="xs:integer"/>
                  <xs:element name="USED_MEM" type="xs:integer"/>
                  <!-- ^^ KB, Memory used by all host processes (including VMs) over a total of MAX_MEM   -->
                  <xs:element name="USED_CPU" type="xs:integer"/>
                  <!-- ^^ Percentage of CPU used by all host processes (including VMs) over a total of # cores * 100   -->
                  <xs:element name="RUNNING_VMS" type="xs:integer"/>
                  <xs:element name="DATASTORES">
                    <xs:complexType>
                      <xs:sequence>
                        <xs:element name="DS" minOccurs="0" maxOccurs="unbounded">
                          <xs:complexType>
                            <xs:all>
                              <xs:element name="ID" type="xs:integer"/>
                              <xs:element name="FREE_MB" type="xs:integer"/>
                              <xs:element name="TOTAL_MB" type="xs:integer"/>
                              <xs:element name="USED_MB" type="xs:integer"/>
                            </xs:all>
                          </xs:complexType>
                        </xs:element>
                      </xs:sequence>
                    </xs:complexType>
                  </xs:element>
                  <xs:element name="PCI_DEVICES">
                    <xs:complexType>
                      <xs:sequence>
                        <xs:element name="PCI" type="xs:anyType" minOccurs="0" maxOccurs="unbounded"/>
                      </xs:sequence>
                    </xs:complexType>
                  </xs:element>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
            <xs:element name="VMS">
                  <xs:complexType>
                    <xs:sequence>
                      <xs:element name="ID" type="xs:integer" minOccurs="0" maxOccurs="unbounded"/>
                    </xs:sequence>
                  </xs:complexType>
            </xs:element>
            <xs:element name="TEMPLATE" type="xs:anyType"/>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
    </xs:schema>

.. code:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" elementFormDefault="qualified"
      targetNamespace="http://opennebula.org/XMLSchema" xmlns="http://opennebula.org/XMLSchema">
      <xs:include schemaLocation="host.xsd"/>
      <xs:element name="HOST_POOL">
        <xs:complexType>
          <xs:sequence maxOccurs="1" minOccurs="1">
            <xs:element ref="HOST" maxOccurs="unbounded" minOccurs="0"/>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
    </xs:schema>

Schemas for Image
-----------------

.. code:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://opennebula.org/XMLSchema" elementFormDefault="qualified" targetNamespace="http://opennebula.org/XMLSchema">
      <xs:element name="IMAGE">
        <xs:complexType>
          <xs:sequence>
            <xs:element name="ID" type="xs:integer"/>
            <xs:element name="UID" type="xs:integer"/>
            <xs:element name="GID" type="xs:integer"/>
            <xs:element name="UNAME" type="xs:string"/>
            <xs:element name="GNAME" type="xs:string"/>
            <xs:element name="NAME" type="xs:string"/>
            <xs:element name="PERMISSIONS" minOccurs="0" maxOccurs="1">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="OWNER_U" type="xs:integer"/>
                  <xs:element name="OWNER_M" type="xs:integer"/>
                  <xs:element name="OWNER_A" type="xs:integer"/>
                  <xs:element name="GROUP_U" type="xs:integer"/>
                  <xs:element name="GROUP_M" type="xs:integer"/>
                  <xs:element name="GROUP_A" type="xs:integer"/>
                  <xs:element name="OTHER_U" type="xs:integer"/>
                  <xs:element name="OTHER_M" type="xs:integer"/>
                  <xs:element name="OTHER_A" type="xs:integer"/>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
            <xs:element name="TYPE" type="xs:integer"/>
            <xs:element name="DISK_TYPE" type="xs:integer"/>
            <xs:element name="PERSISTENT" type="xs:integer"/>
            <xs:element name="REGTIME" type="xs:integer"/>
            <xs:element name="SOURCE" type="xs:string"/>
            <xs:element name="PATH" type="xs:string"/>
            <xs:element name="FSTYPE" type="xs:string"/>
            <xs:element name="SIZE" type="xs:integer"/>

            <!-- STATE values,
              INIT      = 0, Initialization state
              READY     = 1, Image ready to use
              USED      = 2, Image in use
              DISABLED  = 3, Image can not be instantiated by a VM
              LOCKED    = 4, FS operation for the Image in process
              ERROR     = 5, Error state the operation FAILED
              CLONE     = 6, Image is being cloned
              DELETE    = 7, DS is deleting the image
              USED_PERS = 8, Image is in use and persistent
            -->
            <xs:element name="STATE" type="xs:integer"/>
            <xs:element name="RUNNING_VMS" type="xs:integer"/>
            <xs:element name="CLONING_OPS" type="xs:integer"/>
            <xs:element name="CLONING_ID" type="xs:integer"/>
            <xs:element name="TARGET_SNAPSHOT" type="xs:integer"/>
            <xs:element name="DATASTORE_ID" type="xs:integer"/>
            <xs:element name="DATASTORE" type="xs:string"/>
            <xs:element name="VMS">
                  <xs:complexType>
                    <xs:sequence>
                      <xs:element name="ID" type="xs:integer" minOccurs="0" maxOccurs="unbounded"/>
                    </xs:sequence>
                  </xs:complexType>
            </xs:element>
            <xs:element name="CLONES">
                  <xs:complexType>
                    <xs:sequence>
                      <xs:element name="ID" type="xs:integer" minOccurs="0" maxOccurs="unbounded"/>
                    </xs:sequence>
                  </xs:complexType>
            </xs:element>
            <xs:element name="TEMPLATE" type="xs:anyType"/>
            <xs:element name="SNAPSHOTS">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="SNAPSHOT" minOccurs="0" maxOccurs="unbounded">
                    <xs:complexType>
                      <xs:sequence>
                        <xs:element name="CHILDREN" type="xs:string" minOccurs="0" maxOccurs="1"/>
                        <xs:element name="ACTIVE" type="xs:string" minOccurs="0" maxOccurs="1"/>
                        <xs:element name="DATE" type="xs:integer"/>
                        <xs:element name="ID" type="xs:integer"/>
                        <xs:element name="NAME" type="xs:string" minOccurs="0" maxOccurs="1"/>
                        <xs:element name="PARENT" type="xs:integer"/>
                        <xs:element name="SIZE" type="xs:integer"/>
                      </xs:sequence>
                    </xs:complexType>
                  </xs:element>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
    </xs:schema>

.. code:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" elementFormDefault="qualified"
      targetNamespace="http://opennebula.org/XMLSchema" xmlns="http://opennebula.org/XMLSchema">
      <xs:include schemaLocation="image.xsd"/>
      <xs:element name="IMAGE_POOL">
        <xs:complexType>
          <xs:sequence maxOccurs="1" minOccurs="1">
            <xs:element ref="IMAGE" maxOccurs="unbounded" minOccurs="0"/>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
    </xs:schema>

Schemas for User
----------------

.. code:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" elementFormDefault="qualified"
      targetNamespace="http://opennebula.org/XMLSchema" xmlns="http://opennebula.org/XMLSchema">
      <xs:element name="USER">
        <xs:complexType>
          <xs:sequence>
            <xs:element name="ID" type="xs:integer"/>
            <xs:element name="GID" type="xs:integer"/>
            <xs:element name="GROUPS">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="ID" type="xs:integer" minOccurs="1" maxOccurs="unbounded"/>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
            <xs:element name="GNAME" type="xs:string"/>
            <xs:element name="NAME" type="xs:string"/>
            <xs:element name="PASSWORD" type="xs:string"/>
            <xs:element name="AUTH_DRIVER" type="xs:string"/>
            <xs:element name="ENABLED" type="xs:integer"/>
            <xs:element name="LOGIN_TOKEN" type="xs:string"/>
            <xs:element name="TEMPLATE" type="xs:anyType"/>
            <xs:element name="DATASTORE_QUOTA" minOccurs="0" maxOccurs="1">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="DATASTORE" minOccurs="0" maxOccurs="unbounded">
                  <xs:complexType>
                    <xs:sequence>
                      <xs:element name="ID" type="xs:string"/>
                      <xs:element name="IMAGES" type="xs:string"/>
                      <xs:element name="IMAGES_USED" type="xs:string"/>
                      <xs:element name="SIZE" type="xs:string"/>
                      <xs:element name="SIZE_USED" type="xs:string"/>
                    </xs:sequence>
                  </xs:complexType>
                  </xs:element>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
            <xs:element name="NETWORK_QUOTA" minOccurs="0" maxOccurs="1">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="NETWORK" minOccurs="0" maxOccurs="unbounded">
                  <xs:complexType>
                    <xs:sequence>
                      <xs:element name="ID" type="xs:string"/>
                      <xs:element name="LEASES" type="xs:string"/>
                      <xs:element name="LEASES_USED" type="xs:string"/>
                    </xs:sequence>
                  </xs:complexType>
                  </xs:element>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
            <xs:element name="VM_QUOTA" minOccurs="0" maxOccurs="1">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="VM" minOccurs="0" maxOccurs="1">
                    <xs:complexType>
                      <xs:sequence>
                        <xs:element name="CPU" type="xs:string"/>
                        <xs:element name="CPU_USED" type="xs:string"/>
                        <xs:element name="MEMORY" type="xs:string"/>
                        <xs:element name="MEMORY_USED" type="xs:string"/>
                        <xs:element name="VMS" type="xs:string"/>
                        <xs:element name="VMS_USED" type="xs:string"/>
                        <xs:element name="SYSTEM_DISK_SIZE" type="xs:string"/>
                        <xs:element name="SYSTEM_DISK_SIZE_USED" type="xs:string"/>
                      </xs:sequence>
                    </xs:complexType>
                  </xs:element>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
            <xs:element name="IMAGE_QUOTA" minOccurs="0" maxOccurs="1">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="IMAGE" minOccurs="0" maxOccurs="unbounded">
                    <xs:complexType>
                      <xs:sequence>
                        <xs:element name="ID" type="xs:string"/>
                        <xs:element name="RVMS" type="xs:string"/>
                        <xs:element name="RVMS_USED" type="xs:string"/>
                      </xs:sequence>
                    </xs:complexType>
                  </xs:element>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
            <xs:element name="DEFAULT_USER_QUOTAS">
              <xs:complexType>
                <xs:sequence>
                    <xs:element name="DATASTORE_QUOTA" minOccurs="0" maxOccurs="1">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="DATASTORE" minOccurs="0" maxOccurs="unbounded">
                          <xs:complexType>
                            <xs:sequence>
                              <xs:element name="ID" type="xs:string"/>
                              <xs:element name="IMAGES" type="xs:string"/>
                              <xs:element name="IMAGES_USED" type="xs:string"/>
                              <xs:element name="SIZE" type="xs:string"/>
                              <xs:element name="SIZE_USED" type="xs:string"/>
                            </xs:sequence>
                          </xs:complexType>
                          </xs:element>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                    <xs:element name="NETWORK_QUOTA" minOccurs="0" maxOccurs="1">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="NETWORK" minOccurs="0" maxOccurs="unbounded">
                          <xs:complexType>
                            <xs:sequence>
                              <xs:element name="ID" type="xs:string"/>
                              <xs:element name="LEASES" type="xs:string"/>
                              <xs:element name="LEASES_USED" type="xs:string"/>
                            </xs:sequence>
                          </xs:complexType>
                          </xs:element>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                    <xs:element name="VM_QUOTA" minOccurs="0" maxOccurs="1">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="VM" minOccurs="0" maxOccurs="1">
                            <xs:complexType>
                              <xs:sequence>
                                <xs:element name="CPU" type="xs:string"/>
                                <xs:element name="CPU_USED" type="xs:string"/>
                                <xs:element name="MEMORY" type="xs:string"/>
                                <xs:element name="MEMORY_USED" type="xs:string"/>
                                <xs:element name="VMS" type="xs:string"/>
                                <xs:element name="VMS_USED" type="xs:string"/>
                              </xs:sequence>
                            </xs:complexType>
                          </xs:element>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                    <xs:element name="IMAGE_QUOTA" minOccurs="0" maxOccurs="1">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="IMAGE" minOccurs="0" maxOccurs="unbounded">
                            <xs:complexType>
                              <xs:sequence>
                                <xs:element name="ID" type="xs:string"/>
                                <xs:element name="RVMS" type="xs:string"/>
                                <xs:element name="RVMS_USED" type="xs:string"/>
                              </xs:sequence>
                            </xs:complexType>
                          </xs:element>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
    </xs:schema>

.. code:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" elementFormDefault="qualified"
      targetNamespace="http://opennebula.org/XMLSchema" xmlns="http://opennebula.org/XMLSchema">
      <xs:element name="USER_POOL">
        <xs:complexType>
          <xs:sequence maxOccurs="1" minOccurs="1">
            <xs:choice maxOccurs="unbounded" minOccurs="0">
              <xs:element name="USER" maxOccurs="unbounded" minOccurs="0">
                <xs:complexType>
                  <xs:sequence>
                    <xs:element name="ID" type="xs:integer"/>
                    <xs:element name="GID" type="xs:integer"/>
                    <xs:element name="GROUPS">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="ID" type="xs:integer" minOccurs="1" maxOccurs="unbounded"/>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                    <xs:element name="GNAME" type="xs:string"/>
                    <xs:element name="NAME" type="xs:string"/>
                    <xs:element name="PASSWORD" type="xs:string"/>
                    <xs:element name="AUTH_DRIVER" type="xs:string"/>
                    <xs:element name="ENABLED" type="xs:integer"/>
                    <xs:element name="LOGIN_TOKEN" type="xs:string"/>
                    <xs:element name="TEMPLATE" type="xs:anyType"/>
                  </xs:sequence>
                </xs:complexType>
              </xs:element>
              <xs:element name="QUOTAS" maxOccurs="unbounded" minOccurs="0">
                <xs:complexType>
                  <xs:sequence>
                    <xs:element name="ID" type="xs:integer"/>
                    <xs:element name="DATASTORE_QUOTA" minOccurs="0" maxOccurs="1">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="DATASTORE" minOccurs="0" maxOccurs="unbounded">
                          <xs:complexType>
                            <xs:sequence>
                              <xs:element name="ID" type="xs:string"/>
                              <xs:element name="IMAGES" type="xs:string"/>
                              <xs:element name="IMAGES_USED" type="xs:string"/>
                              <xs:element name="SIZE" type="xs:string"/>
                              <xs:element name="SIZE_USED" type="xs:string"/>
                            </xs:sequence>
                          </xs:complexType>
                          </xs:element>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                    <xs:element name="NETWORK_QUOTA" minOccurs="0" maxOccurs="1">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="NETWORK" minOccurs="0" maxOccurs="unbounded">
                          <xs:complexType>
                            <xs:sequence>
                              <xs:element name="ID" type="xs:string"/>
                              <xs:element name="LEASES" type="xs:string"/>
                              <xs:element name="LEASES_USED" type="xs:string"/>
                            </xs:sequence>
                          </xs:complexType>
                          </xs:element>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                    <xs:element name="VM_QUOTA" minOccurs="0" maxOccurs="1">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="VM" minOccurs="0" maxOccurs="1">
                            <xs:complexType>
                              <xs:sequence>
                                <xs:element name="CPU" type="xs:string"/>
                                <xs:element name="CPU_USED" type="xs:string"/>
                                <xs:element name="MEMORY" type="xs:string"/>
                                <xs:element name="MEMORY_USED" type="xs:string"/>
                                <xs:element name="VMS" type="xs:string"/>
                                <xs:element name="VMS_USED" type="xs:string"/>
                                <xs:element name="SYSTEM_DISK_SIZE" type="xs:string"/>
                                <xs:element name="SYSTEM_DISK_SIZE_USED" type="xs:string"/>
                              </xs:sequence>
                            </xs:complexType>
                          </xs:element>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                    <xs:element name="IMAGE_QUOTA" minOccurs="0" maxOccurs="1">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="IMAGE" minOccurs="0" maxOccurs="unbounded">
                            <xs:complexType>
                              <xs:sequence>
                                <xs:element name="ID" type="xs:string"/>
                                <xs:element name="RVMS" type="xs:string"/>
                                <xs:element name="RVMS_USED" type="xs:string"/>
                              </xs:sequence>
                            </xs:complexType>
                          </xs:element>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                  </xs:sequence>
                </xs:complexType>
              </xs:element>
            </xs:choice>
            <xs:element name="DEFAULT_USER_QUOTAS">
              <xs:complexType>
                <xs:sequence>
                    <xs:element name="DATASTORE_QUOTA" minOccurs="0" maxOccurs="1">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="DATASTORE" minOccurs="0" maxOccurs="unbounded">
                          <xs:complexType>
                            <xs:sequence>
                              <xs:element name="ID" type="xs:string"/>
                              <xs:element name="IMAGES" type="xs:string"/>
                              <xs:element name="IMAGES_USED" type="xs:string"/>
                              <xs:element name="SIZE" type="xs:string"/>
                              <xs:element name="SIZE_USED" type="xs:string"/>
                            </xs:sequence>
                          </xs:complexType>
                          </xs:element>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                    <xs:element name="NETWORK_QUOTA" minOccurs="0" maxOccurs="1">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="NETWORK" minOccurs="0" maxOccurs="unbounded">
                          <xs:complexType>
                            <xs:sequence>
                              <xs:element name="ID" type="xs:string"/>
                              <xs:element name="LEASES" type="xs:string"/>
                              <xs:element name="LEASES_USED" type="xs:string"/>
                            </xs:sequence>
                          </xs:complexType>
                          </xs:element>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                    <xs:element name="VM_QUOTA" minOccurs="0" maxOccurs="1">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="VM" minOccurs="0" maxOccurs="1">
                            <xs:complexType>
                              <xs:sequence>
                                <xs:element name="CPU" type="xs:string"/>
                                <xs:element name="CPU_USED" type="xs:string"/>
                                <xs:element name="MEMORY" type="xs:string"/>
                                <xs:element name="MEMORY_USED" type="xs:string"/>
                                <xs:element name="VMS" type="xs:string"/>
                                <xs:element name="VMS_USED" type="xs:string"/>
                              </xs:sequence>
                            </xs:complexType>
                          </xs:element>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                    <xs:element name="IMAGE_QUOTA" minOccurs="0" maxOccurs="1">
                      <xs:complexType>
                        <xs:sequence>
                          <xs:element name="IMAGE" minOccurs="0" maxOccurs="unbounded">
                            <xs:complexType>
                              <xs:sequence>
                                <xs:element name="ID" type="xs:string"/>
                                <xs:element name="RVMS" type="xs:string"/>
                                <xs:element name="RVMS_USED" type="xs:string"/>
                              </xs:sequence>
                            </xs:complexType>
                          </xs:element>
                        </xs:sequence>
                      </xs:complexType>
                    </xs:element>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
    </xs:schema>

Schemas for Virtual Machine
---------------------------

.. code:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" elementFormDefault="qualified"
      targetNamespace="http://opennebula.org/XMLSchema" xmlns="http://opennebula.org/XMLSchema">
      <xs:element name="VM">
        <xs:complexType>
          <xs:sequence>
            <xs:element name="ID" type="xs:integer"/>
            <xs:element name="UID" type="xs:integer"/>
            <xs:element name="GID" type="xs:integer"/>
            <xs:element name="UNAME" type="xs:string"/>
            <xs:element name="GNAME" type="xs:string"/>
            <xs:element name="NAME" type="xs:string"/>
            <xs:element name="PERMISSIONS" minOccurs="0" maxOccurs="1">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="OWNER_U" type="xs:integer"/>
                  <xs:element name="OWNER_M" type="xs:integer"/>
                  <xs:element name="OWNER_A" type="xs:integer"/>
                  <xs:element name="GROUP_U" type="xs:integer"/>
                  <xs:element name="GROUP_M" type="xs:integer"/>
                  <xs:element name="GROUP_A" type="xs:integer"/>
                  <xs:element name="OTHER_U" type="xs:integer"/>
                  <xs:element name="OTHER_M" type="xs:integer"/>
                  <xs:element name="OTHER_A" type="xs:integer"/>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
            <xs:element name="LAST_POLL" type="xs:integer"/>

            <!-- STATE values,
            see http://docs.opennebula.org/stable/user/references/vm_states.html

              INIT      = 0
              PENDING   = 1
              HOLD      = 2
              ACTIVE    = 3 In this state, the Life Cycle Manager state is relevant
              STOPPED   = 4
              SUSPENDED = 5
              DONE      = 6
              FAILED    = 7
              POWEROFF  = 8
              UNDEPLOYED = 9
            -->
            <xs:element name="STATE" type="xs:integer"/>

            <!-- LCM_STATE values, this sub-state is relevant only when STATE is
                 ACTIVE (4)

              LCM_INIT            = 0,
              PROLOG              = 1,
              BOOT                = 2,
              RUNNING             = 3,
              MIGRATE             = 4,
              SAVE_STOP           = 5,
              SAVE_SUSPEND        = 6,
              SAVE_MIGRATE        = 7,
              PROLOG_MIGRATE      = 8,
              PROLOG_RESUME       = 9,
              EPILOG_STOP         = 10,
              EPILOG              = 11,
              SHUTDOWN            = 12,
              CANCEL              = 13,
              FAILURE             = 14,
              CLEANUP_RESUBMIT    = 15,
              UNKNOWN             = 16,
              HOTPLUG             = 17,
              SHUTDOWN_POWEROFF   = 18,
              BOOT_UNKNOWN        = 19,
              BOOT_POWEROFF       = 20,
              BOOT_SUSPENDED      = 21,
              BOOT_STOPPED        = 22,
              CLEANUP_DELETE      = 23,
              HOTPLUG_SNAPSHOT    = 24,
              HOTPLUG_NIC         = 25,
              HOTPLUG_SAVEAS           = 26,
              HOTPLUG_SAVEAS_POWEROFF  = 27,
              HOTPLUG_SAVEAS_SUSPENDED = 28,
              SHUTDOWN_UNDEPLOY   = 29,
              EPILOG_UNDEPLOY     = 30,
              PROLOG_UNDEPLOY     = 31,
              BOOT_UNDEPLOY       = 32,
              HOTPLUG_PROLOG_POWEROFF = 33,
              HOTPLUG_EPILOG_POWEROFF = 34,
              BOOT_MIGRATE            = 35,
              BOOT_FAILURE            = 36,
              BOOT_MIGRATE_FAILURE    = 37,
              PROLOG_MIGRATE_FAILURE  = 38,
              PROLOG_FAILURE          = 39,
              EPILOG_FAILURE          = 40,
              EPILOG_STOP_FAILURE     = 41,
              EPILOG_UNDEPLOY_FAILURE = 42,
              PROLOG_MIGRATE_POWEROFF = 43,
              PROLOG_MIGRATE_POWEROFF_FAILURE = 44,
              PROLOG_MIGRATE_SUSPEND          = 45,
              PROLOG_MIGRATE_SUSPEND_FAILURE  = 46
              BOOT_UNDEPLOY_FAILURE   = 47,
              BOOT_STOPPED_FAILURE    = 48,
              PROLOG_RESUME_FAILURE   = 49,
              PROLOG_UNDEPLOY_FAILURE = 50,
              DISK_SNAPSHOT_POWEROFF         = 51,
              DISK_SNAPSHOT_REVERT_POWEROFF  = 52,
              DISK_SNAPSHOT_DELETE_POWEROFF  = 53,
              DISK_SNAPSHOT_SUSPENDED        = 54,
              DISK_SNAPSHOT_REVERT_SUSPENDED = 55,
              DISK_SNAPSHOT_DELETE_SUSPENDED = 56,
              DISK_SNAPSHOT        = 57,
              DISK_SNAPSHOT_REVERT = 58,
              DISK_SNAPSHOT_DELETE = 59
            -->
            <xs:element name="LCM_STATE" type="xs:integer"/>
            <xs:element name="PREV_STATE" type="xs:integer"/>
            <xs:element name="PREV_LCM_STATE" type="xs:integer"/>
            <xs:element name="RESCHED" type="xs:integer"/>
            <xs:element name="STIME" type="xs:integer"/>
            <xs:element name="ETIME" type="xs:integer"/>
            <xs:element name="DEPLOY_ID" type="xs:string"/>
            <xs:element name="MONITORING">
            <!--
              <xs:complexType>
                <xs:all>
                  <- Percentage of 1 CPU consumed (two fully consumed cpu is 200) ->
                  <xs:element name="CPU" type="xs:decimal" minOccurs="0" maxOccurs="1"/>

                  <- MEMORY consumption in kilobytes ->
                  <xs:element name="MEMORY" type="xs:integer" minOccurs="0" maxOccurs="1"/>

                  <- NETTX: Sent bytes to the network ->
                  <xs:element name="NETTX" type="xs:integer" minOccurs="0" maxOccurs="1"/>

                  <- NETRX: Received bytes from the network ->
                  <xs:element name="NETRX" type="xs:integer" minOccurs="0" maxOccurs="1"/>
                </xs:all>
              </xs:complexType>
            -->
            </xs:element>
            <xs:element name="TEMPLATE" type="xs:anyType"/>
            <xs:element name="USER_TEMPLATE" type="xs:anyType"/>
            <xs:element name="HISTORY_RECORDS">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="HISTORY" maxOccurs="unbounded" minOccurs="0">
                    <xs:complexType>
                      <xs:sequence>
                        <xs:element name="OID" type="xs:integer"/>
                        <xs:element name="SEQ" type="xs:integer"/>
                        <xs:element name="HOSTNAME" type="xs:string"/>
                        <xs:element name="HID" type="xs:integer"/>
                        <xs:element name="CID" type="xs:integer"/>
                        <xs:element name="STIME" type="xs:integer"/>
                        <xs:element name="ETIME" type="xs:integer"/>
                        <xs:element name="VMMMAD" type="xs:string"/>
                        <xs:element name="VNMMAD" type="xs:string"/>
                        <xs:element name="TMMAD" type="xs:string"/>
                        <xs:element name="DS_LOCATION" type="xs:string"/>
                        <xs:element name="DS_ID" type="xs:integer"/>
                        <xs:element name="PSTIME" type="xs:integer"/>
                        <xs:element name="PETIME" type="xs:integer"/>
                        <xs:element name="RSTIME" type="xs:integer"/>
                        <xs:element name="RETIME" type="xs:integer"/>
                        <xs:element name="ESTIME" type="xs:integer"/>
                        <xs:element name="EETIME" type="xs:integer"/>

                        <!-- REASON values:
                          NONE  = 0 History record is not closed yet
                          ERROR = 1 History record was closed because of an error
                          USER  = 2 History record was closed because of a user action
                        -->
                        <xs:element name="REASON" type="xs:integer"/>

                        <!-- ACTION values:
                          NONE_ACTION             = 0
                          MIGRATE_ACTION          = 1
                          LIVE_MIGRATE_ACTION     = 2
                          SHUTDOWN_ACTION         = 3
                          SHUTDOWN_HARD_ACTION    = 4
                          UNDEPLOY_ACTION         = 5
                          UNDEPLOY_HARD_ACTION    = 6
                          HOLD_ACTION             = 7
                          RELEASE_ACTION          = 8
                          STOP_ACTION             = 9
                          SUSPEND_ACTION          = 10
                          RESUME_ACTION           = 11
                          BOOT_ACTION             = 12
                          DELETE_ACTION           = 13
                          DELETE_RECREATE_ACTION  = 14
                          REBOOT_ACTION           = 15
                          REBOOT_HARD_ACTION      = 16
                          RESCHED_ACTION          = 17
                          UNRESCHED_ACTION        = 18
                          POWEROFF_ACTION         = 19
                          POWEROFF_HARD_ACTION    = 20
                          DISK_ATTACH_ACTION      = 21
                          DISK_DETACH_ACTION      = 22
                          NIC_ATTACH_ACTION       = 23
                          NIC_DETACH_ACTION       = 24
                          DISK_SNAPSHOT_CREATE_ACTION = 25
                          DISK_SNAPSHOT_DELETE_ACTION = 26
                        -->
                        <xs:element name="ACTION" type="xs:integer"/>
                      </xs:sequence>
                    </xs:complexType>
                  </xs:element>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
            <xs:element name="SNAPSHOTS" minOccurs="0" maxOccurs="unbounded">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="DISK_ID" type="xs:integer"/>
                  <xs:element name="SNAPSHOT" minOccurs="0" maxOccurs="unbounded">
                    <xs:complexType>
                      <xs:sequence>
                        <xs:element name="ACTIVE" type="xs:string" minOccurs="0" maxOccurs="1"/>
                        <xs:element name="CHILDREN" type="xs:string" minOccurs="0" maxOccurs="1"/>
                        <xs:element name="DATE" type="xs:integer"/>
                        <xs:element name="ID" type="xs:integer"/>
                        <xs:element name="NAME" type="xs:string" minOccurs="0" maxOccurs="1"/>
                        <xs:element name="PARENT" type="xs:integer"/>
                        <xs:element name="SIZE" type="xs:integer"/>
                      </xs:sequence>
                    </xs:complexType>
                  </xs:element>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
    </xs:schema>

.. code:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" elementFormDefault="unqualified"
        targetNamespace="http://opennebula.org/XMLSchema" xmlns="http://opennebula.org/XMLSchema">
        <xs:include schemaLocation="vm.xsd"/>
        <xs:element name="VM_POOL">
            <xs:complexType>
                <xs:sequence maxOccurs="1" minOccurs="1">
                    <xs:element ref="VM" maxOccurs="unbounded" minOccurs="0"/>
                </xs:sequence>
            </xs:complexType>
        </xs:element>
    </xs:schema>

Schemas for Virtual Machine Template
------------------------------------

.. code:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://opennebula.org/XMLSchema" elementFormDefault="qualified" targetNamespace="http://opennebula.org/XMLSchema">
      <xs:element name="VMTEMPLATE">
        <xs:complexType>
          <xs:sequence>
            <xs:element name="ID" type="xs:integer"/>
            <xs:element name="UID" type="xs:integer"/>
            <xs:element name="GID" type="xs:integer"/>
            <xs:element name="UNAME" type="xs:string"/>
            <xs:element name="GNAME" type="xs:string"/>
            <xs:element name="NAME" type="xs:string"/>
            <xs:element name="PERMISSIONS" minOccurs="1" maxOccurs="1">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="OWNER_U" type="xs:integer"/>
                  <xs:element name="OWNER_M" type="xs:integer"/>
                  <xs:element name="OWNER_A" type="xs:integer"/>
                  <xs:element name="GROUP_U" type="xs:integer"/>
                  <xs:element name="GROUP_M" type="xs:integer"/>
                  <xs:element name="GROUP_A" type="xs:integer"/>
                  <xs:element name="OTHER_U" type="xs:integer"/>
                  <xs:element name="OTHER_M" type="xs:integer"/>
                  <xs:element name="OTHER_A" type="xs:integer"/>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
            <xs:element name="REGTIME" type="xs:integer"/>
            <xs:element name="TEMPLATE" type="xs:anyType"/>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
    </xs:schema>

.. code:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" elementFormDefault="qualified"
      targetNamespace="http://opennebula.org/XMLSchema" xmlns="http://opennebula.org/XMLSchema">
      <xs:include schemaLocation="vmtemplate.xsd"/>
      <xs:element name="VMTEMPLATE_POOL">
        <xs:complexType>
          <xs:sequence maxOccurs="1" minOccurs="1">
            <xs:element ref="VMTEMPLATE" maxOccurs="unbounded" minOccurs="0"/>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
    </xs:schema>

Schemas for Virtual Network
---------------------------


.. code:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" elementFormDefault="qualified"
      targetNamespace="http://opennebula.org/XMLSchema" xmlns="http://opennebula.org/XMLSchema">
      <xs:element name="VNET">
        <xs:complexType>
          <xs:sequence>
            <xs:element name="ID" type="xs:integer"/>
            <xs:element name="UID" type="xs:integer"/>
            <xs:element name="GID" type="xs:integer"/>
            <xs:element name="UNAME" type="xs:string"/>
            <xs:element name="GNAME" type="xs:string"/>
            <xs:element name="NAME" type="xs:string"/>
            <xs:element name="PERMISSIONS" minOccurs="0" maxOccurs="1">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="OWNER_U" type="xs:integer"/>
                  <xs:element name="OWNER_M" type="xs:integer"/>
                  <xs:element name="OWNER_A" type="xs:integer"/>
                  <xs:element name="GROUP_U" type="xs:integer"/>
                  <xs:element name="GROUP_M" type="xs:integer"/>
                  <xs:element name="GROUP_A" type="xs:integer"/>
                  <xs:element name="OTHER_U" type="xs:integer"/>
                  <xs:element name="OTHER_M" type="xs:integer"/>
                  <xs:element name="OTHER_A" type="xs:integer"/>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
            <xs:element name="CLUSTER_ID" type="xs:integer"/>
            <xs:element name="CLUSTER" type="xs:string"/>
            <xs:element name="BRIDGE" type="xs:string"/>
            <xs:element name="VLAN" type="xs:integer"/>
            <xs:element name="PARENT_NETWORK_ID" type="xs:string"/>
            <xs:element name="PHYDEV" type="xs:string"/>
            <xs:element name="VLAN_ID" type="xs:string"/>
            <xs:element name="USED_LEASES" type="xs:integer"/>
            <xs:element name="TEMPLATE" type="xs:anyType"/>
            <xs:element name="AR_POOL">
              <xs:complexType>
                <xs:sequence minOccurs="0">
                  <xs:element name="AR" minOccurs="0" maxOccurs="unbounded">
                    <xs:complexType>
                      <xs:sequence>
                        <xs:element name="AR_ID" type="xs:string"/>
                        <xs:element name="GLOBAL_PREFIX" type="xs:string" minOccurs="0"/>
                        <xs:element name="IP" type="xs:string" minOccurs="0"/>
                        <xs:element name="MAC" type="xs:string"/>
                        <xs:element name="PARENT_NETWORK_AR_ID" type="xs:string" minOccurs="0"/>
                        <xs:element name="SIZE" type="xs:integer"/>
                        <xs:element name="TYPE" type="xs:string"/>
                        <xs:element name="ULA_PREFIX" type="xs:string" minOccurs="0"/>
                        <xs:element name="VLAN" type="xs:string"  minOccurs="0"/>
                        <xs:element name="MAC_END" type="xs:string" minOccurs="0"/>
                        <xs:element name="IP_END" type="xs:string" minOccurs="0"/>
                        <xs:element name="IP6_ULA" type="xs:string" minOccurs="0"/>
                        <xs:element name="IP6_ULA_END" type="xs:string" minOccurs="0"/>
                        <xs:element name="IP6_GLOBAL" type="xs:string" minOccurs="0"/>
                        <xs:element name="IP6_GLOBAL_END" type="xs:string" minOccurs="0"/>
                        <xs:element name="USED_LEASES" type="xs:string"/>
                        <xs:element name="LEASES" minOccurs="0" maxOccurs="1">
                          <xs:complexType>
                            <xs:sequence>
                              <xs:element name="LEASE" minOccurs="0" maxOccurs="unbounded">
                                <xs:complexType>
                                  <xs:all>
                                    <xs:element name="IP" type="xs:string" minOccurs="0"/>
                                    <xs:element name="IP6_GLOBAL" type="xs:string" minOccurs="0"/>
                                    <xs:element name="IP6_LINK" type="xs:string" minOccurs="0"/>
                                    <xs:element name="IP6_ULA" type="xs:string" minOccurs="0"/>
                                    <xs:element name="MAC" type="xs:string"/>
                                    <xs:element name="VM" type="xs:integer" minOccurs="0"/>
                                    <xs:element name="VNET" type="xs:integer" minOccurs="0"/>
                                  </xs:all>
                                </xs:complexType>
                              </xs:element>
                            </xs:sequence>
                          </xs:complexType>
                        </xs:element>
                      </xs:sequence>
                    </xs:complexType>
                  </xs:element>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
    </xs:schema>

.. code:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" elementFormDefault="qualified"
      targetNamespace="http://opennebula.org/XMLSchema" xmlns="http://opennebula.org/XMLSchema">
      <xs:include schemaLocation="vnet.xsd"/>
      <xs:element name="VNET_POOL">
        <xs:complexType>
          <xs:sequence maxOccurs="1" minOccurs="1">
            <xs:element name="VNET" maxOccurs="unbounded" minOccurs="0">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="ID" type="xs:integer"/>
                  <xs:element name="UID" type="xs:integer"/>
                  <xs:element name="GID" type="xs:integer"/>
                  <xs:element name="UNAME" type="xs:string"/>
                  <xs:element name="GNAME" type="xs:string"/>
                  <xs:element name="NAME" type="xs:string"/>
                  <xs:element name="PERMISSIONS" minOccurs="0" maxOccurs="1">
                    <xs:complexType>
                      <xs:sequence>
                        <xs:element name="OWNER_U" type="xs:integer"/>
                        <xs:element name="OWNER_M" type="xs:integer"/>
                        <xs:element name="OWNER_A" type="xs:integer"/>
                        <xs:element name="GROUP_U" type="xs:integer"/>
                        <xs:element name="GROUP_M" type="xs:integer"/>
                        <xs:element name="GROUP_A" type="xs:integer"/>
                        <xs:element name="OTHER_U" type="xs:integer"/>
                        <xs:element name="OTHER_M" type="xs:integer"/>
                        <xs:element name="OTHER_A" type="xs:integer"/>
                      </xs:sequence>
                    </xs:complexType>
                  </xs:element>
                  <xs:element name="CLUSTER_ID" type="xs:integer"/>
                  <xs:element name="CLUSTER" type="xs:string"/>
                  <xs:element name="BRIDGE" type="xs:string"/>
                  <xs:element name="VLAN" type="xs:integer"/>
                  <xs:element name="PARENT_NETWORK_ID" type="xs:string"/>
                  <xs:element name="PHYDEV" type="xs:string"/>
                  <xs:element name="VLAN_ID" type="xs:string"/>
                  <xs:element name="USED_LEASES" type="xs:integer"/>
                  <xs:element name="TEMPLATE" type="xs:anyType"/>
                  <xs:element name="AR_POOL">
                    <xs:complexType>
                      <xs:sequence minOccurs="0">
                        <xs:element name="AR" minOccurs="0" maxOccurs="unbounded">
                          <xs:complexType>
                            <xs:sequence>
                              <xs:element name="ALLOCATED" type="xs:string" minOccurs="0"/>
                              <xs:element name="AR_ID" type="xs:string"/>
                              <xs:element name="GLOBAL_PREFIX" type="xs:string" minOccurs="0"/>
                              <xs:element name="IP" type="xs:string" minOccurs="0"/>
                              <xs:element name="MAC" type="xs:string"/>
                              <xs:element name="PARENT_NETWORK_AR_ID" type="xs:string" minOccurs="0"/>
                              <xs:element name="SIZE" type="xs:integer"/>
                              <xs:element name="TYPE" type="xs:string"/>
                              <xs:element name="ULA_PREFIX" type="xs:string" minOccurs="0"/>
                              <xs:element name="VLAN" type="xs:string"  minOccurs="0"/>
                            </xs:sequence>
                          </xs:complexType>
                        </xs:element>
                      </xs:sequence>
                    </xs:complexType>
                  </xs:element>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
    </xs:schema>


Schemas for Accounting
----------------------

.. code:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" elementFormDefault="qualified"
      targetNamespace="http://opennebula.org/XMLSchema" xmlns="http://opennebula.org/XMLSchema">

      <xs:element name="HISTORY_RECORDS">
        <xs:complexType>
          <xs:sequence maxOccurs="1" minOccurs="1">
            <xs:element ref="HISTORY" maxOccurs="unbounded" minOccurs="0"/>
          </xs:sequence>
        </xs:complexType>
      </xs:element>

      <xs:element name="HISTORY">
        <xs:complexType>
          <xs:sequence>
            <xs:element name="OID" type="xs:integer"/>
            <xs:element name="SEQ" type="xs:integer"/>
            <xs:element name="HOSTNAME" type="xs:string"/>
            <xs:element name="HID" type="xs:integer"/>
            <xs:element name="CID" type="xs:integer"/>
            <xs:element name="STIME" type="xs:integer"/>
            <xs:element name="ETIME" type="xs:integer"/>
            <xs:element name="VMMMAD" type="xs:string"/>
            <xs:element name="VNMMAD" type="xs:string"/>
            <xs:element name="TMMAD" type="xs:string"/>
            <xs:element name="DS_LOCATION" type="xs:string"/>
            <xs:element name="DS_ID" type="xs:integer"/>
            <xs:element name="PSTIME" type="xs:integer"/>
            <xs:element name="PETIME" type="xs:integer"/>
            <xs:element name="RSTIME" type="xs:integer"/>
            <xs:element name="RETIME" type="xs:integer"/>
            <xs:element name="ESTIME" type="xs:integer"/>
            <xs:element name="EETIME" type="xs:integer"/>

            <!-- REASON values:
              NONE  = 0 History record is not closed yet
              ERROR = 1 History record was closed because of an error
              USER  = 2 History record was closed because of a user action
            -->
            <xs:element name="REASON" type="xs:integer"/>

            <!-- ACTION values:
              NONE_ACTION             = 0
              MIGRATE_ACTION          = 1
              LIVE_MIGRATE_ACTION     = 2
              SHUTDOWN_ACTION         = 3
              SHUTDOWN_HARD_ACTION    = 4
              UNDEPLOY_ACTION         = 5
              UNDEPLOY_HARD_ACTION    = 6
              HOLD_ACTION             = 7
              RELEASE_ACTION          = 8
              STOP_ACTION             = 9
              SUSPEND_ACTION          = 10
              RESUME_ACTION           = 11
              BOOT_ACTION             = 12
              DELETE_ACTION           = 13
              DELETE_RECREATE_ACTION  = 14
              REBOOT_ACTION           = 15
              REBOOT_HARD_ACTION      = 16
              RESCHED_ACTION          = 17
              UNRESCHED_ACTION        = 18
              POWEROFF_ACTION         = 19
              POWEROFF_HARD_ACTION    = 20
              DISK_ATTACH_ACTION      = 21
              DISK_DETACH_ACTION      = 22
              NIC_ATTACH_ACTION       = 23
              NIC_DETACH_ACTION       = 24
              DISK_SNAPSHOT_CREATE_ACTION = 25
              DISK_SNAPSHOT_DELETE_ACTION = 26
            -->
            <xs:element name="ACTION" type="xs:integer"/>

            <xs:element name="VM">
              <xs:complexType>
                <xs:sequence>
                  <xs:element name="ID" type="xs:integer"/>
                  <xs:element name="UID" type="xs:integer"/>
                  <xs:element name="GID" type="xs:integer"/>
                  <xs:element name="UNAME" type="xs:string"/>
                  <xs:element name="GNAME" type="xs:string"/>
                  <xs:element name="NAME" type="xs:string"/>
                  <xs:element name="PERMISSIONS" minOccurs="0" maxOccurs="1">
                    <xs:complexType>
                      <xs:sequence>
                        <xs:element name="OWNER_U" type="xs:integer"/>
                        <xs:element name="OWNER_M" type="xs:integer"/>
                        <xs:element name="OWNER_A" type="xs:integer"/>
                        <xs:element name="GROUP_U" type="xs:integer"/>
                        <xs:element name="GROUP_M" type="xs:integer"/>
                        <xs:element name="GROUP_A" type="xs:integer"/>
                        <xs:element name="OTHER_U" type="xs:integer"/>
                        <xs:element name="OTHER_M" type="xs:integer"/>
                        <xs:element name="OTHER_A" type="xs:integer"/>
                      </xs:sequence>
                    </xs:complexType>
                  </xs:element>
                  <xs:element name="LAST_POLL" type="xs:integer"/>

                  <!-- STATE values,
                  see http://opennebula.org/documentation:documentation:api#actions_for_virtual_machine_management

                    INIT      = 0
                    PENDING   = 1
                    HOLD      = 2
                    ACTIVE    = 3 In this state, the Life Cycle Manager state is relevant
                    STOPPED   = 4
                    SUSPENDED = 5
                    DONE      = 6
                    FAILED    = 7
                    POWEROFF  = 8
                    UNDEPLOYED = 9
                  -->
                  <xs:element name="STATE" type="xs:integer"/>

                  <!-- LCM_STATE values, this sub-state is relevant only when STATE is
                       ACTIVE (4)

                    LCM_INIT            = 0,
                    PROLOG              = 1,
                    BOOT                = 2,
                    RUNNING             = 3,
                    MIGRATE             = 4,
                    SAVE_STOP           = 5,
                    SAVE_SUSPEND        = 6,
                    SAVE_MIGRATE        = 7,
                    PROLOG_MIGRATE      = 8,
                    PROLOG_RESUME       = 9,
                    EPILOG_STOP         = 10,
                    EPILOG              = 11,
                    SHUTDOWN            = 12,
                    CANCEL              = 13,
                    FAILURE             = 14,
                    CLEANUP_RESUBMIT    = 15,
                    UNKNOWN             = 16,
                    HOTPLUG             = 17,
                    SHUTDOWN_POWEROFF   = 18,
                    BOOT_UNKNOWN        = 19,
                    BOOT_POWEROFF       = 20,
                    BOOT_SUSPENDED      = 21,
                    BOOT_STOPPED        = 22,
                    CLEANUP_DELETE      = 23,
                    HOTPLUG_SNAPSHOT    = 24,
                    HOTPLUG_NIC         = 25,
                    HOTPLUG_SAVEAS           = 26,
                    HOTPLUG_SAVEAS_POWEROFF  = 27,
                    HOTPLUG_SAVEAS_SUSPENDED = 28,
                    SHUTDOWN_UNDEPLOY   = 29,
                    EPILOG_UNDEPLOY     = 30,
                    PROLOG_UNDEPLOY     = 31,
                    BOOT_UNDEPLOY       = 32,
                    HOTPLUG_PROLOG_POWEROFF = 33,
                    HOTPLUG_EPILOG_POWEROFF = 34,
                    BOOT_MIGRATE            = 35,
                    BOOT_FAILURE            = 36,
                    BOOT_MIGRATE_FAILURE    = 37,
                    PROLOG_MIGRATE_FAILURE  = 38,
                    PROLOG_FAILURE          = 39,
                    EPILOG_FAILURE          = 40,
                    EPILOG_STOP_FAILURE     = 41,
                    EPILOG_UNDEPLOY_FAILURE = 42,
                    PROLOG_MIGRATE_POWEROFF = 43,
                    PROLOG_MIGRATE_POWEROFF_FAILURE = 44,
                    PROLOG_MIGRATE_SUSPEND          = 45,
                    PROLOG_MIGRATE_SUSPEND_FAILURE  = 46
                    BOOT_UNDEPLOY_FAILURE   = 47,
                    BOOT_STOPPED_FAILURE    = 48,
                    PROLOG_RESUME_FAILURE   = 49,
                    PROLOG_UNDEPLOY_FAILURE = 50,
                    DISK_SNAPSHOT_POWEROFF         = 51,
                    DISK_SNAPSHOT_REVERT_POWEROFF  = 52,
                    DISK_SNAPSHOT_DELETE_POWEROFF  = 53,
                    DISK_SNAPSHOT_SUSPENDED        = 54,
                    DISK_SNAPSHOT_REVERT_SUSPENDED = 55,
                    DISK_SNAPSHOT_DELETE_SUSPENDED = 56,
                    DISK_SNAPSHOT        = 57,
                    DISK_SNAPSHOT_REVERT = 58,
                    DISK_SNAPSHOT_DELETE = 59
                  -->
                  <xs:element name="LCM_STATE" type="xs:integer"/>
                  <xs:element name="PREV_STATE" type="xs:integer"/>
                  <xs:element name="PREV_LCM_STATE" type="xs:integer"/>
                  <xs:element name="RESCHED" type="xs:integer"/>
                  <xs:element name="STIME" type="xs:integer"/>
                  <xs:element name="ETIME" type="xs:integer"/>
                  <xs:element name="DEPLOY_ID" type="xs:string"/>
                  <xs:element name="MONITORING">
                  <!--
                    <xs:complexType>
                      <xs:all>
                        <- Percentage of 1 CPU consumed (two fully consumed cpu is 200) ->
                        <xs:element name="CPU" type="xs:decimal" minOccurs="0" maxOccurs="1"/>

                        <- MEMORY consumption in kilobytes ->
                        <xs:element name="MEMORY" type="xs:integer" minOccurs="0" maxOccurs="1"/>

                        <- NETTX: Sent bytes to the network ->
                        <xs:element name="NETTX" type="xs:integer" minOccurs="0" maxOccurs="1"/>

                        <- NETRX: Received bytes from the network ->
                        <xs:element name="NETRX" type="xs:integer" minOccurs="0" maxOccurs="1"/>
                      </xs:all>
                    </xs:complexType>
                  -->
                  </xs:element>
                  <xs:element name="TEMPLATE" type="xs:anyType"/>
                  <xs:element name="USER_TEMPLATE" type="xs:anyType"/>
                  <xs:element name="HISTORY_RECORDS">
                  </xs:element>
                  <xs:element name="SNAPSHOTS" minOccurs="0" maxOccurs="unbounded">
                    <xs:complexType>
                      <xs:sequence>
                        <xs:element name="DISK_ID" type="xs:integer"/>
                        <xs:element name="SNAPSHOT" minOccurs="0" maxOccurs="unbounded">
                          <xs:complexType>
                            <xs:sequence>
                              <xs:element name="ACTIVE" type="xs:string" minOccurs="0" maxOccurs="1"/>
                              <xs:element name="CHILDREN" type="xs:string" minOccurs="0" maxOccurs="1"/>
                              <xs:element name="DATE" type="xs:integer"/>
                              <xs:element name="ID" type="xs:integer"/>
                              <xs:element name="NAME" type="xs:string" minOccurs="0" maxOccurs="1"/>
                              <xs:element name="PARENT" type="xs:integer"/>
                              <xs:element name="SIZE" type="xs:integer"/>
                            </xs:sequence>
                          </xs:complexType>
                        </xs:element>
                      </xs:sequence>
                    </xs:complexType>
                  </xs:element>
                </xs:sequence>
              </xs:complexType>
            </xs:element>
          </xs:sequence>
        </xs:complexType>
      </xs:element>
    </xs:schema>

.. |FIXME| image:: /images/fixme.gif
