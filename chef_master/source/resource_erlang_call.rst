=====================================================
erl_call
=====================================================
`[edit on GitHub] <https://github.com/chef/chef-web-docs/blob/master/chef_master/source/resource_erlang_call.rst>`__

Use the **erl_call** resource to connect to a node located within a distributed Erlang system. Commands that are executed with this resource are (by their nature) not idempotent, as they are typically unique to the environment in which they are run. Use ``not_if`` and ``only_if`` to guard this resource for idempotence.



.. note:: The ``erl_call`` command needs to be on the path for this resource to work properly.

**Removed from Chef Client 14.0**

Syntax
=====================================================
A **erl_call** resource block connects to a node located within a distributed Erlang system:

.. code-block:: ruby

   erl_call 'list names' do
     code 'net_adm:names().'
     distributed true
     node_name 'chef@latte'
   end

The full syntax for all of the properties that are available to the **erl_call** resource is:

.. code-block:: ruby

   erl_call 'name' do
     code                       String
     cookie                     String
     distributed                true, false
     name_type                  String
     node_name                  String
     notifies                   # see description
     subscribes                 # see description
     action                     Symbol # defaults to :run if not specified
   end

where

* ``erl_call`` is the resource
* ``name`` is the name of the resource block
* ``action`` identifies the steps Chef Infra Client will take to bring the node into the desired state
* ``code``, ``cookie``, ``distributed``, ``name_type``, and ``node_name`` are properties of this resource, with the Ruby type shown. See "Properties" section below for more information about all of the properties that may be used with this resource.

Actions
=====================================================
This resource has the following actions:

``:nothing``
   Prevent the Erlang call from running.

``:run``
   Default. Run the Erlang call.

Properties
=====================================================
This resource has the following properties:

``code``
   **Ruby Type:** String

   The code to be executed on a node located within a distributed Erlang system. Default value: ``q()``.

``cookie``
   **Ruby Type:** String

   The magic cookie for the node to which a connection is made.

``distributed``
   **Ruby Type:** true, false | **Default Value:** ``false``

   The node is a distributed Erlang node.

``ignore_failure``
   **Ruby Type:** true, false | **Default Value:** ``false``

   Continue running a recipe if a resource fails for any reason.

``name_type``
   **Ruby Type:** String

   The ``node_name`` property as a short node name (``sname``) or a long node name (``name``). A node with a long node name cannot communicate with a node with a short node name. Default value: ``sname``.

``node_name``
   **Ruby Type:** String

   The hostname to which the node is to connect. Default value: ``chef@localhost``.

``notifies``
   **Ruby Type:** Symbol, 'Chef::Resource[String]'

   .. tag resources_common_notification_notifies

   A resource may notify another resource to take action when its state changes. Specify a ``'resource[name]'``, the ``:action`` that resource should take, and then the ``:timer`` for that action. A resource may notify more than one resource; use a ``notifies`` statement for each resource to be notified.

   .. end_tag

   .. tag resources_common_notification_timers

   A timer specifies the point during a Chef Infra Client run at which a notification is run. The following timers are available:

   ``:before``
      Specifies that the action on a notified resource should be run before processing the resource block in which the notification is located.

   ``:delayed``
      Default. Specifies that a notification should be queued up, and then executed at the end of a Chef Infra Client run.

   ``:immediate``, ``:immediately``
      Specifies that a notification should be run immediately, per resource notified.

   .. end_tag

   .. tag resources_common_notification_notifies_syntax

   The syntax for ``notifies`` is:

   .. code-block:: ruby

     notifies :action, 'resource[name]', :timer

   .. end_tag

``retries``
   **Ruby Type:** Integer | **Default Value:** ``0``

   The number of attempts to catch exceptions and retry the resource.

``retry_delay``
   **Ruby Type:** Integer | **Default Value:** ``2``

   The retry delay (in seconds).

