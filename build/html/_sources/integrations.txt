.. _auth_integrations:

Authentication System Integrations
==================================

Integrations are considered points of access to the auth system from some point
in the QGIS code base *outside* of the auth system itself. Integrations should
be coded agnostic to the authentication method used. They should be coded
relative to the _type_ of authentication configuration _expansion_ needed, i.e.
how the ``authcfg`` ID will be replace by the secured credentials from the
authentication database.

Data provider integrations
--------------------------

Currently, OWS and Postgres data providers support using the authentication
system. Further integrations can be accomplished for other data providers, using
similar techniques, as well.

.. _integration-ows:

**OWS integrations**

For WMS, WCS and WFS(-T), integration is accomplished using::

   QgsAuthManager::updateNetworkRequest( ... )

.. _integration-postgres:

**Postgres integration**

For Postgres, integration is accomplished using::

   QgsAuthManager::updateDataSourceUriItems( ... )

Plugin Manager integration
--------------------------

Plugin Manager integration is accomplished using::

   QgsAuthManager::updateNetworkRequest( ... )
