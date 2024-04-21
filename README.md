TABLE\_REDIS(5) - File Formats Manual

# NAME

**table\_redis** - format description for smtpd redis tables

# DESCRIPTION

This manual page documents the file format of redis tables used by the
smtpd(8)
mail daemon.

The format described here applies to tables as defined in
smtpd.conf(5).

# REDIS TABLE

A Redis table allows the storing of usernames, passwords, aliases, and domains
in a redis server.

The table is used by
smtpd(8)
when authenticating a user, when user information such as user-id and/or
home directory is required for a delivery, when a domain lookup may be required,
and/or when looking for an alias.

A Redis table consists of one Redis Databases with one or more keys.

If the table is used for authentication, the password should be
encrypted using the
crypt(3)
function.
Such passwords can be generated using the
encrypt(1)
utility or
smtpctl(8)
encrypt command.

# REDIS TABLE CONFIG FILE

**master**

> This is the IP of the master redis server.
> To connect via an unix socket use unix:/path/to/sock
> The default is 127.0.0.1

**master\_port**

> This is the port used to connect to the master redis server.
> The default is 6379

**slave**

> This is the IP of the slave redis server, if any.
> To connect via an unix socket use unix:/path/to/sock

**slave\_port**

> This is the port used to connect to the slave redis server if any.

**database**

> The database number to use.
> The default is 0.

**password**

> The password to use to authenticate to the redis server if any.

**query\_domain**

> This is used to provide a query for a domain query call.
> All the '%s' are replaced
> with the appropriate data, in this case it would be the right hand side of
> the SMTP address.
> This expects one string to be returned with a matching domain name.

**query\_userinfo**

> This is used to provide a query for looking up user information.
> All the '%s' are replaced with the appropriate data, in this case it
> would be the left hand side of the SMTP address.
> This expects three fields to be returned an int containing a UID, an int
> containing a GID
> and a string containing the home directory for the user.

**query\_credentials**

> This is used to provide a query for looking up credentials.
> All the '%s' are replaced
> with the appropriate data, in this case it would be the left hand side of
> the SMTP address.
> the query expects that there are two strings returned one with a
> user name one with a password in encrypted format.

**query\_alias**

> This is used to provide a query to look up aliases.
> All the '%s' are replaced with the appropriate data, in this case it would
> be the left hand side of the SMTP address.
> This expects one string to be returned with the user name the alias resolves to.
> If the query returns an array, all the data will be concatenated into one
> string with ',' as a separator

**query\_mailaddr**

> This is used to provide a query to check if a mail address exists.
> All the '%s' are replaced with the appropriate data, in this case it would
> be the SMTP address.
> This expects an integer as a reply, 0 = false and 1 = true

# EXAMPLES

Due to the nature of redis, multiple schemas can be used.
Those provided here a known to work.

**domain**

> Using a set for the domains:

> > \# redis-cli sadd domains example.net

> in the redis table configuration file:

> > query\_domain SISMEMBER domains %s

**userinfo**

> Hash works well for users

> > \# redis-cli HSET user:foo uid 1001

> > \# redis-cli HSET user:foo gid 1001

> > \# redis-cli HSET user:foo maildir /mail/foo

> in the redis table configuration file :

> > query\_userinfo HMGET user:%s uid gid maildir

**credentials**

> We can extend the hash for our user to put credential in it

> > \# redis-cli HSET user:foo login foo

> > \# redis-cli HSET user:foo passwd encrypted\_password

> in the redis table configuration file:

> > query\_credentials HMGET user:%s login passwd

**alias**

> Using redis sorted list:

> > \# redis-cli LPUSH aliases:foo@example.net foo

> > \# redis-cli LPUSH aliases:bar@example.net foo

> in the redis table configuration file:

> > query\_alias LRANGE aliases:%s 0 -1

**mailaddr**

> Using a set for the addresses:

> > \# redis-cli sadd mailaddr foo@example.net

> in the redis table configuration file:

> > query\_mailaddr SISMEMBER mailaddr %s

# SEE ALSO

encrypt(1),
smtpd.conf(5),
smtpctl(8),
smtpd(8)

# HISTORY

The first version of
**table\_redis**
was written in 2015.
It was converted to the stdio protocol in 2024.

# AUTHORS

**table\_redis**
was initially written by
Emmanuel Vadot &lt;[elbarto@bocal.org](mailto:elbarto@bocal.org)&gt;.
The conversion to the stdio table protocol was done by
Omar Polo &lt;[op@openbsd.org](mailto:op@openbsd.org)&gt;.

Nixpkgs - April 21, 2024
