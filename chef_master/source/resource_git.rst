=====================================================
git resource
=====================================================
`[edit on GitHub] <https://github.com/chef/chef-web-docs/blob/master/chef_master/source/resource_git.rst>`__

.. tag resource_scm_git

Use the **git** resource to manage source control resources that exist in a git repository. git version 1.6.5 (or higher) is required to use all of the functionality in the **git** resource.

.. end_tag

Syntax
=====================================================
A **git** resource block manages source control resources that exist in a git repository:

.. code-block:: ruby

   git "#{Chef::Config[:file_cache_path]}/app_name" do
     repository node[:app_name][:git_repository]
     revision node[:app_name][:git_revision]
     action :sync
   end

The full syntax for all of the properties that are available to the **git** resource is:

.. code-block:: ruby

  git 'name' do
    additional_remotes      Hash
    checkout_branch         String # default value: "deploy"
    depth                   Integer
    destination             String # default value: 'name' unless specified
    enable_checkout         true, false # default value: true
    enable_submodules       true, false # default value: false
    environment             Hash
    group                   String, Integer
    remote                  String # default value: "origin"
    repository              String
    revision                String # default value: "HEAD"
    ssh_wrapper             String
    timeout                 Integer
    user                    String, Integer
    action                  Symbol # defaults to :sync if not specified
  end

where:

* ``git`` is the resource.
* ``name`` is the name given to the resource block.
* ``action`` identifies which steps Chef Infra Client will take to bring the node into the desired state.
* ``additional_remotes``, ``checkout_branch``, ``depth``, ``destination``, ``enable_checkout``, ``enable_submodules``, ``environment``, ``group``, ``remote``, ``repository``, ``revision``, ``ssh_wrapper``, ``timeout``, and ``user`` are the properties available to this resource.

Actions
=====================================================

The git resource has the following actions:

``:checkout``
   Clone or check out the source. When a checkout is available, this provider does nothing.

``:export``
   Export the source, excluding or removing any version control artifacts.

``:nothing``
   .. tag resources_common_actions_nothing

   This resource block does not act unless notified by another resource to take action. Once notified, this resource block either runs immediately or is queued up to run at the end of a Chef Infra Client run.

   .. end_tag

``:sync``
   Default. Update the source to the specified version, or get a new clone or checkout. This action causes a hard reset of the index and working tree, discarding any uncommitted changes.

Properties
=====================================================

The git resource has the following properties:

``additional_remotes``
   **Ruby Type:** Hash

   A Hash of additional remotes that are added to the git repository configuration.

``checkout_branch``
   **Ruby Type:** String | **Default Value:** ``deploy``

   Do a one-time checkout from git **or** use when a branch in the upstream repository is named ``deploy``. To prevent the **git** resource from attempting to check out master from master, set ``enable_checkout`` to ``false`` when using the ``checkout_branch`` property. See ``revision``.

``depth``
   **Ruby Type:** Integer

   The number of past revisions to be included in the git shallow clone. Unless specified the default behavior will do a full clone.

``destination``
   **Ruby Type:** String | **Default Value:** ``The resource block's name``

   The location path to which the source is to be cloned, checked out, or exported. Default value: the ``name`` of the resource block.

``enable_checkout``
   **Ruby Type:** true, false | **Default Value:** ``true``

   Check out a repo from master. Set to ``false`` when using the ``checkout_branch`` attribute to prevent the **git** resource from attempting to check out master from master.

``enable_submodules``
   **Ruby Type:** true, false | **Default Value:** ``false``

   Perform a sub-module initialization and update.

``environment``
   **Ruby Type:** Hash

   A Hash of environment variables in the form of ``({"ENV_VARIABLE" => "VALUE"})``. (These variables must exist for a command to be run successfully.)

   .. note:: The **git** provider automatically sets the ``ENV['HOME']`` and ``ENV['GIT_SSH']`` environment variables. To override this behavior and provide different values, add ``ENV['HOME']`` and/or ``ENV['GIT_SSH']`` to the ``environment`` Hash.

``group``
   **Ruby Type:** String, Integer

   The system group that is responsible for the checked-out code.


``remote``
   **Ruby Type:** String | **Default Value:** ``"origin"``

   The remote repository to use when synchronizing an existing clone.

``repository``
   **Ruby Type:** String

   The URI for the git repository.

``revision``
   **Ruby Type:** String | **Default Value:** ``HEAD``

   A branch, tag, or commit to be synchronized with git. This can be symbolic, like ``HEAD`` or it can be a source control management-specific revision identifier. See ``checkout_branch``.

   The value of the ``revision`` attribute may change over time. From one branch to another, to a tag, to a specific SHA for a commit, and then back to a branch. The ``revision`` attribute may even be changed in a way where history gets rewritten.

   Instead of tracking a specific branch or doing a headless checkout, Chef Infra Client maintains its own branch (via the **git** resource) that does not exist in the upstream repository. Chef Infra Client is then free to forcibly check out this branch to any commit without destroying the local history of an existing branch.

   For example, to explicitly track an upstream repository's master branch:

   .. code-block:: ruby

      revision 'master'

   Use the ``git rev-parse`` and ``git ls-remote`` commands to verify that Chef Infra Client is synchronizing commits correctly. (Chef Infra Client always runs ``git ls-remote`` on the upstream repository to verify the commit is made to the correct repository.)

