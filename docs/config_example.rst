
.. _config_example:

Configuration example
=====================

This configuration can be added to your Flask configuration file that you use when initializing flask application. However, you can configure Multipass also directly through the application object, for example:

.. code-block:: python

    app.config['MULTIPASS_LOGIN_URLS'] = {'/my_login/', '/my_login/<provider>'}

Here you can see an example configuration for an application using both external and local providers.

``'test_auth_provider'`` is a dummy example of a local authentication provider, it's linked to the ``'test_identity_provider'`` as specified in ``MULTIPASS_PROVIDER_MAP``. You can read more about the configuration of local providers here: :ref:`local_providers`

``'github'``, ``'shib'``, ``'saml'`` and ``'my-ldap'`` are examples of external providers. More on configuration of external providers:  :ref:`external_providers`

.. code-block:: python

    _github_oauth_config = {
        'client_id': '',  # put your client id here
        'client_secret': '',  # put your client secret here
        'authorize_url': 'https://github.com/login/oauth/authorize',
        'access_token_url': 'https://github.com/login/oauth/access_token',
        'api_base_url': 'https://api.github.com',
        'userinfo_endpoint': '/user',
    }


    _my_ldap_config = {
        'uri': 'ldaps://ldap.example.com:636',
        'bind_dn': 'uid=admin,DC=example,DC=com',
        'bind_password': 'p455w0rd',
        'timeout': 30,
        'verify_cert': True,
        # optional: if not present, uses certifi's CA bundle (if installed)
        'cert_file': 'path/to/server/cert',
        'starttls': False,
        'page_size': 1000,

        'uid': 'uid',
        'user_base': 'OU=Users,DC=example,DC=com',
        'user_filter': '(objectCategory=person)',

        'gid': 'cn',
        'group_base': 'OU=Organizational Units,DC=example,DC=com',
        'group_filter': '(objectCategory=groupOfNames)',
        'member_of_attr': 'memberOf',
        'ad_group_style': False,
    }

    _my_saml_config = {
        # If enabled, errors are more verbose and received attributes are
        # printed to stdout
        # 'debug': True,
        'sp': {
            # this needs to match what you use when registering the SAML SP
            'entityId': 'multipass-saml-test',
            # these bindings usually do not need to be set manually; the URLs
            # are auto-generated
            # 'assertionConsumerService': {'binding': 'urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST'},
            # 'singleLogoutService': {'binding': 'urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect'},
            # depending on your security config you may need to generate a
            # certificate and private key.
            # you can use https://www.samltool.com/self_signed_certs.php or
            # use `openssl` for it (which is more secure as it ensures the
            # key never leaves your machine)
            'x509cert': '',
            'privateKey': '',
            # persistent name ids are the default, but you can override it
            # with a custom format
            # 'NameIDFormat': 'urn:oasis:names:tc:SAML:2.0:nameid-format:persistent'
        },
        'idp': {
            # This metadata is provided by your SAML IdP. You can omit (or
            # leave empty) the whole 'idp' section in case you need SP
            # metadata to register your app and get the IdP metadata from
            # https://yourapp.example.com/multipass/saml/{auth-provider-name}/metadata
            # and then fill in the IdP metadata afterwards.
            'entityId': 'https://idp.example.com',
            'x509cert': '',
            'singleSignOnService': {
                'url': 'https://idp.example.com/saml',
                'binding': 'urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect'
            },
            # If you do not want to perform a SAML logout when logging out
            # from your application, you can omit this section
            'singleLogoutService': {
                'url': 'https://idp.example.com/saml',
                'binding': 'urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect'
            }
        },
        # These advanced settings allow you to tune the SAML security options.
        # Please see the documentation on https://github.com/onelogin/python3-saml
        # for details on how they behave. Note that by requiring signatures,
        # you usually need to set a cert and key on your SP config, but for
        # testing you may want to disable all signing-related options.
        'security': {
            'nameIdEncrypted': False,
            'authnRequestsSigned': True,
            'logoutRequestSigned': True,
            'logoutResponseSigned': True,
            'signMetadata': True,
            'wantMessagesSigned': True,
            'wantAssertionsSigned': True,
            'wantNameId' : True,
            'wantNameIdEncrypted': False,
            'wantAssertionsEncrypted': False,
            'allowSingleLabelDomains': False,
            'signatureAlgorithm': 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256',
            'digestAlgorithm': 'http://www.w3.org/2001/04/xmlenc#sha256'
        },
        # You can set contact information which will be included in the SP
        # metadata; it is up to the IdP if/how this information is used:
        # 'contactPerson': {
        #     'technical': {
        #         'givenName': 'technical_name',
        #         'emailAddress': 'technical@example.com'
        #     },
        #     'support': {
        #         'givenName': 'support_name',
        #         'emailAddress': 'support@example.com'
        #     }
        # },
        # 'organization': {
        #     'en-US': {
        #         'name': 'sp_test',
        #         'displayname': 'SP test',
        #         'url': 'http://sp.example.com'
        #     }
        # }
    }

    MULTIPASS_AUTH_PROVIDERS = {
        'test_auth_provider': {
            'type': 'static',
            'title': 'Insecure dummy auth',
            'identities': {
                'Pig': 'pig123',
                'Bunny': 'bunny123'
            }
        },
        'github': {
            'type': 'authlib',
            'title': 'GitHub',
            'authlib_args': _github_oauth_config
        },
        'my-ldap': {
            'type': 'ldap',
            'title': 'My Organization LDAP',
            'ldap': _my_ldap_config,
        },
        'my-cas': {
            'type': 'cas'
            'title': 'Some University CAS'
            'cas_url_base': 'https://cas.example.com',
        },
        'shib': {
            'type': 'shibboleth',
            'title': 'Shibboleth SSO',
            'callback_uri': '/shibboleth/sso',
            'logout_uri': 'https://sso.example.com/logout'
        },
        'saml': {
            'type': 'saml',
            'title': 'SAML SSO',
            'saml_config': _my_saml_config,
            # If your IdP is ADFS you may need to enable this. For details, see
            # https://github.com/onelogin/python-saml/pull/144
            # 'lowercase_urlencoding': True
        },
    }

    MULTIPASS_IDENTITY_PROVIDERS = {
        'test_identity_provider': {
            'type': 'static',
            'identities': {
                'Pig': {'email': 'guinea.pig@example.com', 'name': 'Guinea Pig', 'affiliation': 'Pig University'},
                'Bunny': {'email': 'bugs.bunny@example.com', 'name': 'Bugs Bunny', 'affiliation': 'Bunny Inc.'}
            },
            'groups': {
                'Admins': ['Pig'],
                'Everybody': ['Pig', 'Bunny'],
            }
        },
        'github': {
            'type': 'authlib',
            'title': 'GitHub',
            'identifier_field': 'id',
            'mapping': {
                'affiliation': 'company',
                'first_name': 'name'
            }
        },
        'my-ldap': {
            'type': 'ldap',
            'ldap': _my_ldap_config,
            'mapping': {
                'name': 'givenName',
                'email': 'mail',
                'affiliation': 'company'
            }
        },
        'my-cas': {
            'type': 'cas'
        },
        'my_shibboleth': {
            'type': 'shibboleth',
            'mapping': {
                'email': 'ADFS_EMAIL',
                'name': 'ADFS_FIRSTNAME',
                'affiliation': 'ADFS_HOMEINSTITUTE'
            }
        },
        'saml': {
            'type': 'saml',
            'title': 'SAML',
            'mapping': {
                'name': 'DisplayName',
                'email': 'EmailAddress',
                'affiliation': 'HomeInstitute',
            },
            # You can use a different field as the unique identifier.
            # By default the qualified NameID from SAML is used, but in
            # case you want to use something else, any SAML attribute can
            # be used.
            # 'identifier_field': 'Username'
        }
    }

    MULTIPASS_PROVIDER_MAP = {
        'test_auth_provider': 'test_identity_provider',
        'my-ldap': 'my-ldap',
        'my-cas': 'my-cas',
        'shib': 'my_shibboleth',
        # You can also be explicit (only needed for more complex links)
        'github': [
            {
                'identity_provider': 'github'
            }
        ]
    }

    MULTIPASS_LOGIN_FORM_TEMPLATE = 'login_form.html'
    MULTIPASS_LOGIN_SELECTOR_TEMPLATE = 'login_selector.html'
    MULTIPASS_LOGIN_URLS = {'/my_login/', '/my_login/<provider>'}
    MULTIPASS_IDENTITY_INFO_KEYS = ['email', 'name', 'affiliation']
