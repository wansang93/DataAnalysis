# Big Data Analyst Certification

## 1유형

### 데이터 생성 및 불러오기

```python
import pandas as pd

# 데이터 불러오기
URL = r'test'
df = pd.read_csv(URL, encoding='euckr')
df

# 데이터 생성하기
df = pd.DataFrame(data, columns=['1', '2', '3'])
df
```

### SELECT & WHERE

```python
condition = df['column1'] == 'str'
df[['column1', 'column2']][condition]
```

### ㅇㅇ

```python
df[['mpg']]
```

## 2유형

```python
import pandas as pd

train = pd.read_csv('train.csv')
test = pd.read_csv('test.csv')

pd.set_option('display.max_columns', None)


from sklearn.preprocessing import LabelEncoder

```

## 3유형

```python
pd.DataFrame().to_csv('제목.csv', index=False)


X_train.isnull().sum()

drop_columns = ['id', '컬럼명']
X_train.drop(drop_columns, axis=1)


import pandas as pd
import numpy as np

from sklearn.model_selection import train_test_split
from sklearn.preprocessing import OneHotEncoder, StandardScaler
from sklearn.compose import ColumnTransformer


X_train['컬럼'].value_counts()


mean().sort_values(ascending='')
.sort_values('test', ascending='False')

```
