---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 95acb028867caf6f497dd99ad3082efcaab09c7b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93325297"
---
Giriş betiği, dağıtılan bir web hizmetine gönderilen verileri alır ve modele geçirir. Ardından model tarafından döndürülen yanıtı alır ve bunu istemciye döndürür. *Betik, modelinize özeldir*. Modelin beklediği ve döndürdüğü verileri anlaması gerekir.

Giriş betiğinizdeki gerçekleştirmeniz gereken iki şey şunlardır:

1. Modelinize yükleme (adlı bir işlevi kullanarak `init()` )
1. Modelinizi giriş verilerinde çalıştırma (adlı bir işlev kullanarak `run()` )

Bu adımları ayrıntılı olarak inceleyelim.

### <a name="writing-init"></a>İnit () yazma 

#### <a name="loading-a-registered-model"></a>Kayıtlı model yükleme

Kayıtlı modelleriniz, adlı bir ortam değişkeni tarafından işaret edilen bir yolda depolanır `AZUREML_MODEL_DIR` . Tam dizin yapısı hakkında daha fazla bilgi için bkz. [giriş betiğinizdeki model dosyalarını bulma](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models)

#### <a name="loading-a-local-model"></a>Yerel model yükleme

Modelinizi kaydetmeyi ve modelinizi kaynak dizininizin bir parçası olarak geçirmeyi tercih ettiyseniz, yolu, Puanlama betiğinizle ilişkili olarak modelin yolunu geçirerek yerel olarak olduğu gibi okuyabilirsiniz. Örneğin, şu şekilde yapılandırılmış bir dizininiz varsa:

```bash

- source_dir
    - score.py
    - models
        - model1.onnx

```

modellerinizi aşağıdaki Python kodu ile yükleyebilirsiniz:

```python
import os

model = open(os.path.join('.', 'models', 'model1.onnx'))
```

### <a name="writing-run"></a>Run () yazma

`run()` , modelinizin her bir Puanlama isteği aldığında yürütülür ve isteğin gövdesinin aşağıdaki yapıyla bir JSON belgesi olmasını bekler:

```json
{
    "data": <model-specific-data-structure>
}

```

Girişi, `run()` "Data" anahtarından sonraki bir şeyi içeren bir Python dizesidir.

Aşağıdaki örnek, kayıtlı bir scikit-öğrenme modelinin nasıl yükleneceğini ve sayısal tuş bir y verisi ile nasıl puan alınacağını gösterir:

```python
import json
import numpy as np
import os
from sklearn.externals import joblib


def init():
    global model
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(data):
    try:
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Otomatik Swagger şema oluşturma ve ikili (örn. resim) verileri dahil daha gelişmiş örnekler için [Gelişmiş giriş betiği yazma makalesini](../articles/machine-learning/how-to-deploy-advanced-entry-script.md) okuyun