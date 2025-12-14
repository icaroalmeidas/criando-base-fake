# building-fake-data
Learning how to use pyspark and Faker packages

#### Install required packages
```!pip install pyspark Faker ```

#### Creating the data
```python
# importing necessary libraries
from pyspark.sql import SparkSession
from faker import Faker
import random

# create Spark session
spark = SparkSession.builder.appName("bank_balances").getOrCreate()
fake = Faker()

# function to generate fake data
def generate_data(num_records):
    data = []
    for _ in range(num_records):
        name = fake.name()
        balance = round(random.uniform(1000, 100000), 2)
        account_number = fake.unique.bban()
        data.append((name, account_number, balance))  # ✅ Ordem correta
    return data

# generate data and create DataFrame
dados = generate_data(1000)  # ✅ Nome correto
columns = ["name", "account_number", "balance"]
df = spark.createDataFrame(dados, schema=columns)
df.show(10)

# sort data by balance in descending order and show top 10
df_sort = df.orderBy(df.balance.desc()).limit(10)
df_sort.show()

# search for accounts with balance over a certain value
value = 5000
df_over_value = df.filter(df.balance > value)
df_over_value.count()