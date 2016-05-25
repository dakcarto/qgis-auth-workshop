.. _auth_method_plugins:

Authentication Method Plugins
=============================

Method plugins are where all authentication configurations are 'expanded' into
connection objects passed by the Auth Manager. Upon instantiation, the Manager
loads all auth method plugins into a registry. When a data provider (or whatever
calling object that is integrated with the auth system) calls the Manager to do
generic expansion routines, the Manager figures out which method plugin is
approriate to use, relative to the method associated with the auth config *and*
whether the method supports the expected expansion. Whether a method plugin
supports a particular type of expansion, e.g. ``QNetworkRequest`` or
``QgsDataSourceURI`` or both, is up to the plugin author.

.. _method_initial_design:

Initial Design
--------------

The plugin design of ``QgsAuthMethodRegistry`` and ``QgsAuthMethod`` was
essentially copied from that of ``QgsProviderRegistry`` and ``QgsDataProvider``.
This means the plugins can only be loaded at QGIS launch. However, it does allow
for custom authentication method plugins to be 'dropped' into existing similar
QGIS builds, and available upon relaunch. There is currently no factory support
for creating and registering new ``QgsAuthMethod`` plugins via C++, and no
Python bindings.

The latter two limitations were merely due to *lack of time* to implement. There
are also security concerns with allowing Python to register (or replace)
authentication methods.

All authentication method plugins inherit the base class of::

  src/core/auth/qgsauthmethod.h

From there the author can override and implement (or not) any desired
expansions. Any new expansions should be added as virtual functions in
``qgsauthmethod.h``.

.. _integrate-qtnetwork:

Integrate with QtNetwork
------------------------

There are the following virtual function expansions::

   # most common approach for HTTP URL or header expansion
   QgsAuthManager::updateNetworkRequest( QNetworkRequest &request, ... )

   # not generally needed
   QgsAuthManager::updateNetworkReply( QNetworkReply *reply, ... )

*Review function API comments*

.. _integrate-qgsdatasourceuri:

Integration with QgsDataSourceURI
---------------------------------

There is the following virtual function expansion::

   QgsAuthManager::updateDataSourceUriItems( QStringList &connectionItems, ... )

*Review function API comments*


Other integrations
------------------