``ssh_wrapper``
   **Ruby Type:** String

   The path to the wrapper script used when running SSH with git. The ``GIT_SSH`` environment variable is set to this.

``timeout``
   **Ruby Type:** Integer

   The amount of time (in seconds) to wait for a command to execute before timing out. When this property is specified using the **deploy** resource, the value of the ``timeout`` property is passed from the **deploy** resource to the **git** resource.

``user``
   **Ruby Type:** String, Integer

   The system user that is responsible for the checked-out code. Default value: the home directory of this user, as indicated by the ``HOME`` environment variable.

Common Resource Functionality
=====================================================

Chef resources include common properties, notifications, and resource guards.

Common Properties
-----------------------------------------------------

.. tag resources_common_properties

The following properties are common to every resource:

``ignore_failure``
  **Ruby Type:** true, false | **Default Value:** ``false``

  Continue running a recipe if a resource fails for any reason.

``retries``
  **Ruby Type:** Integer | **Default Value:** ``0``

  The number of attempts to catch exceptions and retry the resource.

``retry_delay``
  **Ruby Type:** Integer | **Default Value:** ``2``

  The retry delay (in seconds).

``sensitive``
  **Ruby Type:** true, false | **Default Value:** ``false``

  Ensure that sensitive resource data is not logged by Chef Infra Client.

.. end_tag

Notifications
-----------------------------------------------------
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

Examples
=====================================================
The following examples demonstrate various approaches for using resources in recipes:

**Use the git mirror**

.. tag resource_scm_use_git_mirror

.. To use the git mirror:

.. code-block:: ruby

   git '/opt/mysources/couch' do
     repository 'git://git.apache.org/couchdb.git'
     revision 'master'
     action :sync
   end

.. end_tag

**Use different branches**

.. tag resource_scm_use_different_branches

To use different branches, depending on the environment of the node:

.. code-block:: ruby

   if node.chef_environment == 'QA'
      branch_name = 'staging'
   else
      branch_name = 'master'
   end

   git '/home/user/deployment' do
      repository 'git@github.com:gitsite/deployment.git'
      revision branch_name
      action :sync
      user 'user'
      group 'test'
   end

where the ``branch_name`` variable is set to ``staging`` or ``master``, depending on the environment of the node. Once this is determined, the ``branch_name`` variable is used to set the revision for the repository. If the ``git status`` command is used after running the example above, it will return the branch name as ``deploy``, as this is the default value. Run Chef Infra Client in debug mode to verify that the correct branches are being checked out:

.. code-block:: bash

   $ sudo chef-client -l debug

.. end_tag

**Install an application from git using bash**

.. tag resource_scm_use_bash_and_ruby_build

The following example shows how Bash can be used to install a plug-in for rbenv named ``ruby-build``, which is located in git version source control. First, the application is synchronized, and then Bash changes its working directory to the location in which ``ruby-build`` is located, and then runs a command.

.. code-block:: ruby

   git "#{Chef::Config[:file_cache_path]}/ruby-build" do
     repository 'git://github.com/sstephenson/ruby-build.git'
     revision 'master'
     action :sync
   end

   bash 'install_ruby_build' do
     cwd '#{Chef::Config[:file_cache_path]}/ruby-build'
     user 'rbenv'
     group 'rbenv'
     code <<-EOH
       ./install.sh
       EOH
     environment 'PREFIX' => '/usr/local'
  end

To read more about ``ruby-build``, see here: https://github.com/sstephenson/ruby-build.

.. end_tag

**Upgrade packages from git**

.. tag resource_scm_upgrade_packages

The following example uses the **git** resource to upgrade packages:

.. code-block:: ruby

   # the following code sample comes from the ``source`` recipe
   # in the ``libvpx-cookbook`` cookbook:
   # https://github.com/enmasse-entertainment/libvpx-cookbook

   git "#{Chef::Config[:file_cache_path]}/libvpx" do
     repository node[:libvpx][:git_repository]
     revision node[:libvpx][:git_revision]
     action :sync
     notifies :run, 'bash[compile_libvpx]', :immediately
   end

.. end_tag

**Pass in environment variables**

.. tag resource_scm_git_environment_variables

.. To pass in environment variables:

.. code-block:: ruby

   git '/opt/mysources/couch' do
     repository 'git://git.apache.org/couchdb.git'
     revision 'master'
     environment 'VAR' => 'whatever'
     action :sync
   end

.. end_tag
