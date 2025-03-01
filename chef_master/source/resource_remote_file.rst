=====================================================
remote_file resource
=====================================================
`[edit on GitHub] <https://github.com/chef/chef-web-docs/blob/master/chef_master/source/resource_remote_file.rst>`__

.. tag resource_remote_file_summary

Use the **remote_file** resource to transfer a file from a remote location using file specificity. This resource is similar to the **file** resource.

.. end_tag

.. note:: Fetching files from the ``files/`` directory in a cookbook should be done with the **cookbook_file** resource.

Changed in 12.4 to support Microsoft Windows UNC.

Syntax
=====================================================
A **remote_file** resource block manages files by using files that exist remotely. For example, to write the home page for an Apache website:

.. code-block:: ruby

   remote_file '/var/www/customers/public_html/index.html' do
     source 'http://somesite.com/index.html'
     owner 'web_admin'
     group 'web_admin'
     mode '0755'
     action :create
   end

where

* ``'/var/www/customers/public_html/index.html'`` is path to the file to be created
* ``'http://somesite.com/index.html'`` specifies the location of the remote file, the file is downloaded from there
* ``owner``, ``group``, and ``mode`` define the permissions

The full syntax for all of the properties that are available to the **remote_file** resource is:

.. code-block:: ruby

   remote_file 'name' do
     atomic_update              true, false
     authentication             # default value: remote
     backup                     Integer, false # default value: 5
     checksum                   String
     content                    String, nil
     diff                       String, nil
     force_unlink               true, false # default value: false
     ftp_active_mode            true, false # default value: false
     group                      String, Integer
     headers                    Hash
     inherits                   true, false
     manage_symlink_source      true, false
     mode                       String, Integer
     notifies                   # see description
     owner                      String, Integer
     path                       String # defaults to 'name' if not specified
     rights                     Hash
     source                     String, Array
     subscribes                 # see description
     use_conditional_get        true, false
     verify                     String, Block
     remote_domain              String
     remote_password            String
     remote_user                String
     show_progress              true, false # default value: false
     use_etag                   true, false # default value: true
     use_last_modified          true, false # default value: true
     verifications              Array
     action                     Symbol # defaults to :create if not specified
   end

where:

* ``remote_file`` is the resource.
* ``name`` is the name given to the resource block.
* ``action`` identifies which steps Chef Infra Client will take to bring the node into the desired state.
* ``atomic_update``, ``authentication``, ``backup``, ``checksum``, ``content``, ``diff``, ``force_unlink``, ``ftp_active_mode``, ``group``, ``headers``, ``manage_symlink_source``, ``mode``, ``owner``, ``path``, ``remote_domain``, ``remote_password``, ``remote_user``, ``show_progress``, ``use_etag``, ``use_last_modified``, and ``verifications`` are the properties available to this resource.

Actions
=====================================================

The remote_file resource has the following actions:

``:create``
   Default. Create a file. If a file already exists (but does not match), update that file to match.

``:create_if_missing``
   Create a file only if the file does not exist. When the file exists, nothing happens.

``:delete``
   Delete a file.

``:nothing``
   .. tag resources_common_actions_nothing

   This resource block does not act unless notified by another resource to take action. Once notified, this resource block either runs immediately or is queued up to run at the end of a Chef Infra Client run.

   .. end_tag

``:touch``
   Touch a file. This updates the access (atime) and file modification (mtime) times for a file. (This action may be used with this resource, but is typically only used with the **file** resource.)

Properties
=====================================================

The remote_file resource has the following properties:

``atomic_update``
   **Ruby Type:** true, false

   Perform atomic file updates on a per-resource basis. Set to ``true`` for atomic file updates. Set to ``false`` for non-atomic file updates. This setting overrides ``file_atomic_update``, which is a global setting found in the client.rb file.

``backup``
   **Ruby Type:** Integer, false | **Default Value:** ``5``

   The number of backups to be kept in ``/var/chef/backup`` (for UNIX- and Linux-based platforms) or ``C:/chef/backup`` (for the Microsoft Windows platform). Set to ``false`` to prevent backups from being kept.

``checksum``
   **Ruby Type:** String

   Optional, see ``use_conditional_get``. The SHA-256 checksum of the file. Use to prevent a file from being re-downloaded. When the local file matches the checksum, Chef Infra Client does not download it.

