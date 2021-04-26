---
title: Not defteri kodunu Python betiklerine Dönüştür
titleSuffix: Azure Machine Learning
description: MLOpsPython Code şablonunu kullanarak Machine Learning not defterlerinizi üretime hazırlamış koda dönüştürün. Daha sonra bu kodu test edebilir, dağıtabilir ve otomatikleştirin.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/30/2020
ms.custom: devx-track-python
ms.openlocfilehash: 37778bc096c9089e3706907fcdd6b9c816cc5fbc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817495"
---
# <a name="tutorial-convert-ml-experiments-to-production-python-code"></a>Öğretici: ML denemeleri 'yi üretim Python koduna dönüştürme

Bu öğreticide, Juptyer not defterlerini, MLOpsPython kod şablonu ve Azure Machine Learning kullanarak test etme ve Otomasyonu kolay hale getirmek için Python betiklerine nasıl dönüştürebileceğinizi öğreneceksiniz. Genellikle, bu işlem bir Juptyer Not defterinden deneme/eğitim kodu alıp Python betiklerine dönüştürmek için kullanılır. Bu betikler daha sonra üretim ortamınızda test ve CI/CD otomasyonu için kullanılabilir. 

Machine Learning projesi, gerçek veri kümelerini kullanarak Jupyter Notebook gibi çevik araçlarla deneme gerektirir. Model üretime hazırsa, model kodu bir üretim kodu deposuna yerleştirilmelidir. Bazı durumlarda, model kodu, üretim kodu deposuna yerleştirilecek Python betiklerine dönüştürülmelidir. Bu öğreticide, deneme kodunu Python betiklerine aktarma hakkında önerilen bir yaklaşım ele alınmaktadır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Gereksiz kodu temizle
> * Kodu işlevlere Jupyter Notebook yeniden düzenleme
> * İlgili görevler için Python betikleri oluşturma
> * Birim testleri oluşturma

## <a name="prerequisites"></a>Önkoşullar

