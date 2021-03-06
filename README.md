# Strato Python API Package

[![Build Status](https://travis-ci.org/dkarchmer/django-rest-framework-client.svg?branch=master)](https://travis-ci.org/dkarchmer/django-rest-framework-client)
[![PyPI version](https://img.shields.io/pypi/v/django-rest-framework-client.svg)](https://pypi.python.org/pypi/django-rest-framework-client) 

A python library for interacting with any Django web server base on django-rest-framework

Package is based on https://github.com/samgiles/slumber, but enhanced to support tokens and other features.

## Features

* Support for tokens. Both
    * django-rest-framework's own tokens: `rest_framework.authentication.TokenAuthentication`
    * JWT tokens: `rest_framework_jwt.authentication.JSONWebTokenAuthentication`

* Support for arguments (e.g. `?name1=val1&name2=val2`)

* Support for custom methods (e.g. ``/ap1/v1/object/custom/`)

## Requirements

restframeworkclient requires the following modules.

    * Python 2.7+ or 3.4+
    * requests

## Installation

```
pip install django-rest-framework-client
```

## Usage Guide

Example

```
from drf_client.connection import Api as RestApi

options = {
    'DOMAIN': 'http://127.0.0.1:8000',
    'API_PREFIX': 'api/v1',
    'TOKEN_TYPE': 'jwt',
    'TOKEN_FORMAT': 'JWT {token}',
    'LOGIN': 'auth/login/',
    'LOGOUT': 'auth/logout/',
}

c = RestApi(options)

ok = c.login(email=email, password=password)
if ok:

    # GET some data
    my_object = c.myresourcename.get()
    for obj in my_object['results']:
        pprint(obj)
        logger.info('------------------------------')

    payload = {
        'data1': 'val1',
        'data2': 'val2',
    }

    resp = c.myresourcename.post(data=payload)

```


## Django Setup

Client assumes by default that all urls should end with a slash (tested with the default
router: `routers.DefaultRouter()`)

Apart from the regular Django and Rest Framework setup, this package currently relies on the following custom
login and logout API functions:

```
class AccountSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ('id', 'email', 'username')


class APILogoutViewSet(APIView):
    permission_classes = (permissions.IsAuthenticated,)

    def post(self, request, format=None):
        logout(request)
        return Response({}, status=status.HTTP_204_NO_CONTENT)


urlpatterns = [
    url(r'^auth/logout/$', APILogoutViewSet.as_view(), name='api-logout'),

```

## Development

To test, run python setup.py test or to run coverage analysis:

```
coverage run --source=iotile_cloud setup.py test
coverage report -m
```

You can also use py.test:

```
py.test
```
