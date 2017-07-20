Flask-Multipass
===============

.. image:: https://raw.githubusercontent.com/indico/flask-multipass/master/artwork/flask-multipass.png

.. image:: https://readthedocs.org/projects/flask-multipass/badge/?version=latest
    :target: https://flask-multipass.readthedocs.org/
.. image:: https://github.com/indico/flask-multipass/workflows/Tests/badge.svg
    :target: https://github.com/indico/flask-multipass/actions

Flask-Multipass provides Flask with a user authentication/identity
system which can use different backends (such as local users,
LDAP and OAuth) simultaneously.

It was developed at CERN and is currently used in production by `Indico <https://github.com/indico/indico>`_.

There are bult-in authentication and identity providers for:

 * `Static (hardcoded) credentials <https://github.com/indico/flask-multipass/blob/master/flask_multipass/providers/static.py>`_
 * `Local (SQLAlchemy DB) authentication <https://github.com/indico/flask-multipass/blob/master/flask_multipass/providers/sqlalchemy.py>`_
 * `Authlib (OAuth/OIDC) <https://github.com/indico/flask-multipass/blob/master/flask_multipass/providers/authlib.py>`_
 * `SAML <https://github.com/indico/flask-multipass/blob/master/flask_multipass/providers/saml.py>`_
 * `Shibboleth <https://github.com/indico/flask-multipass/blob/master/flask_multipass/providers/shibboleth.py>`_
 * `LDAP <https://github.com/indico/flask-multipass/blob/master/flask_multipass/providers/ldap/providers.py>`_
 * `CAS <https://github.com/Tom-Hubrecht/flask-multipass/blob/master/flask_multipass/providers/cas.py>`_

Those can be used simultaneously and interchangeably (e.g. authenticate with OAuth and search users with LDAP).

Documentation is available at https://flask-multipass.readthedocs.org

Dependencies
============

* <https://github.com/discogs/python-cas-client>_

```sh
git clone https://github.com/discogs/python-cas-client
pip install python-cas-client/
```

Installation
============

```sh
git clone https://github.com/discogs/python-cas-client
pip install python-cas-client/
git clone https://github.com/xaionaro/flask-multipass-cas
pip install flask-multipass-cas/
```

Example of CAS+LDAP
===================

`~indico/etc/indico.conf`

```
[…]

# Auth

_acdir_ldap_config = {
    'uri': 'ldap://ldap.example.com:389',
    'bind_dn': 'login',
    'bind_password': 'PaSsWoRd',
    'timeout': 30,
    'verify_cert': False,
    'starttls': True,
    'page_size': 1000,

    'uid': 'mailNickname',
    'user_base': 'OU=Users,OU=Department,DC=Example,DC=Com',
    'user_filter': '(objectClass=person)',

    'member_of_attr': '',
    'ad_group_style': False,
}

AuthProviders = {
    'mephi-cas': {
        'type': 'cas',
        'title': 'cas.example.com',
        'cas_url_base': 'https://cas.example.com',
    }
}

IdentityProviders = {
    'mephi-cas': {
        'type': 'cas',
        'mapping': {
            'first_name': 'first_name',
            'last_name': 'last_name',
            'email': 'email'
        }
    },
    'acdir-ldap': {
        'type': 'ldap',
        'ldap': _acdir_ldap_config,
        'mapping': {
            'first_name': 'givenName',
            'last_name': 'sn',
            'email': 'mail',
            'affiliation': 'department'
        }
    }
}
```
