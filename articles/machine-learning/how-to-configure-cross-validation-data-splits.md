---
title: Otomatik makine öğreniminde veri ayırır ve çapraz doğrulama
titleSuffix: Azure Machine Learning
description: Otomatik makine öğrenimi için veri kümesi bölmelerini ve çapraz doğrulamayı yapılandırma hakkında bilgi edinin denemeleri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 02/23/2021
ms.openlocfilehash: 31d3dc2c2d8194541ba1fe7d0865e6c939d75f73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102501592"
---
# <a name="configure-data-splits-and-cross-validation-in-automated-machine-learning"></a>Otomatik makine öğrenmesinde veri bölmelerini ve çapraz doğrulamayı yapılandırma

Bu makalede, otomatik makine öğrenimi, otomatik ML, denemeleri için çapraz doğrulama ayarlarıyla birlikte eğitim verilerini ve doğrulama verilerinin nasıl yapılandırılacağı ile ilgili farklı seçenekleri öğrenirsiniz.

Azure Machine Learning ' de, otomatik ML 'yi birden çok ML modeli oluşturmak için kullandığınızda, her bir alt çalıştırmanın, bu modelin doğruluk veya AUC ağırlıklı gibi kalite ölçümlerini hesaplayarak ilgili modeli doğrulaması gerekir. Bu ölçümler, her bir modelle yapılan tahminlerin, doğrulama verilerinde geçmiş gözlemlerden gerçek etiketlerle karşılaştırılmasıyla hesaplanır. [Ölçümlerin doğrulama türü temelinde nasıl hesaplandığı hakkında daha fazla bilgi edinin](#metric-calculation-for-cross-validation-in-machine-learning). 

Otomatik ML denemeleri otomatik olarak model doğrulaması gerçekleştirir. Aşağıdaki bölümlerde, [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/)ile doğrulama ayarlarını nasıl daha da özelleştireceğiniz açıklanır. 

Düşük kod veya kod içermeyen bir deneyim için [Azure Machine Learning Studio 'da otomatik makine öğrenimi denemeleri oluşturma](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment)makalesine bakın. 

> [!NOTE]
> Studio şu anda eğitim ve doğrulama verileri bölmelerini ve çapraz doğrulama seçeneklerini desteklemektedir, ancak doğrulama kümesi için bireysel veri dosyalarının belirtilmesini desteklemez. 

## <a name="prerequisites"></a>Önkoşullar

İhtiyacınız olan bu makalede,

* Azure Machine Learning çalışma alanı. Çalışma alanını oluşturmak için, bkz. [Azure Machine Learning çalışma alanı oluşturma](how-to-manage-workspace.md).

* Azure Machine Learning SDK ile bir otomatik makine öğrenimi oluşturma denemesinde deneyim elde edin. Temel otomatik makine öğrenimi tasarım düzenlerini görmek için [öğreticiyi](tutorial-auto-train-models.md) izleyin veya [nasıl yapılır?](how-to-configure-auto-train.md)

* Eğitim/doğrulama verilerinin ve çapraz doğrulamanın makine öğrenimi kavramları olarak anlaşılmasının bir açıklaması. Üst düzey bir açıklama için

    * [Machine Learning 'de Eğitim, doğrulama ve test verileri hakkında](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7)

    * [Makine öğreniminde çapraz doğrulamayı anlama](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd) 

## <a name="default-data-splits-and-cross-validation-in-machine-learning"></a>Makine öğreniminde varsayılan veri ayırır ve çapraz doğrulama

Deneme ve eğitim ayarlarınızı tanımlamak için, [oto Mlconfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) nesnesini kullanın. Aşağıdaki kod parçacığında, veya için parametreler olan `n_cross_validation` veya `validation_ data` dahil **olmayan** yalnızca gerekli parametrelerin tanımlandığından emin olun.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'Class'
                            )
```

Açıkça bir `validation_data` veya `n_cross_validation` parametresi belirtmezseniz, otomatik ml, tek veri kümesinde belirtilen satır sayısına bağlı olarak varsayılan teknikleri uygular `training_data` :

|Eğitim &nbsp; veri &nbsp; boyutu| Doğrulama tekniği |
|---|-----|
|**&nbsp; &nbsp; 20.000 &nbsp; satırdan büyük**| Eğitme/doğrulama verileri bölünmesi uygulandı. Varsayılan değer, doğrulama kümesi olarak ilk eğitim verileri kümesinin %10 ' un sürme sayısıdır. Buna karşılık, bu doğrulama kümesi ölçüm hesaplamaları için kullanılır.
|**&nbsp; &nbsp; 20.000 &nbsp; satırdan küçük**| Çapraz doğrulama yaklaşımı uygulanır. Varsayılan katların sayısı satır sayısına bağlıdır. <br> **Veri kümesi 1.000 satırdan azsa**, 10 katlar kullanılır. <br> **Satırlar 1.000 ve 20.000 arasındaysa**, üç katlar kullanılır.

## <a name="provide-validation-data"></a>Doğrulama verisi sağlama

Bu durumda, tek bir veri dosyası ile başlayabilir ve bunu eğitim verilerine ve doğrulama veri kümelerine bölebilir ya da doğrulama kümesi için ayrı bir veri dosyası sağlayabilirsiniz. Her iki durumda da, `validation_data` nesnenizin parametresi `AutoMLConfig` doğrulama kümesi olarak hangi verileri kullanacağınızı atar. Bu parametre yalnızca bir [Azure Machine Learning DataSet](how-to-create-register-datasets.md) veya Pandas dataframe biçimindeki veri kümelerini kabul eder.   

> [!NOTE]
> `validation_size`Tahmin senaryolarında parametre desteklenmez.

Aşağıdaki kod örneği, içinde sunulan verilerin hangi kısmını `dataset` eğitim ve doğrulama için kullanmak üzere açıkça tanımlar.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

training_data, validation_data = dataset.random_split(percentage=0.8, seed=1)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = training_data,
                             validation_data = validation_data,
                             label_column_name = 'Class'
                            )
```

## <a name="provide-validation-set-size"></a>Doğrulama kümesi boyutu sağla

Bu durumda, deneme için yalnızca tek bir veri kümesi sağlanır. Diğer bir deyişle, `validation_data` parametresi belirtilmez  ve belirtilen veri kümesi `training_data` parametreye atanır.  

`AutoMLConfig`Nesneniz `validation_size` için, parametresini, doğrulama için eğitim verilerinin bir kısmını tutacak şekilde ayarlayabilirsiniz. Bu, doğrulama kümesinin otomatik ML tarafından belirtilen ilk tarafından bölüneceği anlamına gelir `training_data` . Bu değer 0,0 ile 1,0 arasında olmalıdır (örneğin, 0,2 doğrulama verileri için verilerin %20 ' si tutulur).

> [!NOTE]
> `validation_size`Tahmin senaryolarında parametre desteklenmez. 

Aşağıdaki kod örneğine bakın:

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="k-fold-cross-validation"></a>Çapraz doğrulamayı yana kesme

K katlama çapraz doğrulama gerçekleştirmek için `n_cross_validations` parametresini ekleyin ve bir değere ayarlayın. Bu parametre, kaç tane çapraz doğrulamayı, aynı sayıda katlara göre gerçekleştirileceğini ayarlar.

> [!NOTE]
> `n_cross_validations`Derin sinir ağlarını kullanan sınıflandırma senaryolarında parametre desteklenmez.
 
Aşağıdaki kodda, çapraz doğrulama için beş katların tanımlanması gerekir. Bu nedenle, beş farklı seyahat, veri 4/5 kullanan her eğitim ve her bir doğrulama, her seferinde farklı bir gizleme katlamalı şekilde verilerin 1/5 ' i kullanıyor.

Sonuç olarak, ölçümler, beş doğrulama ölçümlerinin ortalaması ile hesaplanır.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 5
                             label_column_name = 'Class'
                            )
```
## <a name="monte-carlo-cross-validation"></a>Monte Carlo çapraz doğrulama

Monte Carlo çapraz doğrulama gerçekleştirmek için, nesnenizin hem hem `validation_size` de `n_cross_validations` parametreleri ekleyin `AutoMLConfig` . 

Monte Carlo çapraz doğrulaması için otomatik ML, doğrulama için parametresi tarafından belirtilen eğitim verilerinin bölümünü bir kenara koyar `validation_size` ve daha sonra eğitim için verilerin geri kalanını atar. Bu işlem daha sonra parametresinde belirtilen değere göre tekrarlanır `n_cross_validations` ; her seferinde rastgele olan yeni eğitim ve doğrulama bölmelerini oluşturur.

> [!NOTE]
> Tahmin senaryolarında Monte Carlo çapraz doğrulaması desteklenmez.

Takip kodu tanımlar, çapraz doğrulama için 7 katds ve eğitim verilerinin %20 ' si doğrulama için kullanılmalıdır. Bu nedenle, 7 farklı seyahat, her eğitim verilerin %80 ' sini kullanır ve her doğrulama her seferinde farklı bir gizleme katlamalı verilerin %20 ' sini kullanır.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 7
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="specify-custom-cross-validation-data-folds"></a>Özel çapraz doğrulama veri katlarını belirtin

Ayrıca kendi çapraz doğrulama (CV) veri katlarınızı da sağlayabilirsiniz. Bu, hangi sütunların bölüneceği ve doğrulama için kullanılacağını belirtirken, daha gelişmiş bir senaryo olarak kabul edilir.  Eğitim verilerinize özel CV bölünmüş sütunları ekleyin ve sütun adlarını parametre olarak doldurarak sütunları belirtin `cv_split_column_names` . Her sütun bir çapraz doğrulama bölünmesini temsil eder ve tamsayı değerleri 1 veya 0 ile doldurulur; burada 1, eğitim için satırın kullanılması gerektiğini gösterir ve 0, satırın doğrulama için kullanılması gerektiğini gösterir.

Aşağıdaki kod parçacığı, iki CV bölünen sütunları ' CV1 ' ve ' CV2 ' olan banka pazarlama verilerini içerir.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_with_cv.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'y',
                             cv_split_column_names = ['cv1', 'cv2']
                            )
```

> [!NOTE]
> `cv_split_column_names`Ve ile kullanmak `training_data` için `label_column_name` lütfen Azure Machine Learning Python SDK sürümü 1.6.0 veya üstünü yükseltin. Önceki SDK sürümleri için lütfen bkz `cv_splits_indices` . using, ancak `X` ve `y` yalnızca veri kümesi girişi ile kullanıldığını unutmayın. 


## <a name="metric-calculation-for-cross-validation-in-machine-learning"></a>Makine öğreniminde çapraz doğrulama için ölçüm hesaplaması

K katlamalı veya Monte Carlo çapraz doğrulaması kullanıldığında ölçümler her bir doğrulama katalınarak hesaplanır ve sonra toplanır. Toplama işlemi, skaler ölçümler için bir ortalama ve grafiklerin toplamı olur. Çapraz doğrulama sırasında hesaplanan ölçümler, tüm katlara ve bu nedenle eğitim kümesindeki tüm örneklere dayanır. [Otomatik makine öğreniminde ölçümler hakkında daha fazla bilgi edinin](how-to-understand-automated-ml.md).

Özel bir doğrulama kümesi veya otomatik olarak seçilen doğrulama kümesi kullanıldığında, model değerlendirme ölçümleri eğitim verileri değil yalnızca bu doğrulama kümesinden hesaplanır.

## <a name="next-steps"></a>Sonraki adımlar

* [İmdengeli verileri ve fazla sığdırmayı önleyin](concept-manage-ml-pitfalls.md).
* [Öğretici: taksi Fares-Split Data bölümünü tahmin etmek için otomatik makine öğrenimi kullanın](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets).
* [Bir zaman serisi tahmin modelini otomatik olarak eğitme](how-to-auto-train-forecast.md).
