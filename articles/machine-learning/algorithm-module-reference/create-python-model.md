---
title: 'Python modeli oluşturma: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Özel Modelleme veya veri işleme modülü oluşturmak için Azure Machine Learning Python model modeli oluşturma ' yı nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: 33e15055958ac99f2aa9eb160f9e5cf3c5b0cd41
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493791"
---
# <a name="create-python-model"></a>Python Modeli Oluşturma

Bu makalede, Python betiğiyle eğitilen bir model oluşturmak için **Python modeli oluşturma** modülünün nasıl kullanılacağı açıklanır. 

Modeli, Azure Machine Learning tasarımcı ortamındaki bir Python paketine dahil olan herhangi bir öğrenici üzerine temelleyebilirsiniz. 

Modeli oluşturduktan sonra, Azure Machine Learning diğer öğrenme gibi bir veri kümesi üzerinde modeli eğmek için [eğitme modeli](train-model.md) ' ni kullanabilirsiniz. Eğitilen model, tahmine dayalı hale getirmek üzere modeli kullanmak için [puan modeline](score-model.md) geçirilebilir. Eğitilen model daha sonra kaydedilebilir ve Puanlama iş akışı bir Web hizmeti olarak yayımlanabilir.

> [!WARNING]
> Şu anda [modeli değerlendirmek](evaluate-model.md)Için bir Python modelinin puanlanmış sonuçlarının iletilmesi mümkün değildir. Bir modeli değerlendirmeniz gerekiyorsa, özel Python betiği yazabilir ve [Python betiği Yürüt](execute-python-script.md) modülünü kullanarak çalıştırabilirsiniz.  


## <a name="how-to-configure-create-python-model"></a>Python modeli oluşturmayı yapılandırma

Bu modülün kullanımı, Python 'un ara veya uzman bilgisini gerektirir. Modül, Azure Machine Learning ' de zaten yüklü olan Python paketlerine dahil olan herhangi bir Learner 'ın kullanımını destekler. Bkz. [Python betiğini yürütme](execute-python-script.md)içindeki önceden yüklenmiş Python paket listesi.
  

Bu makalede, **Python modelinin** basit bir işlem hattı ile nasıl kullanılacağı gösterilir. Ardışık düzenin grafiği aşağıda verilmiştir.

![-Python-model oluştur](./media/module/aml-create-python-model.png)

1.  **Python modeli oluştur**' a tıklayın, modelleme veya veri yönetimi işleminizi uygulamak için betiği düzenleyin. Modeli, Azure Machine Learning ortamındaki bir Python paketine dahil olan herhangi bir öğrenner üzerinde temel alabilirsiniz.


    Aşağıda, popüler *sköğren* paketi kullanılarak Iki sınıf Naive Bayes sınıflandırıcının örnek kodu verilmiştir.

```Python

# The script MUST define a class named AzureMLModel.
# This class MUST at least define the following three methods:
    # __init__: in which self.model must be assigned,
    # train: which trains self.model, the two input arguments must be pandas DataFrame,
    # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
# The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.


import pandas as pd
from sklearn.naive_bayes import GaussianNB


class AzureMLModel:
    def __init__(self):
        self.model = GaussianNB()
        self.feature_column_names = list()

    def train(self, df_train, df_label):
        self.feature_column_names = df_train.columns.tolist()
        self.model.fit(df_train, df_label)

    def predict(self, df):
        return pd.DataFrame(
            {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
             'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
        )


```


2. Yeni oluşturduğunuz **Python modeli oluşturma** modülünü **eğitme** modeli ve **puan modeline** bağlayın

3. Modeli değerlendirmeniz gerekiyorsa, bir [Python betiği yürütün](execute-python-script.md) ve değerlendirmeyi uygulamak için Python betiğini düzenleyin.

Örnek değerlendirme kodu aşağıda verilmiştir.

```Python


# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to 
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):
    
    from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
    import pandas as pd
    import numpy as np
    
    scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
    ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
    ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
    probabilities = scores["probabilities"]
    
    accuracy, precision, recall, auc = \
    accuracy_score(ytrue, ypred),\
    precision_score(ytrue, ypred),\
    recall_score(ytrue, ypred),\
    roc_auc_score(ytrue, probabilities)
    
    metrics = pd.DataFrame();
    metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
    metrics["Value"] = [accuracy, precision, recall, auc]
    
    return metrics,

```
