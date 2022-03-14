# Adding Custom Tables to the Database

User can also add custom SQLite tables to the analysis. The process is rather simple and requires the following steps:

###### Step 1: Define custom table in ```custom/custom_tables/tables.py```

In the ```custom/custom_tables/tables.py``` file, enter a custom SQLite table structure following the SQLAlchemy package structure. Below,
we provide an example of ```CustomTable``` class with the following columns:

- ```id (Integer):``` primary key id for entry.
- ```value1 (Integer):``` column with an integer value.
- ```value2 (Integer):``` column with an integer value.
- ```value3 (Integer):``` column with an integer value.
- ```datetime (DateTime):``` data and time for the entry.
- ```analysis_id (Integer, ForeignKey):``` id from Analysis table.

Here is the code for the table:

```python
class CustomTable(Base):
    __tablename__ = 'custom_table'
    id = Column(Integer, primary_key=True)
    value1 = Column(Integer)
    value2 = Column(Integer)
    value3 = Column(Integer)
    datetime = Column(DateTime, default=datetime.datetime.utcnow)
    analysis_id = Column(Integer, ForeignKey(Analysis.id))
    analysis = relationship(Analysis)

    def __repr__(self):
        return "<CustomTable(id=%d)>" % self.id
```

The full code for ```custom/custom_tables/tables.py``` will look like this:

```python
import datetime
from sqlalchemy import *
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import relationship
from aisy.sca_tables import Analysis
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

Base = declarative_base()


def base():
    return Base


def start_custom_tables(database_name):
    engine = create_engine('sqlite:///{}'.format(database_name), echo=False)
    base().metadata.create_all(engine)


def start_custom_tables_session(database_name):
    engine = create_engine('sqlite:///{}'.format(database_name), echo=False)
    return sessionmaker(bind=engine)()


class CustomTable(Base):
    __tablename__ = 'custom_table'
    id = Column(Integer, primary_key=True)
    value1 = Column(Integer)
    value2 = Column(Integer)
    value3 = Column(Integer)
    datetime = Column(DateTime, default=datetime.datetime.utcnow)
    analysis_id = Column(Integer, ForeignKey(Analysis.id))
    analysis = relationship(Analysis)

    def __repr__(self):
        return "<CustomTable(id=%d)>" % self.id

```

###### Step 2: Inserting entries to your custom table

First, in your script file (located at ```scripts/```), create the ```Aisy()``` object and set *dataset folder*, *dataset name* and
*database name* and run the analysis as usual (look at the imports):

```python
import aisy_sca
from app import *
from custom.custom_models.neural_networks import *
from custom.custom_tables.tables import *

aisy = aisy_sca.Aisy()
aisy.set_resources_root_folder(resources_root_folder)
aisy.set_database_root_folder(databases_root_folder)
aisy.set_datasets_root_folder(datasets_root_folder)
aisy.set_database_name("database_ascad.sqlite")
aisy.set_dataset(datasets_dict["ascad-variable.h5"])
aisy.set_aes_leakage_model(leakage_model="HW", byte=2)
aisy.set_batch_size(400)
aisy.set_epochs(20)
aisy.set_neural_network(mlp)

aisy.run()
```

Next, start the custom table session:

```python
start_custom_tables(databases_root_folder + "database_ascad.sqlite")
session = start_custom_tables_session(databases_root_folder + "database_ascad.sqlite")
```

Then, insert your entries to your CustomTable as soon as you define the values to be inserted:

```python
new_insert = CustomTable(value1=10, value2=20, value3=30, analysis_id=aisy.settings["analysis_id"])
session.add(new_insert)
session.commit()
```

A full script example will look like this:

```python
import aisy_sca
from app import *
from custom.custom_models.neural_networks import *
from custom.custom_tables.tables import *

aisy = aisy_sca.Aisy()
aisy.set_resources_root_folder(resources_root_folder)
aisy.set_database_root_folder(databases_root_folder)
aisy.set_datasets_root_folder(datasets_root_folder)
aisy.set_database_name("database_ascad.sqlite")
aisy.set_dataset(datasets_dict["ascad-variable.h5"])
aisy.set_aes_leakage_model(leakage_model="HW", byte=2)
aisy.set_batch_size(400)
aisy.set_epochs(20)
aisy.set_neural_network(mlp)

aisy.run()

start_custom_tables(databases_root_folder + "database_ascad.sqlite")
session = start_custom_tables_session(databases_root_folder + "database_ascad.sqlite")

new_insert = CustomTable(value1=10, value2=20, value3=30, analysis_id=aisy.get_analysis_id())
session.add(new_insert)
session.commit()
``` 