- [MLOpsPython şablonunu](https://github.com/microsoft/MLOpsPython/generate) oluşturun ve `experimentation/Diabetes Ridge Regression Training.ipynb` ve `experimentation/Diabetes Ridge Regression Scoring.ipynb` not defterlerini kullanın. Bu not defterleri, deneme 'den üretime dönüştürme örneği olarak kullanılır. Bu not defterlerini adresinde bulabilirsiniz [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation) .
- `nbconvert`yükleyin. [Yükleme](https://nbconvert.readthedocs.io/en/latest/install.html) sayfasında, yalnızca __nbconvert 'i yükleme__ bölümündeki yükleme yönergelerini izleyin.

## <a name="remove-all-nonessential-code"></a>Tüm gereksiz kodu kaldır

Deneme sırasında yazılan bazı kodlar yalnızca keşif amaçlıdır. Bu nedenle, deneysel kodu üretim koduna dönüştürmenin ilk adımı, bu gereksiz kodu kaldırmayacak. Gerekli olmayan kodun kaldırılması kodu daha sürdürülebilir hale getirir. Bu bölümde kodu Not defterinden kaldıracaksınız `experimentation/Diabetes Ridge Regression Training.ipynb` . Ve ' nin şeklini ve öğesini `X` `y` çağıran hücre yazdırma deyimleri `features.describe` yalnızca veri araştırması için ve kaldırılabilir. Gereksiz kodu kaldırdıktan sonra, `experimentation/Diabetes Ridge Regression Training.ipynb` markaşağı olmadan aşağıdaki kod gibi görünmelidir:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
import pandas as pd

sample_data = load_diabetes()

df = pd.DataFrame(
    data=sample_data.data,
    columns=sample_data.feature_names)
df['Y'] = sample_data.target

X = df.drop('Y', axis=1).values
y = df['Y'].values

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

args = {
    "alpha": 0.5
}

reg_model = Ridge(**args)
reg_model.fit(data["train"]["X"], data["train"]["y"])

preds = reg_model.predict(data["test"]["X"])
mse = mean_squared_error(preds, y_test)
metrics = {"mse": mse}
print(metrics)

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Kodu işlevlere yeniden düzenleme

İkincisi, Juprivcode 'un işlevlere yeniden düzenlenmiş olması gerekir. Kodu işlevlere yeniden düzenleme, birim testini daha kolay hale getirir ve kodu daha sürdürülebilir hale getirir. Bu bölümde, yeniden düzenleme yapacaksınız:

- Diabetes Ridge Regresyon eğitimi Not defteri ( `experimentation/Diabetes Ridge Regression Training.ipynb` )
- Diabetes Ridge Regresyon Puanlama Not defteri ( `experimentation/Diabetes Ridge Regression Scoring.ipynb` )

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Diabetes Ridge gerileme eğitim Not defterini işlevlere yeniden düzenleme

' De `experimentation/Diabetes Ridge Regression Training.ipynb` , aşağıdaki adımları izleyin:

1. `split_data`Veri çerçevesini teste bölmek ve verileri eğmek için adlı bir işlev oluşturun. İşlev, veri çerçevesini `df` bir parametre olarak almalıdır ve anahtarları ve anahtarlarını içeren bir sözlük döndürmelidir `train` `test` .

    *Verileri bölmek Için verileri eğitim ve doğrulama kümeleri* başlığına taşıyın `split_data` ve nesneyi döndürecek şekilde değiştirin `data` .

1. `train_model`Parametreleri alan ve `data` eğitilen bir model döndüren adlı bir işlev oluşturun `args` .

    Kodu *eğitim kümesindeki başlık eğitimi modelinde* `train_model` işleve taşıyın ve nesneyi döndürecek şekilde değiştirin `reg_model` . Sözlüğü kaldırma `args` , değerler `args` parametreden gelir.

1. `get_model_metrics`Parametresi `reg_model` ve parametrelerini `data` değerlendiren ve sonra eğitilen model için ölçüm sözlüğünü döndüren adlı bir işlev oluşturun.

    Kodu *doğrulama kümesi başlığındaki doğrulama modeli '* ndeki `get_model_metrics` işleve taşıyın ve nesneyi döndürecek şekilde değiştirin `metrics` .

Üç işlev şu şekilde olmalıdır:

```python
# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics
```

Hala içinde `experimentation/Diabetes Ridge Regression Training.ipynb` , aşağıdaki adımları izleyin:

1. Hiçbir `main` parametre alıp hiçbir şey döndürmemesi adlı yeni bir işlev oluşturun.
1. "Verileri yükle" başlığının altındaki kodu `main` işleve taşıyın.
1. İşleve yeni yazılan işlevler için etkinleştirmeleri ekleyin `main` :
    ```python
    # Split Data into Training and Validation Sets
    data = split_data(df)
    ```

    ```python
    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)
    ```

    ```python
    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)
    ```
1. Kodu, "modeli Kaydet" başlığının altındaki `main` işleve taşıyın.

`main`İşlev aşağıdaki kod gibi görünmelidir:

```python
def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)
```

Bu aşamada, ilk hücrede içeri aktarma deyimleri dışında bir işlevde bulunmayan not defterinde kalan bir kod olmamalıdır.

İşlevi çağıran bir ifade ekleyin `main` .

```python
main()
```

Yeniden düzenleme işleminden sonra, `experimentation/Diabetes Ridge Regression Training.ipynb` markafter olmadan aşağıdaki kod gibi görünmelidir:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

main()
```

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Diabetes Ridge Regresyon Puanlama Not defterini işlevlere yeniden düzenleme

' De `experimentation/Diabetes Ridge Regression Scoring.ipynb` , aşağıdaki adımları izleyin:

1. Hiçbir `init` parametre alıp hiçbir şey döndürmemesi adlı yeni bir işlev oluşturun.
1. "Model yükleme" başlığının altındaki kodu `init` işleve kopyalayın.

`init`İşlev aşağıdaki kod gibi görünmelidir:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

`init`İşlev oluşturulduktan sonra, "Load model" başlığı altındaki tüm kodu aşağıdaki şekilde tek bir çağrısıyla değiştirin `init` :

```python
init()
```

' De `experimentation/Diabetes Ridge Regression Scoring.ipynb` , aşağıdaki adımları izleyin:

1. `run`Parametresi alan ve olarak çağrılan yeni bir işlev `raw_data` oluşturun `request_headers` ve aşağıdaki gibi bir sonuç sözlüğü döndürür:

    ```python
    {"result": result.tolist()}
    ```

1. Kodu "hazırlama verileri" ve "veri puanı" başlıklarının altına kopyalayın `run` .

    `run`İşlev aşağıdaki kod gibi görünmelidir (değişkenleri belirleyen deyimleri kaldırmayı unutmayın `raw_data` ve `request_headers` Bu, daha sonra `run` işlev çağrıldığında kullanılır):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

`run`İşlev oluşturulduktan sonra, "veri hazırlama" ve "veri puanı" başlıklarının altındaki tüm kodu aşağıdaki kodla değiştirin:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

Önceki kod değişkenleri ayarlar `raw_data` ve `request_header` ile işlevi çağırır ve `run` `raw_data` `request_header` tahminleri yazdırır.

Yeniden düzenleme işleminden sonra, `experimentation/Diabetes Ridge Regression Scoring.ipynb` markafter olmadan aşağıdaki kod gibi görünmelidir:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, {})
print("Test result: ", prediction)
```

## <a name="combine-related-functions-in-python-files"></a>Python dosyalarında ilgili işlevleri birleştirme

Kodun yeniden kullanılmasına daha fazla yardımcı olmak için, üçüncü ve ilgili işlevlerin Python dosyalarında birleştirilmesi gerekir. Bu bölümde, aşağıdaki Not defterleri için Python dosyaları oluşturacaksınız:

- Diabetes Ridge Regresyon eğitimi Not defteri ( `experimentation/Diabetes Ridge Regression Training.ipynb` )
- Diabetes Ridge Regresyon Puanlama Not defteri ( `experimentation/Diabetes Ridge Regression Scoring.ipynb` )

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Diabetes Ridge Regresyon eğitimi Not defteri için Python dosyası oluşturma

Aşağıdaki ifadeyi paketini ve yolunu kullanan bir komut isteminde çalıştırarak not defterinizi yürütülebilir bir betiğe dönüştürün `nbconvert` `experimentation/Diabetes Ridge Regression Training.ipynb` :

```
jupyter nbconvert "Diabetes Ridge Regression Training.ipynb" --to script --output train
```

Not defteri ' ne dönüştürüldükten sonra `train.py` istenmeyen açıklamaları kaldırın. Dosyasının sonundaki çağrısını, `main()` Aşağıdaki kod gibi bir koşullu çağrı ile değiştirin:

```python
if __name__ == '__main__':
    main()
```

`train.py`Dosyanız aşağıdaki kod gibi görünmelidir:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

if __name__ == '__main__':
    main()
```

`train.py` , artık çalıştırılarak bir terminalden çağrılabilir `python train.py` .
Ayrıca, işlevleri `train.py` başka dosyalardan de çağrılabilir.

`train_aml.py` `diabetes_regression/training` MLOpsPython deposundaki dizininde bulunan dosya `train.py` Azure Machine Learning deneme çalıştırması bağlamında içinde tanımlanan işlevleri çağırır. İşlevler, bu kılavuzun ilerleyen kısımlarında ele alınan birim testlerinde de çağrılabilir.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Diabetes Ridge Regresyon Puanlama Not defteri için Python dosyası oluşturma

Aşağıdaki ifadeyi paketini ve yolunu kullanan bir komut isteminde çalıştırarak not defterinizi yürütülebilir bir betiğe birlikte açın `nbconvert` `experimentation/Diabetes Ridge Regression Scoring.ipynb` :

```
jupyter nbconvert "Diabetes Ridge Regression Scoring.ipynb" --to script --output score
```

Not defteri ' ne dönüştürüldükten sonra `score.py` istenmeyen açıklamaları kaldırın. `score.py`Dosyanız aşağıdaki kod gibi görünmelidir:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, request_header)
print("Test result: ", prediction)
```

`model`Betiğin tamamında görünür olması için değişkenin genel olması gerekir. Aşağıdaki ifadeyi işlevinin başına ekleyin `init` :

```python
global model
```

Önceki deyimin eklendikten sonra, `init` işlev aşağıdaki kod gibi görünmelidir:

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Her Python dosyası için birim testleri oluşturma

Dördüncü olarak, Python işlevleriniz için birim testleri oluşturun. Birim testleri kodu işlevsel gerilemeler ile koruyabilir ve bakımını daha kolay hale getirir. Bu bölümde, içindeki işlevler için birim testleri oluşturacağız `train.py` .

`train.py` birden çok işlev içerir, ancak `train_model` Bu öğreticide yalnızca pytest çerçevesini kullanarak işlev için tek bir birim testi oluşturacağız. Pytest tek Python birimi test çatısı değildir, ancak en sık kullanılan bir biridir. Daha fazla bilgi için [Pytest](https://pytest.org)' i ziyaret edin.

Birim testi genellikle üç ana eylem içerir:

- Nesne düzenleme-gerekli nesneleri oluşturma ve ayarlama
- Bir nesne üzerinde işlem
- Onay beklenildiği onaylama

Birim testi, `train_model` bazı sabit kodlanmış veriler ve bağımsız değişkenlerle çağrı yapar ve `train_model` Bu tahmini bir tahmin yapmak ve bu tahmini beklenen bir değerle karşılaştırmak için elde edilen eğitilen modeli kullanarak beklendiği gibi işlem yapıp yapmadığını doğrular.

```python
import numpy as np
from code.training.train import train_model


def test_train_model():
    # Arrange
    X_train = np.array([1, 2, 3, 4, 5, 6]).reshape(-1, 1)
    y_train = np.array([10, 9, 8, 8, 6, 5])
    data = {"train": {"X": X_train, "y": y_train}}

    # Act
    reg_model = train_model(data, {"alpha": 1.2})

    # Assert
    preds = reg_model.predict([[1], [2]])
    np.testing.assert_almost_equal(preds, [9.93939393939394, 9.03030303030303])
```

## <a name="next-steps"></a>Sonraki adımlar

Bir deneyden üretim koduna nasıl dönüştüreceğiniz artık daha fazla bilgi ve sonraki adımlar için aşağıdaki bağlantılara bakın:

+ [MLOpsPython](https://github.com/microsoft/MLOpsPython/blob/master/docs/custom_model.md): Azure Pipelines ve Azure Machine Learning kullanarak kendi modelinizi eğlendirmek, değerlendirmek ve dağıtmak IÇIN bir CI/CD işlem hattı oluşturun
+ [Azure ML deneme çalıştırmaları ve ölçümlerini izleme](./how-to-log-view-metrics.md)
+ [ML web hizmet uç noktalarından verileri izleme ve toplama](./how-to-enable-app-insights.md)