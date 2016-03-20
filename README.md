# Hive file for Plaid API

Check out https://beekeeper.readthedocs.org/en/latest/ for details on how to use Beekeeper

## Notes

Update "root" to production endpoint

Ultimately, hive file should be served at https://plaid.com/api/hive.json

Works only with Plaid Link (https://plaid.com/docs/link/)

'''
from beekeeper import API

Plaid = API.from_hive_file('plaid.json', client_id="test_id", secret="test_secret", host="tartan")

'''