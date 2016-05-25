.. _auth_overview:

Authentication System Technical Overview
========================================

.. figure:: img/auth-system-tech-overview.png
   :align: center

**QgsAuthManager** [``src/core/auth/qgsauthmanager.h``]

* Singleton that oversees all master password and auth database functions and
  marshalling of auth methods
* Instantiates in ``QgsApplication::initQgis()`` and cleans up in
  ``QgsApplication::exitQgis()``

**QgsAuthCrypto** [``src/core/auth/qgsauthcrypto.h``]

* Simple interface for hashing/verifying master password and encrypt/decrypt
  operations on auth configs with master password.
* Currently uses `Qt Cryptographic Architecture` (QCA), though originally
  designed for `CryptoPP`_, which was found to be *way too finicky* to `build
  on Windows`_, especially for non-devs.

.. _Qt Cryptographic Architecture: http://delta.affinix.com/qca/
.. _CryptoPP: http://www.cryptopp.com/
.. _build on Windows: http://www.codeproject.com/Articles/16388/Compiling-and-Integrating-Crypto-into-the-Microsof

**QgsAuthMethodConfig** [``src/core/auth/qgsauthmethodconfig.h``]

* Class representing auth method configs
* Has public properties that can generally be queried *without* requiring the
  user to input the master password
* Has sensitive properties that become semi-public once the master password
  is set/verified and the config has been retrieved and decrypted from the
  auth database by the auth manager
* Has sensitive properties that can be set and then encrypted and stored in
  the auth database by the auth manager

**QgsAuthMethod** [``src/core/auth/qgsauthmethod.h``] and **QgsAuthMethodEdit**

* Class and edit widget that comprise an auth config method plugin
* Each method accepts marshaled calls from the auth manager to update
  authentication-specific objects when needed, e.g. ``QNetworkRequest`` and
  ``DataSourceURI``, during resource connections
* Each method has an in-memory cache of authentication objects, generated
  during the processing of an auth config, that are stored upon first
  access/load of the config. Subsequent calls use the cached resource, e.g.
  generated SSL certificate, key and CA chain objects.

**QgsAuthMethodRegistry**
[``src/core/auth/qgsauthmethodregistry.h``] and **QgsAuthMethodMetadata**

* Singleton plugin registry modeled after ``QgsProviderRegistry``
* Loads plugins with ``lib*authmethod.(so|dll)`` name pattern

**QgsAuthConfigEdit** [``src/gui/auth/qgsauthconfigedit.h``]

* An embeddable or standalone widget for creating/editing auth configs
  directly in the auth database
* Depending upon method, does lightweight validation, e.g. cert issue dates

.. figure:: img/auth-configwidget_create.png
   :align: center

   Standalone config creation

.. figure:: img/auth-config-create_pkcs12-paths.png
   :align: center

   Standalone with existing config in edit mode


Auth-specific Contents of SRC
---------------------------------

::

   ├── auth (method plugins)
   │   ├── basic
   │   │   ├── qgsauthbasicedit.cpp
   │   │   ├── qgsauthbasicedit.h
   │   │   ├── qgsauthbasicedit.ui
   │   │   ├── qgsauthbasicmethod.cpp
   │   │   └── qgsauthbasicmethod.h
   │   ├── identcert
   │   │   ├── qgsauthidentcertedit.cpp
   │   │   ├── qgsauthidentcertedit.h
   │   │   ├── qgsauthidentcertedit.ui
   │   │   ├── qgsauthidentcertmethod.cpp
   │   │   └── qgsauthidentcertmethod.h
   │   ├── pkipaths
   │   │   ├── qgsauthpkipathsedit.cpp
   │   │   ├── qgsauthpkipathsedit.h
   │   │   ├── qgsauthpkipathsedit.ui
   │   │   ├── qgsauthpkipathsmethod.cpp
   │   │   └── qgsauthpkipathsmethod.h
   │   └── pkipkcs12
   │       ├── qgsauthpkcs12edit.cpp
   │       ├── qgsauthpkcs12edit.h
   │       ├── qgsauthpkcs12edit.ui
   │       ├── qgsauthpkcs12method.cpp
   │       └── qgsauthpkcs12method.h