``force_unlink``
   **Ruby Type:** true, false | **Default Value:** ``false``

   How Chef Infra Client handles certain situations when the target file turns out not to be a file. For example, when a target file is actually a symlink. Set to ``true`` for Chef Infra Client delete the non-file target and replace it with the specified file. Set to ``false`` for Chef Infra Client to raise an error.

``ftp_active_mode``
   **Ruby Type:** true, false | **Default Value:** ``false``

   Whether Chef Infra Client uses active or passive FTP. Set to ``true`` to use active FTP.

``group``
   **Ruby Type:** Integer, String

   A string or ID that identifies the group owner by group name, including fully qualified group names such as ``domain\group`` or ``group@domain``. If this value is not specified, existing groups remain unchanged and new group assignments use the default ``POSIX`` group (if available).

``headers``
   **Ruby Type:** Hash

   A Hash of custom headers. For example:

   .. code-block:: ruby

      headers({ "Cookie" => "user=grantmc; pass=p@ssw0rd!" })

   or:

   .. code-block:: ruby

      headers({ "Referer" => "#{header}" })

   or:

   .. code-block:: ruby

      headers( "Authorization"=>"Basic #{ Base64.encode64("#{username}:#{password}").gsub("\n", "") }" )

``ignore_failure``
   **Ruby Type:** true, false | **Default Value:** ``false``

   Continue running a recipe if a resource fails for any reason.

``inherits``
   **Ruby Type:** true, false | **Default Value:** ``true``

   Microsoft Windows only. Whether a file inherits rights from its parent directory.

``manage_symlink_source``
   **Ruby Type:** true, false | **Default Value:** ``true`` (with warning)

   Change the behavior of the file resource if it is pointed at a symlink. When this value is set to ``true``, the Chef client will manage the symlink's permissions or will replace the symlink with a normal file if the resource has content. When this value is set to ``false``, Chef will follow the symlink and will manage the permissions and content of the symlink's target file.

   The default behavior is ``true`` but emits a warning that the default value will be changed to ``false`` in a future version; setting this explicitly to ``true`` or ``false`` suppresses this warning.

``mode``
   **Ruby Type:** Integer, String

   A quoted 3-5 character string that defines the octal mode. For example: ``'755'``, ``'0755'``, or ``00755``. If ``mode`` is not specified and if the file already exists, the existing mode on the file is used. If ``mode`` is not specified, the file does not exist, and the ``:create`` action is specified, Chef Infra Client assumes a mask value of ``'0777'`` and then applies the umask for the system on which the file is to be created to the ``mask`` value. For example, if the umask on a system is ``'022'``, Chef Infra Client uses the default value of ``'0755'``.

   The behavior is different depending on the platform.

   UNIX- and Linux-based systems: A quoted 3-5 character string that defines the octal mode that is passed to chmod. For example: ``'755'``, ``'0755'``, or ``00755``. If the value is specified as a quoted string, it works exactly as if the ``chmod`` command was passed. If the value is specified as an integer, prepend a zero (``0``) to the value to ensure that it is interpreted as an octal number. For example, to assign read, write, and execute rights for all users, use ``'0777'`` or ``'777'``; for the same rights, plus the sticky bit, use ``01777`` or ``'1777'``.

   Microsoft Windows: A quoted 3-5 character string that defines the octal mode that is translated into rights for Microsoft Windows security. For example: ``'755'``, ``'0755'``, or ``00755``. Values up to ``'0777'`` are allowed (no sticky bits) and mean the same in Microsoft Windows as they do in UNIX, where ``4`` equals ``GENERIC_READ``, ``2`` equals ``GENERIC_WRITE``, and ``1`` equals ``GENERIC_EXECUTE``. This property cannot be used to set ``:full_control``. This property has no effect if not specified, but when it and ``rights`` are both specified, the effects are cumulative.

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

``owner``
   **Ruby Type:** Integer, String

   A string or ID that identifies the group owner by user name, including fully qualified user names such as ``domain\user`` or ``user@domain``. If this value is not specified, existing owners remain unchanged and new owner assignments use the current user (when necessary).

``path``
   **Ruby Type:** String

   The full path to the file, including the file name and its extension. Default value: the ``name`` of the resource block. See "Syntax" section above for more information.

``remote_user``
   **Ruby Type:** String

   **Windows only** The name of a user with access to the remote file specified by the ``source`` property. The user name may optionally be specified with a domain, such as: ``domain\user`` or ``user@my.dns.domain.com`` via Universal Principal Name (UPN) format. The domain may also be set using the ``remote_domain`` property. Note that this property is ignored if ``source`` is not a UNC path. If this property is specified, the ``remote_password`` property is required.

   *New in Chef Client 13.4.*

``remote_password``
   **Ruby Type:** String

   **Windows only** The password of the user specified by the ``remote_user`` property. This property is required if `remote_user` is specified and may only be specified if ``remote_user`` is specified. The ``sensitive`` property for this resource will automatically be set to ``true`` if ``remote_password`` is specified.

   *New in Chef Client 13.4.*

``remote_domain``
   **Ruby Type:** String

   **Windows only** The domain of the user specified by the ``remote_user`` property. By default the resource will authenticate against the domain of the remote system, or as a local account if the remote system is not joined to a domain. If the remote system is not part of a domain, it is necessary to authenticate as a local user on the remote system by setting the domain to ``.``, for example: ``remote_domain "."``. The domain may also be specified as part of the ``remote_user`` property.

   *New in Chef Client 13.4.*

``retries``
   **Ruby Type:** Integer | **Default Value:** ``0``

   The number of attempts to catch exceptions and retry the resource.

``retry_delay``
   **Ruby Type:** Integer | **Default Value:** ``2``

   The retry delay (in seconds).

``rights``
   **Ruby Type:** Integer, String

   Microsoft Windows only. The permissions for users and groups in a Microsoft Windows environment. For example: ``rights <permissions>, <principal>, <options>`` where ``<permissions>`` specifies the rights granted to the principal, ``<principal>`` is the group or user name, and ``<options>`` is a Hash with one (or more) advanced rights options.

``source``
   **Ruby Type:** String, Array

   Required. The location of the source file. The location of the source file may be HTTP (``http://``), FTP (``ftp://``), SFTP (``sftp://``), local (``file:///``), or UNC (``\\host\share\file.tar.gz``).

   There are many ways to define the location of a source file. By using a path:

   .. code-block:: ruby

      source 'http://couchdb.apache.org/img/sketch.png'

   By using FTP:

   .. code-block:: ruby

      source 'ftp://remote_host/path/to/img/sketch.png'

   By using SFTP:

   .. code-block:: ruby

      source 'sftp://username:password@remote_host:22/path/to/img/sketch.png'

   By using a local path:

   .. code-block:: ruby

      source 'file:///path/to/img/sketch.png'

   By using a Microsoft Windows UNC:

   .. code-block:: ruby

      source '\\\\path\\to\\img\\sketch.png'

   By using a node attribute:

   .. code-block:: ruby

      source node['nginx']['foo123']['url']

   By using attributes to define paths:

   .. code-block:: ruby

      source "#{node['python']['url']}/#{version}/Python-#{version}.tar.bz2"

   By defining multiple paths for multiple locations:

   .. code-block:: ruby

      source 'http://seapower/spring.png', 'http://seapower/has_sprung.png'

   By defining those same multiple paths as an array:

   .. code-block:: ruby

      source ['http://seapower/spring.png', 'http://seapower/has_sprung.png']

   When multiple paths are specified, Chef Infra Client will attempt to download the files in the order listed, stopping after the first successful download.

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

``use_conditional_get``
   **Ruby Type:** true, false | **Default Value:** ``true``

   Enable conditional HTTP requests by using a conditional ``GET`` (with the If-Modified-Since header) or an opaque identifier (ETag). To use If-Modified-Since headers, ``use_last_modified`` must also be set to ``true``. To use ETag headers, ``use_etag`` must also be set to ``true``.

``use_etag``
   **Ruby Type:** true, false | **Default Value:** ``true``

   Enable ETag headers. Set to ``false`` to disable ETag headers. To use this setting, ``use_conditional_get`` must also be set to ``true``.

``use_last_modified``
   **Ruby Type:** true, false | **Default Value:** ``true``

   Enable If-Modified-Since headers. Set to ``false`` to disable If-Modified-Since headers. To use this setting, ``use_conditional_get`` must also be set to ``true``.

``show_progess``
   **Ruby Type:** true, false | **Default Value:** ``false``

   Displays the progress of the file download. Set to ``true`` to enable this feature.

``verify``
   **Ruby Type:** String, Block

   A block or a string that returns ``true`` or ``false``. A string, when ``true`` is executed as a system command.

   A block is arbitrary Ruby defined within the resource block by using the ``verify`` property. When a block is ``true``, Chef Infra Client will continue to update the file as appropriate.

   For example, this should return ``true``:

   .. code-block:: ruby

      remote_file '/tmp/baz' do
        verify { 1 == 1 }
      end

   This should return ``true``:

   .. code-block:: ruby

      remote_file '/etc/nginx.conf' do
        verify 'nginx -t -c %{path}'
      end

   .. warning:: For releases of Chef Infra Client prior to 12.5 (chef-client 12.4 and earlier) the correct syntax is:

      .. code-block:: ruby

         remote_file '/etc/nginx.conf' do
           verify 'nginx -t -c %{file}'
         end

      See GitHub issues https://github.com/chef/chef/issues/3232 and https://github.com/chef/chef/pull/3693 for more information about these differences.

   This should return ``true``:

   .. code-block:: ruby

      remote_file '/tmp/bar' do
        verify { 1 == 1}
      end

   And this should return ``true``:

   .. code-block:: ruby

      remote_file '/tmp/foo' do
        verify do |path|
          true
        end
      end

   Whereas, this should return ``false``:

   .. code-block:: ruby

      remote_file '/tmp/turtle' do
        verify '/usr/bin/false'
      end

   If a string or a block return ``false``, the Chef Infra Client run will stop and an error is returned.

Atomic File Updates
-----------------------------------------------------
.. tag resources_common_atomic_update

Atomic updates are used with **file**-based resources to help ensure that file updates can be made when updating a binary or if disk space runs out.

Atomic updates are enabled by default. They can be managed globally using the ``file_atomic_update`` setting in the client.rb file. They can be managed on a per-resource basis using the ``atomic_update`` property that is available with the **cookbook_file**, **file**, **remote_file**, and **template** resources.

.. note:: On certain platforms, and after a file has been moved into place, Chef Infra Client may modify file permissions to support features specific to those platforms. On platforms with SELinux enabled, Chef Infra Client will fix up the security contexts after a file has been moved into the correct location by running the ``restorecon`` command. On the Microsoft Windows platform, Chef Infra Client will create files so that ACL inheritance works as expected.

.. end_tag

Windows File Security
-----------------------------------------------------
.. tag resources_common_windows_security

To support Microsoft Windows security, the **template**, **file**, **remote_file**, **cookbook_file**, **directory**, and **remote_directory** resources support the use of inheritance and access control lists (ACLs) within recipes.

.. end_tag

**Access Control Lists (ACLs)**

.. tag resources_common_windows_security_acl

The ``rights`` property can be used in a recipe to manage access control lists (ACLs), which allow permissions to be given to multiple users and groups. Use the ``rights`` property can be used as many times as necessary; Chef Infra Client will apply them to the file or directory as required. The syntax for the ``rights`` property is as follows:

.. code-block:: ruby

   rights permission, principal, option_type => value

where

``permission``
   Use to specify which rights are granted to the ``principal``. The possible values are: ``:read``, ``:write``, ``read_execute``, ``:modify``, and ``:full_control``.

   These permissions are cumulative. If ``:write`` is specified, then it includes ``:read``. If ``:full_control`` is specified, then it includes both ``:write`` and ``:read``.

   (For those who know the Microsoft Windows API: ``:read`` corresponds to ``GENERIC_READ``; ``:write`` corresponds to ``GENERIC_WRITE``; ``:read_execute`` corresponds to ``GENERIC_READ`` and ``GENERIC_EXECUTE``; ``:modify`` corresponds to ``GENERIC_WRITE``, ``GENERIC_READ``, ``GENERIC_EXECUTE``, and ``DELETE``; ``:full_control`` corresponds to ``GENERIC_ALL``, which allows a user to change the owner and other metadata about a file.)

``principal``
   Use to specify a group or user name. This is identical to what is entered in the login box for Microsoft Windows, such as ``user_name``, ``domain\user_name``, or ``user_name@fully_qualified_domain_name``. Chef Infra Client does not need to know if a principal is a user or a group.

``option_type``
   A hash that contains advanced rights options. For example, the rights to a directory that only applies to the first level of children might look something like: ``rights :write, 'domain\group_name', :one_level_deep => true``. Possible option types:

   .. list-table::
      :widths: 60 420
      :header-rows: 1

      * - Option Type
        - Description
      * - ``:applies_to_children``
        - Specify how permissions are applied to children. Possible values: ``true`` to inherit both child directories and files;  ``false`` to not inherit any child directories or files; ``:containers_only`` to inherit only child directories (and not files); ``:objects_only`` to recursively inherit files (and not child directories).
      * - ``:applies_to_self``
        - Indicates whether a permission is applied to the parent directory. Possible values: ``true`` to apply to the parent directory or file and its children; ``false`` to not apply only to child directories and files.
      * - ``:one_level_deep``
        - Indicates the depth to which permissions will be applied. Possible values: ``true`` to apply only to the first level of children; ``false`` to apply to all children.

For example:

.. code-block:: ruby

   resource 'x.txt' do
     rights :read, 'Everyone'
     rights :write, 'domain\group'
     rights :full_control, 'group_name_or_user_name'
     rights :full_control, 'user_name', :applies_to_children => true
   end

or:

.. code-block:: ruby

    rights :read, ['Administrators','Everyone']
    rights :full_control, 'Users', :applies_to_children => true
    rights :write, 'Sally', :applies_to_children => :containers_only, :applies_to_self => false, :one_level_deep => true

Some other important things to know when using the ``rights`` attribute:

* Only inherited rights remain. All existing explicit rights on the object are removed and replaced.
* If rights are not specified, nothing will be changed. Chef Infra Client does not clear out the rights on a file or directory if rights are not specified.
* Changing inherited rights can be expensive. Microsoft Windows will propagate rights to all children recursively due to inheritance. This is a normal aspect of Microsoft Windows, so consider the frequency with which this type of action is necessary and take steps to control this type of action if performance is the primary consideration.

Use the ``deny_rights`` property to deny specific rights to specific users. The ordering is independent of using the ``rights`` property. For example, it doesn't matter if rights are granted to everyone is placed before or after ``deny_rights :read, ['Julian', 'Lewis']``, both Julian and Lewis will be unable to read the document. For example:

.. code-block:: ruby

   resource 'x.txt' do
     rights :read, 'Everyone'
     rights :write, 'domain\group'
     rights :full_control, 'group_name_or_user_name'
     rights :full_control, 'user_name', :applies_to_children => true
     deny_rights :read, ['Julian', 'Lewis']
   end

or:

.. code-block:: ruby

   deny_rights :full_control, ['Sally']

.. end_tag

**Inheritance**

.. tag resources_common_windows_security_inherits

By default, a file or directory inherits rights from its parent directory. Most of the time this is the preferred behavior, but sometimes it may be necessary to take steps to more specifically control rights. The ``inherits`` property can be used to specifically tell Chef Infra Client to apply (or not apply) inherited rights from its parent directory.

For example, the following example specifies the rights for a directory:

.. code-block:: ruby

   directory 'C:\mordor' do
     rights :read, 'MORDOR\Minions'
     rights :full_control, 'MORDOR\Sauron'
   end

and then the following example specifies how to use inheritance to deny access to the child directory:

.. code-block:: ruby

   directory 'C:\mordor\mount_doom' do
     rights :full_control, 'MORDOR\Sauron'
     inherits false # Sauron is the only person who should have any sort of access
   end

If the ``deny_rights`` permission were to be used instead, something could slip through unless all users and groups were denied.

Another example also shows how to specify rights for a directory:

.. code-block:: ruby

   directory 'C:\mordor' do
     rights :read, 'MORDOR\Minions'
     rights :full_control, 'MORDOR\Sauron'
     rights :write, 'SHIRE\Frodo' # Who put that there I didn't put that there
   end

but then not use the ``inherits`` property to deny those rights on a child directory:

.. code-block:: ruby

   directory 'C:\mordor\mount_doom' do
     deny_rights :read, 'MORDOR\Minions' # Oops, not specific enough
   end

Because the ``inherits`` property is not specified, Chef Infra Client will default it to ``true``, which will ensure that security settings for existing files remain unchanged.

.. end_tag

Prevent Re-downloads
-----------------------------------------------------
To prevent Chef Infra Client from re-downloading files that are already present on a node, use one of the following attributes in a recipe: ``use_conditional_get`` (default) or ``checksum``.

* The ``use_conditional_get`` attribute is the default behavior of Chef Infra Client. If the remote file is located on a server that supports ETag and/or If-Modified-Since headers, Chef Infra Client will use a conditional ``GET`` to determine if the file has been updated. If the file has been updated, Chef Infra Client will re-download the file.

* The ``checksum`` attribute will ask Chef Infra Client to compare the checksum for the local file to the one at the remote location. If they match, Chef Infra Client will not re-download the file. Using a local checksum for comparison requires that the local checksum be the correct checksum.

The desired approach just depends on the desired workflow. For example, if a node requires a new file every day, using the checksum approach would require that the local checksum be updated and/or verified every day as well, in order to ensure that the local checksum was the correct one. Using a conditional ``GET`` in this scenario will greatly simplify the management required to ensure files are being updated accurately.

Access a remote UNC path on Windows
-----------------------------------------------------
The ``remote_file`` resource on Windows supports accessing files from a remote SMB/CIFS share. The file name should be specified in the source property as a UNC path e.g. ``\\myserver\myshare\mydirectory\myfile.txt``. This
allows access to the file at that path location even if the Chef Infra Client process identity does not have permission to access the file. Credentials for authenticating to the remote system can be specified using the ``remote_user``, ``remote_domain``, and ``remote_password`` properties when the user that Chef Infra Client is running does not have access to the remote file. See the "Properties" section for more details on these options.

**Note**: This is primarily for accessing remote files when the user that Chef Infra Client is running as does not have sufficient access, and alternative credentials need to be specified. If the user already has access, the credentials do not need to be specified.
In a case where the local system and remote system are in the same domain, the ``remote_user`` and ``remote_password`` properties often do not need to be specified, as the user may already have access to the remote file share.

Examples:

**Access a file from a different domain account:**

.. code-block:: ruby

   remote_file "E:/domain_test.txt"  do
     source  "\\\\myserver\\myshare\\mydirectory\\myfile.txt"
     remote_domain "domain"
     remote_user "username"
     remote_password "password"
   end

OR

.. code-block:: ruby

   remote_file "E:/domain_test.txt"  do
     source  "\\\\myserver\\myshare\\mydirectory\\myfile.txt"
     remote_user "domain\\username"
     remote_password "password"
   end

**Access a file using a local account on the remote machine:**

.. code-block:: ruby

   remote_file "E:/domain_test.txt"  do
     source  "\\\\myserver\\myshare\\mydirectory\\myfile.txt"
     remote_domain "."
     remote_user "username"
     remote_password "password"
   end

OR

.. code-block:: ruby

   remote_file "E:/domain_test.txt"  do
     source  "\\\\myserver\\myshare\\mydirectory\\myfile.txt"
     remote_user ".\\username"
     remote_password "password"
   end

Examples
=====================================================
The following examples demonstrate various approaches for using resources in recipes:

**Transfer a file from a URL**

.. tag resource_remote_file_transfer_from_url

.. To transfer a file from a URL:

.. code-block:: ruby

   remote_file '/tmp/testfile' do
     source 'http://www.example.com/tempfiles/testfile'
     mode '0755'
     checksum '3a7dac00b1' # A SHA256 (or portion thereof) of the file.
   end

.. end_tag

**Transfer a file only when the source has changed**

.. tag resource_remote_file_transfer_remote_source_changes

.. To transfer a file only if the remote source has changed (using the |resource http request| resource):

.. The "Transfer a file only when the source has changed" example is deprecated in Chef Client 11.6

.. code-block:: ruby

   remote_file '/tmp/couch.png' do
     source 'http://couchdb.apache.org/img/sketch.png'
     action :nothing
   end

   http_request 'HEAD http://couchdb.apache.org/img/sketch.png' do
     message ''
     url 'http://couchdb.apache.org/img/sketch.png'
     action :head
     if ::File.exist?('/tmp/couch.png')
       headers 'If-Modified-Since' => File.mtime('/tmp/couch.png').httpdate
     end
     notifies :create, 'remote_file[/tmp/couch.png]', :immediately
   end

.. end_tag

**Install a file from a remote location using bash**

.. tag resource_remote_file_install_with_bash

The following is an example of how to install the ``foo123`` module for Nginx. This module adds shell-style functionality to an Nginx configuration file and does the following:

* Declares three variables
* Gets the Nginx file from a remote location
* Installs the file using Bash to the path specified by the ``src_filepath`` variable

.. code-block:: ruby

   # the following code sample is similar to the ``upload_progress_module``
   # recipe in the ``nginx`` cookbook:
   # https://github.com/chef-cookbooks/nginx

   src_filename = "foo123-nginx-module-v#{
     node['nginx']['foo123']['version']
   }.tar.gz"
   src_filepath = "#{Chef::Config['file_cache_path']}/#{src_filename}"
   extract_path = "#{
     Chef::Config['file_cache_path']
     }/nginx_foo123_module/#{
     node['nginx']['foo123']['checksum']
   }"

   remote_file 'src_filepath' do
     source node['nginx']['foo123']['url']
     checksum node['nginx']['foo123']['checksum']
     owner 'root'
     group 'root'
     mode '0755'
   end

   bash 'extract_module' do
     cwd ::File.dirname(src_filepath)
     code <<-EOH
       mkdir -p #{extract_path}
       tar xzf #{src_filename} -C #{extract_path}
       mv #{extract_path}/*/* #{extract_path}/
       EOH
     not_if { ::File.exist?(extract_path) }
   end

