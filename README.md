# Ansible Role for Sentry

Ansible role for installing a standalone sentry server with Postgres and Redis optional addons

## Variables

Here is an example variables file

```
sentry:
  role_logging: false
  install_redis: true
  install_postgres: true
  install_repo: true
  configpath: /etc/sentry
  workingdir: /var/www/sentry
  config:
    users: {}
    general:
      SENTRY_SINGLE_ORGANIZATION: true
      DEBUG: false
      SENTRY_USE_BIG_INTS: true
      SENTRY_CACHE: 'sentry.cache.redis.RedisCache'
      BROKER_URL: 'redis://localhost:6379'
      SENTRY_RATELIMITER: 'sentry.ratelimits.redis.RedisRateLimiter'
      SENTRY_BUFFER: 'sentry.buffer.redis.RedisBuffer'
      SENTRY_QUOTAS: 'sentry.quotas.redis.RedisQuota'
      SENTRY_TSDB: 'sentry.tsdb.redis.RedisTSDB'
      SENTRY_DIGESTS: 'sentry.digests.backends.redis.RedisBackend'
      SENTRY_WEB_HOST: '0.0.0.0'
      SENTRY_WEB_PORT: 9000
      SENTRY_WEB_OPTIONS: {}
      # How to define additional web options
      #   workers: '3'
      #   protocol: 'uwsgi'
      # SECURE_PROXY_SSL_HEADER: ('HTTP_X_FORWARDED_PROTO', 'https')
      # SESSION_COOKIE_SECURE: true
      # CSRF_COOKIE_SECURE: true
      DATABASES:
        default:
          ENGINE: sentry.db.postgres
          NAME: sentry
          USER: postgres
          PASSWORD: postgres
          HOST: 127.0.0.1
          PORT: 5432
          AUTOCOMMIT: true
          ATOMIC_REQUESTS:
      CACHES:
        default:
          BACKEND: 'django.core.cache.backends.memcached.MemcachedCache'
          LOCATION:
            - 127.0.0.1:11211
    filestore:
      filestore.backend: 'filestore'
      filestore.options:
        location: '/tmp/sentry-files'
    mail:
      mail.backend: 'dummy'
      mail.host: ''
      mail.port: 25
      mail.username: ''
      mail.password: ''
      mail.use-tls: false
      mail.from: 'root@localhost'
      mail.enable-replies: false
      mail.reply-hostname: ''
      mail.mailgun-api-key: ''
    system:
      system.secret-key: "key"
      redisclusters:
        default:
          hosts:
            0:
              host: 127.0.0.1
              port: 6379

```

Anything under `sentry.config.general` will be written to `sentry.conf.py`. All other values will be written to `config.yml`. For more information see [https://docs.sentry.io/server/config/](https://docs.sentry.io/server/config/)

## Dependencies

This role has the following optional dependencies:

```
dependencies:
  - role: geerlingguy.repo-epel
  - role: geerlingguy.redis
  - role: geerlingguy.postgresql
```

This can be disabled by setting the following in variables:

```
sentry:
  install_redis: true
  install_postgres: true
  install_repo: true
```

**Note: By disabling these you will need to configure installation of Redis, Postgres and EPL Repos yourself! This role assumes you want a working Sentry installation with
everything on a single host. Disable these options if your data layer is elsewhere**

See the roles documentation for how to configure them to your needs:

* [https://github.com/geerlingguy/ansible-role-postgresql](https://github.com/geerlingguy/ansible-role-postgresql)
* [https://github.com/geerlingguy/ansible-role-repo-epel](https://github.com/geerlingguy/ansible-role-repo-epel)
* [https://github.com/geerlingguy/ansible-role-redis](https://github.com/geerlingguy/ansible-role-redis)

## Example Task with role

```
- name: Sentry | Install Sentry
  hosts: sentry
  roles:
    - edr.sentry
```