::

   ├── core
   │   ├── auth
   │   │   ├── qgsauthcertutils.cpp
   │   │   ├── qgsauthcertutils.h
   │   │   ├── qgsauthconfig.cpp
   │   │   ├── qgsauthconfig.h
   │   │   ├── qgsauthcrypto.cpp
   │   │   ├── qgsauthcrypto.h
   │   │   ├── qgsauthmanager.cpp
   │   │   ├── qgsauthmanager.h
   │   │   ├── qgsauthmethod.h
   │   │   ├── qgsauthmethodmetadata.cpp
   │   │   ├── qgsauthmethodmetadata.h
   │   │   ├── qgsauthmethodregistry.cpp
   │   │   └── qgsauthmethodregistry.h

::

   ├── gui
   │   ├── auth
   │   │   ├── qgsauthauthoritieseditor.cpp
   │   │   ├── qgsauthauthoritieseditor.h
   │   │   ├── qgsauthcertificateinfo.cpp
   │   │   ├── qgsauthcertificateinfo.h
   │   │   ├── qgsauthcertificatemanager.cpp
   │   │   ├── qgsauthcertificatemanager.h
   │   │   ├── qgsauthcerttrustpolicycombobox.cpp
   │   │   ├── qgsauthcerttrustpolicycombobox.h
   │   │   ├── qgsauthconfigedit.cpp
   │   │   ├── qgsauthconfigedit.h
   │   │   ├── qgsauthconfigeditor.cpp
   │   │   ├── qgsauthconfigeditor.h
   │   │   ├── qgsauthconfigidedit.cpp
   │   │   ├── qgsauthconfigidedit.h
   │   │   ├── qgsauthconfigselect.cpp
   │   │   ├── qgsauthconfigselect.h
   │   │   ├── qgsautheditorwidgets.cpp
   │   │   ├── qgsautheditorwidgets.h
   │   │   ├── qgsauthguiutils.cpp
   │   │   ├── qgsauthguiutils.h
   │   │   ├── qgsauthidentitieseditor.cpp
   │   │   ├── qgsauthidentitieseditor.h
   │   │   ├── qgsauthimportcertdialog.cpp
   │   │   ├── qgsauthimportcertdialog.h
   │   │   ├── qgsauthimportidentitydialog.cpp
   │   │   ├── qgsauthimportidentitydialog.h
   │   │   ├── qgsauthmasterpassresetdialog.cpp
   │   │   ├── qgsauthmasterpassresetdialog.h
   │   │   ├── qgsauthmethodedit.h
   │   │   ├── qgsauthserverseditor.cpp
   │   │   ├── qgsauthserverseditor.h
   │   │   ├── qgsauthsslconfigwidget.cpp
   │   │   ├── qgsauthsslconfigwidget.h
   │   │   ├── qgsauthsslerrorsdialog.cpp
   │   │   ├── qgsauthsslerrorsdialog.h
   │   │   ├── qgsauthsslimportdialog.cpp
   │   │   ├── qgsauthsslimportdialog.h
   │   │   ├── qgsauthtrustedcasdialog.cpp
   │   │   └── qgsauthtrustedcasdialog.h

::

   └── ui
       ├── auth
       │   ├── qgsauthauthoritieseditor.ui
       │   ├── qgsauthcertificateinfo.ui
       │   ├── qgsauthcertificatemanager.ui
       │   ├── qgsauthconfigedit.ui
       │   ├── qgsauthconfigeditor.ui
       │   ├── qgsauthconfigidedit.ui
       │   ├── qgsauthconfigselect.ui
       │   ├── qgsauthconfiguriedit.ui
       │   ├── qgsautheditorwidgets.ui
       │   ├── qgsauthidentitieseditor.ui
       │   ├── qgsauthimportcertdialog.ui
       │   ├── qgsauthimportidentitydialog.ui
       │   ├── qgsauthmasterpassresetdialog.ui
       │   ├── qgsauthmethodplugins.ui
       │   ├── qgsauthserverseditor.ui
       │   ├── qgsauthsslconfigwidget.ui
       │   ├── qgsauthsslerrorsdialog.ui
       │   ├── qgsauthsslimportdialog.ui
       │   ├── qgsauthsslimporterrors.ui
       │   └── qgsauthtrustedcasdialog.ui

::

   ├── core
   │   ├── qgscredentials.cpp
   │   ├── qgscredentials.h
   ├── gui
   │   ├── qgscredentialdialog.cpp
   │   ├── qgscredentialdialog.h
   └── ui
       ├── qgscredentialdialog.ui
