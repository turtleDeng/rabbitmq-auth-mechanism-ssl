# x509 (TLS/SSL) certificate Authentication Mechanism for RabbitMQ

Authenticates the user, obtaining the username from the client's
SSL certificate. The user's password is not checked.

In order to use this mechanism the client must connect over SSL, and
present a client certificate.

The mechanism must also be enabled in RabbitMQ's configuration file -
see [TLS Authentication guide](http://www.rabbitmq.com/authentication.html) for more details, or
in short, ensure that the 'rabbit' section of your configuration
contains:

``` erlang
{auth_mechanisms, ['PLAIN', 'AMQPLAIN', 'EXTERNAL']}
```

to allow this mechanism in addition to the defaults, or:

``` erlang
{auth_mechanisms, ['EXTERNAL']}
```

to allow only this mechanism.

For safety the server must be configured with the SSL option 'verify'
set to 'verify_peer', to ensure that if an SSL client presents a
certificate, it gets verified.

By default this will set the username to an RFC4514-ish string form of
the certificate's subject's Distinguished Name, similar to that
produced by OpenSSL's "-nameopt RFC2253" option.

You can obtain this string form from a certificate with a command like:

```
openssl x509 -in path/to/cert.pem -nameopt RFC2253 -subject -noout
```

or from an existing amqps connection with commands like:

```
rabbitmqctl list_connections peer_cert_subject
```

To use the Common Name instead, ensure that the 'rabbit' section of
your configuration contains:

```
{ssl_cert_login_from, common_name}
```

Note that the authenticated user will then be looked up in the
configured authentication / authorisation backend(s) - this will be
the mnesia-based user database by default, but could include other
backends if so configured.

## Copyright & License

(c) Pivotal Software Inc., 2007-2016.

Released under the same license as RabbitMQ.