.. end_tag

**Store certain settings**

.. tag resource_remote_file_store_certain_settings

The following recipe shows how an attributes file can be used to store certain settings. An attributes file is located in the ``attributes/`` directory in the same cookbook as the recipe which calls the attributes file. In this example, the attributes file specifies certain settings for Python that are then used across all nodes against which this recipe will run.

Python packages have versions, installation directories, URLs, and checksum files. An attributes file that exists to support this type of recipe would include settings like the following:

.. code-block:: ruby

   default['python']['version'] = '2.7.1'

   if python['install_method'] == 'package'
     default['python']['prefix_dir'] = '/usr'
   else
     default['python']['prefix_dir'] = '/usr/local'
   end

   default['python']['url'] = 'http://www.python.org/ftp/python'
   default['python']['checksum'] = '80e387...85fd61'

and then the methods in the recipe may refer to these values. A recipe that is used to install Python will need to do the following:

* Identify each package to be installed (implied in this example, not shown)
* Define variables for the package ``version`` and the ``install_path``
* Get the package from a remote location, but only if the package does not already exist on the target system
* Use the **bash** resource to install the package on the node, but only when the package is not already installed

.. code-block:: ruby

   #  the following code sample comes from the ``oc-nginx`` cookbook on |github|: https://github.com/cookbooks/oc-nginx

   version = node['python']['version']
   install_path = "#{node['python']['prefix_dir']}/lib/python#{version.split(/(^\d+\.\d+)/)[1]}"

   remote_file "#{Chef::Config[:file_cache_path]}/Python-#{version}.tar.bz2" do
     source "#{node['python']['url']}/#{version}/Python-#{version}.tar.bz2"
     checksum node['python']['checksum']
     mode '0755'
     not_if { ::File.exist?(install_path) }
   end

   bash 'build-and-install-python' do
     cwd Chef::Config[:file_cache_path]
     code <<-EOF
       tar -jxvf Python-#{version}.tar.bz2
       (cd Python-#{version} && ./configure #{configure_options})
       (cd Python-#{version} && make && make install)
     EOF
     not_if { ::File.exist?(install_path) }
   end

.. end_tag

**Use the platform_family? method**

.. tag resource_remote_file_use_platform_family

The following is an example of using the ``platform_family?`` method in the Recipe DSL to create a variable that can be used with other resources in the same recipe. In this example, ``platform_family?`` is being used to ensure that a specific binary is used for a specific platform before using the **remote_file** resource to download a file from a remote location, and then using the **execute** resource to install that file by running a command.

.. code-block:: ruby

   if platform_family?('rhel')
     pip_binary = '/usr/bin/pip'
   else
     pip_binary = '/usr/local/bin/pip'
   end

   remote_file "#{Chef::Config[:file_cache_path]}/distribute_setup.py" do
     source 'http://python-distribute.org/distribute_setup.py'
     mode '0755'
     not_if { File.exist?(pip_binary) }
   end

   execute 'install-pip' do
     cwd Chef::Config[:file_cache_path]
     command <<-EOF
       # command for installing Python goes here
       EOF
     not_if { File.exist?(pip_binary) }
   end

where a command for installing Python might look something like:

.. code-block:: ruby

    #{node['python']['binary']} distribute_setup.py
    #{::File.dirname(pip_binary)}/easy_install pip

.. end_tag

**Specify local Windows file path as a valid URI**

.. tag resource_remote_file_local_windows_path

When specifying a local Microsoft Windows file path as a valid file URI, an additional forward slash (``/``) is required. For example:

.. code-block:: ruby

   remote_file 'file:///c:/path/to/file' do
     ...       # other attributes
   end

.. end_tag
