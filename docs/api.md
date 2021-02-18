# API

** The API page is still a working in progress and will be updated in the next release version.**

AISY Framework provides a simple API that allows the user to access results from the database.

### Starting API

```python
from api.api import API
from commons.sca_database import *

# set analysis id (this can be retrieved from web application)
analysis_id = 1
# start database
db_location = "my_db_location"
db_name = "my_db.sqlite"
db = ScaDatabase("{}/{}".format(db_location, db_name))
# start api
api = API(db, analysis_id)
```

### Key Ranks (or Guessing Entropy)

```python
key_ranks = api.get_all_key_ranks()

import matplotlib.pyplot as plt

for key_rank in key_ranks:
    plt.plot(key_rank['values'])
plt.xlabel("Traces")
plt.ylabel("Key Rank")
plt.show()
```

### Success Rates

```python
success_rates = api.get_all_success_rates()

import matplotlib.pyplot as plt

for success_rate in success_rates:
    plt.plot(success_rate['values'])
plt.xlabel("Traces")
plt.ylabel("Success Rate")
plt.show()
```

### Metrics

```python
metrics = api.get_metric_names()
print(metrics)
```
The printed list of metrics should be similar to:
['accuracy', 'loss', 'val_accuracy', 'val_loss']
