---
template: post
title: Error Tracking/Reporting with Sentry
slug: error-tracking-with-sentry
draft: false
date: 'Sat Apr 13 2019 22:12:23 GMT+0600'
description: >-
  At one point in development, we all need an error reporting tool. Sentry is an
  amazing Error Reporting/Tracking tool. I am a Django developer. In this blog I
  will show you how you can configure sentry with Django.
category: DevOps
tags:
  - Sentry
  - Django
---
At one point in development, we all need an error reporting tool. Sentry is an amazing Error Reporting/Tracking tool. I am a Django developer. In this blog I will show you how you can configure sentry with Django.

As it is a pretty advanced thing, I assume you can setup django project and have basic knowledge about django. So, first of all, we need to install Raven.

```
pip install raven --upgrade
```

Then you need to create a project in [Sentry](https://sentry.io/welcome/). Then in your settings.py file which is in your project directory, put your sentry credentials here like this. 

```Python
# settings.py

RAVEN_CONFIG = {
    'dsn':'https://<public_key>:<secret_key>@sentry.io/<project_id>  
}
```

Three information is needed. Public Key, Secret Key and Project ID. Though It's not best practice to put these credentials in settings.py directly, for now you can put it here. In another blog I will discuss about how you should serve all your credentials for project. 

Kind of done, now you can see all 500 errors of your project in event section of Sentry. But if you want more. Like if you want to send all your log to sentry then you need put sentry in LOGGING Settings

```Python
# settings.py

LOGGING = {
    'version': 1,You can change also change other 
    'disable_existing_loggers': True,
    'formatters': {
        'verbose': {
            'format': '%(levelname)s %(asctime)s %(module)s '
                      '%(process)d %(thread)d %(message)s'
        },
    },
    'handlers': {
        'sentry': {
            'level': 'DEBUG',  # To capture more than ERROR, change to WARNING, INFO, etc.
            'class': 'raven.contrib.django.raven_compat.handlers.SentryHandler',
            'tags': {'custom-tag': 'x'},
        },
        'console': {
            'level': 'DEBUG',
            'class': 'logging.StreamHandler',
            'formatter': 'verbose'
        }
    },
    'loggers': {
        'root': {
            'level': 'WARNING',
            'handlers': ['sentry'],
        },
        'django.db.backends': {
            'level': 'ERROR',
            'handlers': ['sentry'],
            'propagate': False,
        },
        'raven': {
            'level': 'DEBUG',
            'handlers': ['sentry'],
            'propagate': False,
        },
        'sentry.errors': {
            'level': 'DEBUG',
            'handlers': ['console'],
            'propagate': False,
        },
    },
}
```

So you are using sentry as a handler. You can change it's level to ERROR, WARNING & INFO as you want. 

Next thing, if you want to 404 errors to monitor. Then put a middleware in Middleware list

```
'raven.contrib.django.raven_compat.middleware.Sentry404CatchMiddleware'
```

That's it. Done!
