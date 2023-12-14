---
id: lpy7brxiajf9spoemc8yxp8
title: machine_learning
desc: ''
updated: 1697932371148
created: 1697932353188
---


## Machine Learning

``` py

spark = SparkSession.builder.appName('SupervisedLearning').getOrCreate()
data = [...]

from pyspark.sql.types import FloatType

schema = StructType([
    StructField('Sqft', IntegerType(), True),
    StructField('Bedrooms', IntegerType(), True),
    StructField('Bathrooms', IntegerType(), True),
    StructField('Price', IntegerType(), True)
])
df = spark.createDataFrame(data, schema)
df.show()

# CLASSIFICATION WITH LOGISTIC REGRESSION ==========================================

from pyspark.ml.feature import VectorAssembler
from pyspark.ml.classification import LogisticRegression
from pyspark.ml import Pipeline

feature_columns = ['Sqft', 'Bedrooms', 'Bathrooms']
assembler = VectorAssembler(inputCols = feature_columns, outputCols = 'features')
df_assembled - assembler.transform(df)

# train test split
train_data, test_data = df_assembled = randomSplit(0.8, 0.2)
lr = LogisticRegression(featuresCol = 'features', labelCol = 'Price')
# creating model
model = lr.fit(train_data)
# predict using model
predictions = model.transform(test_data)
predictions.select('features', 'Price', 'predictions').show()

# REGRESSION WITH LINEAR REGRESSION ==========================================

from pyspark.ml.regression import LinearRegression

lr = LinearRegression(featuresCol = 'features', labelCol = 'Price')

model = lr.fit(train_data)
predictions = model.transform(test_data)
predictions.select('features', 'Price', 'predictions').show()

```

### Unsupervised Learning

```py

data = [...]
schema = StructType([
    StructField('CustomerID', StringType(), True),
    StructField('ElectronicsSpend', IntegerType(), True),
    StructField('FashionSpend', IntegerType(), True),
    StructField('GrocerySpend', IntegerType(), True)
])
df = spark.createDataFrame(data, schema)

# K-MEANS CLUSTERING

from pyspark.ml.clustering import KMeans

feature_columns = ['ElectronicsSpend', 'FashionSpend', 'GrocerySpend']
assembler = VectorAssembler(inputCols = feature_columns, outputCols = 'features')
df_assembled - assembler.transform(df)

kmeans = KMeans(featuresCol = 'features', k = 3)
model = kmeans.fit(train_data)
predictions = model.transform(test_data)
predictions.select('CustomerID', 'features', 'predictions').show()


# PRINCIPAL COMPONENT ANALYSIS
from pyspark.ml.feature import PCA
pca = PCA(k=2, inputCol = 'features', outputCol = 'pca_features')
nodel = pca.fit(df_assembled)
df_pca = model.transform(df_assembled)
df_pca.select('CustomerID', 'pca_features').show(truncate=False)


```