# Plaid + beekeeper

[beekeeper](https://github.com/haikuginger/beekeeper) is a Python library designed around dynamically generating a RESTful client interface based on a minimal JSON file, called a "hive".  Visit the [beehive github repo](https://github.com/haikuginger/beekeeper) and the [documentation](https://beekeeper.readthedocs.org/en/latest/) for more details.

[Plaid](https://plaid.com/) is the API for banking data.  Visit the [Plaid documentation](https://www.plaid.com/docs/) for more details.

## Getting started

First, gain access to the Plaid API by following the [instructions here](https://www.plaid.com/docs/#gaining-access).

Next, install beekeeper using pip:

```
pip install beekeeper
```

To interact with the Plaid API, simply do the following:

```python
from beekeeper import API

plaid = API.from_hive_file('plaid.json', client_id="test_id", secret="test_secret", env="tartan")

```

This will call the development environment ('tartan').  To call the production environment, use `env="api"` or omit the `env` parameter altogether (it will default to `"api"`).

Calling the API object will print the layout of the API with descriptions:

```
>>> plaid
Plaid()
|   The API for banking data
|
|---Tokens[access_token]
|   |   manage public_tokens and access_tokens
|   |
|   |---upgrade(upgrade_to, access_token[, options])
|   |       add functionality for any of Plaid's products to an existing access_token
|   |
|   |---exchange(public_token)
|   |       exchange a public_token returned by Plaid Link for an access_token
|   |
|   |---delete(access_token, any_active_product)
|   |       delete an access_token from your account; supply any active product as a variable
|
...

```

## Usage

```python
from beekeeper import API

plaid = API.from_hive_file('plaid.json', client_id="test_id", secret="test_secret")

public_token = "some-public-token"

access_token = plaid.Tokens.exchange(public_token)["access_token"]

auth_response 			= plaid.Accounts[access_token].auth()
connect_response 		= plaid.Accounts[access_token].connect()
balance_response 		= plaid.Accounts[access_token].balance()
info_response 			= plaid.Accounts[access_token].info()
income_response 		= plaid.Accounts[access_token].income()
risk_response 			= plaid.Accounts[access_token].risk()

all_plaid_categories	= plaid.Details.categories()
category_by_id			= plaid.Details['22018000'].categories()

all_plaid_institutions	= plaid.Details.institutions()
institution_by_id		= plaid.Details['55fa106813c81cf103e9e093'].institutions()
```

Note, you'll need to use `plaid.Tokens[{ACCESS_TOKEN}].upgrade({PRODUCT_NAME})` to gain access to the products beyond the one you chose during the Link process, except for `balance` which can be called on any active access_token.

To delete an access_token from your account, simply call `plaid.Tokens[{ACCESS_TOKEN}].delete({ANY_ACTIVE_PRODUCT})`, where `{ANY_ACTIVE_PRODUCT}` is any of `'auth'`, `'connect'`, `'info'`, `'income'`, or `'risk'` which you have previously activated.


## Plaid Link

This Plaid hive assumes that you are using [Plaid Link](https://plaid.com/docs/link/) to onboard users.


## Other Notes

Ultimately, a hive file could be served at https://plaid.com/api/hive.json, in which case instantiating the hive would be as simple as:

```python
from beekeeper import API

plaid = API.from_domain('plaid.com', client_id="test_id", secret="test_secret")
```
