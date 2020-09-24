
# SurveyGizmo

A Python Wrapper for [SurveyGizmo](https://apihelp.surveygizmo.com/help)'s mostly restful API service.

[![Build Status](https://travis-ci.org/ITNG/SurveyGizmo.svg?branch=master)](https://travis-ci.org/ITNG/SurveyGizmo)
[![codecov](https://codecov.io/gh/ITNG/SurveyGizmo/branch/master/graph/badge.svg)](https://codecov.io/gh/ITNG/SurveyGizmo)


## Requirements

- **python**: 2.7, 3.4, 3.5, 3.6


## Installation

```sh
$ pip install SurveyGizmo
```


## Usage

Start by instantiating the SurveyGizmo object and providing some configuration parameters. Options can also be set through the `config` property.

```python
from surveygizmo import SurveyGizmo

client = SurveyGizmo(
    api_version='v5'

    # example token from SurveyGizmo docs
    api_token = "E4F796932C2743FEBF150B421BE15EB9"
    api_token_secret = "A9fGMkJ5pJF1k"
)

# Update client options through the config property.
client.config.api_token = "E4F796932C2743FEBF150B421BE15EB9"
client.config.api_token_secret = "A9fGMkJ5pJF1k"
```

Calls to the API are by object type, then by function. For example,

```python
client.api.survey.list()
client.api.survey.get('39501')
client.api.survey.copy('39501', 'New title boop')
client.api.surveyresponse.list('39501')
```

Most API calls have required parameters, which are similarly required by the function signatures.

```python
>>> client.api.survey.create('Survey Title')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: create() missing 1 required positional argument: 'type'
```

Optional parameters may be provided as keyword arguments, although not all parameters are valid python identifiers.

```python
client.api.survey.create('Survey Title 1', 'survey', team='456789')
```

```python
>>> client.api.emailmessage.create('123456', '100000', body[html]='...')
  File "<stdin>", line 1
SyntaxError: keyword can't be an expression
```

Instead, these arguments may be passed as a kwargs dictionary.

```python
client.api.emailmessage.create('123456', '100000', **{'body[html]': '...'})
```

Most resources have the list, get, create, update, copy, and delete actions. If SurveyGizmo's REST API does not implement an action, the client will raise a `NotImplementedError`.


## Authentication

Token based authentication is the only currently supported authentication method. `user:pass` and `user:md5` were [deprecated](https://community.surveygizmo.com/questions/question/final-notice-surveygizmo-api-authentication-changes/) on May 31, 2016. Oauth support is not currently a goal, but pull requests are welcome.

#### token
```python
client.config.api_token = 'E4F796932C2743FEBF150B421BE15EB9'
client.config.api_token_secret = 'A9fGMkJ5pJF1k'
```

## API Filtering

SurveyGizmo's API supports filtering for `list` calls on surveys, survey campaigns, and survey responses. For more information, reference the SurveyGizmo [filter documentation](https://apihelp.surveygizmo.com/help/article/link/filters).

The filtering implementation contains no real magic and is simply a convenience wrapper around the awkward filtering semantics. There is no enforcement of which resources can perform filtering or what types of properties are being filtered for a resource.

To filter, simply

```python
filtered = client.api.surveyresponse.filter('datesubmitted', '<=', '2013-07-01')
filtered.list('39501')
```

Filtering is also chainable.

```python
client.api.survey.filter('createdon', '<=', '2013-04-01').list()
...

client.api.surveyresponse \
    .filter('datesubmitted', '<=', '2013-07-01') \
    .filter('datesubmitted', '>', '2013-06-01') \
    .list('39501')
```


## Config paramaters

* **api_version** - 'v4', 'v5', 'head'. Defaults to 'head'
* **api_token**
* **api_token_secret**
* **response_type** - `None`, `'json'`, `'pson'`, `'xml'`, `'debug'`. By default (using `None`), the API returns a JSON response which is parsed by the client into a python dictionary. Specifying a `response_type` will return an unparsed body of the specified format.
* **requests_kwargs** - Additional arguments passed to `requests.get`. Useful for setting timeouts and otherwise configuring the requests library.
* **prepare_url** - Force the client to return the url after being prepared instead of executing the api call. This is useful in cases where you need to call the api asynchronously. Defaults to 'False'
* **handler52x** - Handler for CloudFlare's 52x errors. Expects a callable (e.g., `surveygizmo.default_52xhandler`). Defaults to 'None'.


## CloudFlare 52x Errors

After SurveyGizmo's move to CloudFlare, it isn't uncommon to see connectivity issues where the service is temporarily unreachable. These errors exist on the 52x range of HTTP status codes. To automatically handle 52x errors, set a callable for `config.handler52x`. A basic handler is provided under `surveygizmo.default_52xhandler`, which simply retries the request every second until a non-52x response is returned.


## API Resources

* [api.account](https://apihelp.surveygizmo.com/help/article/link/account-object)
* [api.accountteams](https://apihelp.surveygizmo.com/help/article/link/accountteams-object)
* [api.accountuser](https://apihelp.surveygizmo.com/help/article/link/accountuser-object)
* [api.contact](https://apihelp.surveygizmo.com/help/article/link/contact-sub-object)
* [api.contactlist](https://apihelp.surveygizmo.com/help/article/link/contactlist-object)
* [api.emailmessage](https://apihelp.surveygizmo.com/help/article/link/emailmessage-sub-object)
* [api.survey](https://apihelp.surveygizmo.com/help/article/link/survey-object)
* [api.surveycampaign](https://apihelp.surveygizmo.com/help/article/link/surveycampaign-sub-object)
* [api.surveyoption](https://apihelp.surveygizmo.com/help/article/link/surveyoption-sub-object)
* [api.surveypage](https://apihelp.surveygizmo.com/help/article/link/surveypage-sub-object)
* [api.surveyquestion](https://apihelp.surveygizmo.com/help/article/link/surveyquestion-sub-object)
* [api.surveyreport](https://apihelp.surveygizmo.com/help/article/link/surveyreport-sub-object)
* [api.surveyresponse](https://apihelp.surveygizmo.com/help/article/link/surveyresponse-sub-object)
* [api.surveystatistic](https://apihelp.surveygizmo.com/help/article/link/surveystatistic-sub-object)


## Changelog

### 1.2.4

- #28 Added support for SurveyContact class

### 1.2.3

- #21 Make the 'base_url' configurable.
- Drop explicit python 3.3 support.

### 1.2.2

- #17 Added basic pagination support. Thanks @WesleyBatista!

### 1.2.1

- Added `_prepare_url` argument to API calls that overrides the configured setting for `prepare_url`.

### 1.2.0

- Reimplmented API import as metaclass.
- Reimplemented filtering, removed race condition.

### 1.1.0

- Added required parameters various API calls (mostly create).

### 1.0.0

1.0.0 is a reimplementation of the entire API. Tests have been added and the package is basically stable.

- Replace all authentication methods with only token based authentication.
- Rewrite API to use class inheritance instead of module function wrapping.
- Remove `preserve_filters` option.
- Rename `add_filter` to just `filter`. Filters are chainable.


### 0.2.0

0.2.0 is a forwards incompatible release, but only minorly so.

Forwards incompatible changes:

- Renamed the 'change' operations to 'update'. This is consistent with SurveyGizmo's API naming.
- Removed the 'keep' kwarg for preserving filters bettween api funcion calls. This is now configured with 'preserve_filters'. Filters are now cleared manually with `api.clear_filters()`
- Removed the undocumented 'url_fetch' kwarg, which prevented api execution and instead returned the prepared url.

Backwards incompatible changes:

- Modified 'api_version' to no longer has any effect on the client. SurveyGizmo provides no way to meaningfully differentiate between API versions, so this checking was unneeded and created code duplication
- Added 'prepare_url' as a replacement for 'url_fetch'. This forces the client to return the url after being prepared instead of executing the api call. This is useful in cases where you need to call the api asynchronously.
- Added 'requests_kwargs'. These are additional arguments passed to `requests.get`. Useful for setting timeouts and otherwise configuring the requests library.
- Added handling for CloudFlare 52x errors


## Release Process

- Update package version in `setup.py`
- Create git tag for version
- Upload release to PyPI
    ```bash
    $ pip install -U setuptools wheel
    $ rm -rf dist/ build/
    $ python setup.py sdist bdist_wheel upload
    ```


## Copyright & License
Copyright &copy; 2013-2018 NC State University. See LICENSE for details.
