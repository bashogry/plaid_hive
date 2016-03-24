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

token = plaid.Tokens['test_chase']
token.auth()

```

This will call the development environment ("tartan").  To call the production environment, use `env="api"` or omit the `env` parameter altogether (it will default to `"api"`).

Calling the API object will print the layout of the API with descriptions:

```
>>> plaid

Plaid()
|   The API for banking data
|
|---Tokens[access_token]
|   |   each end-user + institution pair is identified with a unique access_token
|   |
|   |---info(access_token)
|   |       account holder information on file with the financial institution
|   |
|   |---upgrade(upgrade_to, access_token[, options])
|   |       add functionality for any of Plaid's products to an existing access_token
|   |
|   |---risk(access_token)
|   |       an assessment of abnormal behavior across a user's accounts
|   |
|   |---exchange(public_token)
|   |       exchange a public_token returned by Plaid Link for an access_token
|   |
|   |---auth(access_token)
|   |       account and routing numbers for a given access_token
|   |
|   |---connect(access_token[, options])
|   |       a list of accounts and transactions for a given access_token
|   |
|   |---income(access_token)
|   |       estimated annual income and details for a user
|   |
|   |---balance(access_token)
|   |       real-time account balances for a given access_token
|   |
|   |---delete(access_token, any_active_product)
|   |       delete an access_token from your account; supply any active product as a variable
|
|---Institutions[id]
|   |   detailed information on currently supported financial institutions
|   |
|   |---list()
|   |       lists each institution
|   |
|   |---get(id)
|   |       gets a single institution
|
|---Categories[id]
|   |   detailed information on all Plaid transaction categories
|   |
|   |---list()
|   |       lists each category
|   |
|   |---get(id)
|   |       gets a single category

```

## Usage

Accessing the Plaid API using beekeeper is as easy as instantiating the beekeeper API objects and then calling the built-in functions on it:

```python
from beekeeper import API

plaid = API.from_hive_file('plaid.json', client_id="test_id", secret="test_secret", env="tartan")


# exchange a public token (supplied by Plaid Link) for an access token

token_string = plaid.Tokens.exchange('some-public-token')
token = plaid.Tokens[token_string]


# upgrade an existing access_token to an additional product

response = token.upgrade('connect')


# access any of Plaid's products, after they have been activated either during the Link process
# or by calling upgrade()

auth_response 			= token.auth()
connect_response 		= token.connect()
balance_response 		= token.balance()
info_response 			= token.info()
income_response 		= token.income()
risk_response 			= token.risk()

# passing in optional arguments works as follows (`'test_chase'` is test access_token; for a full 
# list of options, see the Connect documentation at https://plaid.com/docs/#retrieve-transactions

response 				= token.connect(options='{"pending":"True"}')


# delete an access_token from your account
# you can pass delete() the name of any of the access_token's active products

response = token.delete('auth')
```

Alternatively, you could assign a token object to a variable without an access_token and then use it to interact with the API for multiple access_tokens and endpoints:

```python
token 					= plaid.Tokens
response_for_token		= token.connect(access_token="test_chase")
response_for_another	= token.connect(access_token="test_amex")
```

Access supplementary information about the Plaid API using the Categories and Institutions objects:

```python
categories 				= plaid.Categories
all_categories			= categories.list()
single_category			= categories.get(id='22018000')		# or, categories['22018000'].get()

institutions 			= plaid.Institutions
all_plaid_institutions	= institutions.list()
institution_by_id		= institutions.get('55fa106813c81cf103e9e093')

```


## Plaid Link

This Plaid hive assumes that you are using [Plaid Link](https://plaid.com/docs/link/) to onboard users.


## Other Notes

Ultimately, a hive file could be served at https://plaid.com/api/hive.json, in which case instantiating the hive would be as simple as:

```python
from beekeeper import API

plaid = API.from_domain('plaid.com', client_id="test_id", secret="test_secret")
```
