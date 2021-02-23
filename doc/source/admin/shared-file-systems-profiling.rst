.. _shared_file_systems_profiling:

========================================
Profiling the Shared File Systems service
========================================

Profiler
^^^^^^^^

The detailed description of the profiler and its config options is available at
`Profiler docs <https://docs.openstack.org/osprofiler/latest/user/index.html>`_.


Using Profiler
^^^^^^^^^^^^^^

To start profiling Manila code, the following steps have to be taken:

#. Add the following lines to the ``/etc/manila/manila.conf`` file (the
   profiling is disabled by default).

   .. code-block:: console

      [profiler]
      connection_string = messaging://
      hmac_keys = SECRET_KEY
      trace_sqlalchemy = True
      enabled = True

   Examples of possible values for ``connection_string`` option:

   * ``messaging://`` - use oslo_messaging driver for sending spans.
   * ``redis://127.0.0.1:6379`` - use redis driver for sending spans.
   * ``mongodb://127.0.0.1:27017`` - use mongodb driver for sending spans.
   * ``elasticsearch://127.0.0.1:9200`` - use elasticsearch driver for sending spans.
   * ``jaeger://127.0.0.1:6831`` - use jaeger tracing as driver for sending spans.

#. Restart all manila services and keystone service.

#. To verify profiler with manilaclient, run any command with --profile <key>.
   The key (e.g. SECRET_KEY) should be one of the hmac_keys mentioned in
   manila.conf. To generate correct profiling information across all services
   at least one key needs to be consistent between OpenStack projects.

   .. code-block:: console

       $manila --profile SECRET_KEY create NFS 1 --share-type <share_type>
       #It will print <Trace ID>

#. To verify profiler with openstackclient, run any command with
   --os-profile <key>.

   .. code-block:: console

       $openstack --os-profile SECRET_KEY share create NFS 1 --share-type <share_type>
       #It will print <Trace ID>

#. The display the traces in HTML, run below command.

   .. code-block:: console

       $osprofiler trace show --html <Trace ID> --connection-string <connection_string> --out <output.html
       #The <connection_string> should be the one defined in manila.conf