``subscribes``
   **Ruby Type:** Symbol, 'Chef::Resource[String]'

   .. tag resources_common_notification_subscribes

   A resource may listen to another resource, and then take action if the state of the resource being listened to changes. Specify a ``'resource[name]'``, the ``:action`` to be taken, and then the ``:timer`` for that action.

   Note that ``subscribes`` does not apply the specified action to the resource that it listens to - for example:

   .. code-block:: ruby

    file '/etc/nginx/ssl/example.crt' do
      mode '0600'
      owner 'root'
    end

    service 'nginx' do
      subscribes :reload, 'file[/etc/nginx/ssl/example.crt]', :immediately
    end

   In this case the ``subscribes`` property reloads the ``nginx`` service whenever its certificate file, located under ``/etc/nginx/ssl/example.crt``, is updated. ``subscribes`` does not make any changes to the certificate file itself, it merely listens for a change to the file, and executes the ``:reload`` action for its resource (in this example ``nginx``) when a change is detected.

   .. end_tag

   .. tag resources_common_notification_timers

   A timer specifies the point during a Chef Infra Client run at which a notification is run. The following timers are available:

   ``:before``
      Specifies that the action on a notified resource should be run before processing the resource block in which the notification is located.

   ``:delayed``
      Default. Specifies that a notification should be queued up, and then executed at the end of a Chef Infra Client run.

   ``:immediate``, ``:immediately``
      Specifies that a notification should be run immediately, per resource notified.

   .. end_tag

   .. tag resources_common_notification_subscribes_syntax

   The syntax for ``subscribes`` is:

   .. code-block:: ruby

      subscribes :action, 'resource[name]', :timer

   .. end_tag

Guards
-----------------------------------------------------
.. tag resources_common_guards

A guard property can be used to evaluate the state of a node during the execution phase of a Chef Infra Client run. Based on the results of this evaluation, a guard property is then used to tell Chef Infra Client if it should continue executing a resource. A guard property accepts either a string value or a Ruby block value:

* A string is executed as a shell command. If the command returns ``0``, the guard is applied. If the command returns any other value, then the guard property is not applied. String guards in a **powershell_script** run Windows PowerShell commands and may return ``true`` in addition to ``0``.
* A block is executed as Ruby code that must return either ``true`` or ``false``. If the block returns ``true``, the guard property is applied. If the block returns ``false``, the guard property is not applied.

A guard property is useful for ensuring that a resource is idempotent by allowing that resource to test for the desired state as it is being executed, and then if the desired state is present, for Chef Infra Client to do nothing.

.. end_tag

**Properties**

.. tag resources_common_guards_properties

The following properties can be used to define a guard that is evaluated during the execution phase of a Chef Infra Client run:

``not_if``
  Prevent a resource from executing when the condition returns ``true``.

``only_if``
  Allow a resource to execute only if the condition returns ``true``.

.. end_tag

**Arguments**

.. tag resources_common_guards_arguments

The following arguments can be used with the ``not_if`` or ``only_if`` guard properties:

``:user``
   Specify the user that a command will run as. For example:

   .. code-block:: ruby

      not_if 'grep adam /etc/passwd', :user => 'adam'

``:group``
   Specify the group that a command will run as. For example:

   .. code-block:: ruby

      not_if 'grep adam /etc/passwd', :group => 'adam'

``:environment``
   Specify a Hash of environment variables to be set. For example:

   .. code-block:: ruby

      not_if 'grep adam /etc/passwd', :environment => {
        'HOME' => '/home/adam'
      }

``:cwd``
   Set the current working directory before running a command. For example:

   .. code-block:: ruby

      not_if 'grep adam passwd', :cwd => '/etc'

``:timeout``
   Set a timeout for a command. For example:

   .. code-block:: ruby

      not_if 'sleep 10000', :timeout => 10

.. end_tag

Examples
=====================================================
The following examples demonstrate various approaches for using resources in recipes:

**Run a command**

.. To run a command on an Erlang node:

.. code-block:: ruby

   erl_call 'list names' do
     code 'net_adm:names().'
     distributed true
     node_name 'chef@latte'
   end


