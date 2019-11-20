---
title: 'Regresyon modeli öğreticisi: otomatik ML'
titleSuffix: Azure Machine Learning
description: Otomatik makine öğrenimi kullanarak makine öğrenimi modeli oluşturmayı öğrenin. Azure Machine Learning, veri ön işleme, algoritma seçimi ve hiper parametre seçimini sizin için otomatik bir şekilde gerçekleştirebilir. Ardından, son model Azure Machine Learning ile dağıtılır.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 11/04/2019
ms.openlocfilehash: 23441fb64293647698921c17c06731ab413b7699
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582448"
---
# <a name="tutorial-use-automated-machine-learning-to-predict-taxi-fares"></a>Öğretici: taksi Fares 'yi tahmin etmek için otomatik makine öğrenimi kullanma
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu öğreticide, NYC TAXI tarifeli havayolu fiyatlarını tahmin etmek üzere bir gerileme modeli oluşturmak için Azure Machine Learning ' de otomatik makine öğrenimini kullanırsınız. Bu işlem, eğitim verilerini ve yapılandırma ayarlarını kabul eder ve en iyi modele ulaşmak için farklı özellik normalleştirme/standartlaştırma yöntemlerinin, modellerinin ve hiper parametre ayarlarının birleşimleri aracılığıyla otomatik olarak yinelenir.

![Akış diyagramı](./media/tutorial-auto-train-models/flow2.png)

Bu öğreticide aşağıdaki görevleri öğreneceksiniz:

> [!div class="checklist"]
> * Azure açık veri kümelerini kullanarak verileri indirme, dönüştürme ve Temizleme
> * Otomatik makine öğrenimi regresyon modelini eğitme
> * Model doğruluğunu hesapla

Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. Azure Machine Learning [ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin.

## <a name="prerequisites"></a>Ön koşullar

* Zaten bir Azure Machine Learning çalışma alanınız veya Not defteri sanal makineniz yoksa [Kurulum öğreticisini](tutorial-1st-experiment-sdk-setup.md) doldurun.
* Kurulum öğreticisini tamamladıktan sonra, aynı not defteri sunucusunu kullanarak **öğreticiler/Regression-Automated-ml. ipynb** Not defterini açın.

Bu öğretici, kendi [Yerel ortamınızda](how-to-configure-environment.md#local)çalıştırmak istiyorsanız [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) 'da da kullanılabilir. Gerekli paketleri almak için `pip install azureml-sdk[automl] azureml-opendatasets azureml-widgets` çalıştırın.

## <a name="download-and-prepare-data"></a>Verileri indirme ve hazırlama

Gerekli paketleri içeri aktarın. Açık veri kümeleri paketi, indirilmeden önce tarih parametrelerini kolayca filtrelemek için her bir veri kaynağını temsil eden bir sınıf içerir (örneğin`NycTlcGreen`).

```python
from azureml.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Taxı verilerini tutmak için bir veri çerçevesi oluşturarak başlayın. Spark olmayan bir ortamda çalışırken, açık veri kümeleri, büyük veri kümeleriyle `MemoryError` önlemek için belirli sınıflarla tek seferde bir aydan verilerin indirilmesini sağlar.

Taxı verilerini indirmek için, bir kerede bir ay sonra bir kez ve veri çerçevesini bloktan kaçınmak için her aydan rastgele örnek 2.000 kayıtları `green_taxi_df`. Sonra verileri önizleyin.


```python
green_taxi_df = pd.DataFrame([])
start = datetime.strptime("1/1/2015","%m/%d/%Y")
end = datetime.strptime("1/31/2015","%m/%d/%Y")

for sample_month in range(12):
    temp_df_green = NycTlcGreen(start + relativedelta(months=sample_month), end + relativedelta(months=sample_month)) \
        .to_pandas_dataframe()
    green_taxi_df = green_taxi_df.append(temp_df_green.sample(2000))

green_taxi_df.head(10)
```

<div>
<style scoped>. dataframe tbody tr TH: yalnızca-of-type {Vertical-ALIGN: Middle;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Konağında VendorID</th>
      <th>lpepPickupDatetime</th>
      <th>lpepDropoffDatetime</th>
      <th>passengerCount</th>
      <th>Üçlü mesafe</th>
      <th>Pulocationıd</th>
      <th>Dolocationıd</th>
      <th>Pickupboylam</th>
      <th>Pickupenlem</th>
      <th>Açılan Boylam</th>
      <th>...</th>
      <th>paymentType</th>
      <th>fareAmount</th>
      <th>Ina</th>
      <th>Mtavergisi</th>
      <th>improvementSurcharge</th>
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>Ehailücret</th>
      <th>totalAmount</th>
      <th>Üçlü tür</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>131969</th>
      <td>2</td>
      <td>2015-01-11 05:34:44</td>
      <td>2015-01-11 05:45:03</td>
      <td>3</td>
      <td>4,84</td>
      <td>None</td>
      <td>None</td>
      <td>-73,88</td>
      <td>40,84</td>
      <td>-73,94</td>
      <td>...</td>
      <td>2</td>
      <td>15,00</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nBir</td>
      <td>16,30</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1129817</th>
      <td>2</td>
      <td>2015-01-20 16:26:29</td>
      <td>2015-01-20 16:30:26</td>
      <td>1</td>
      <td>0,69</td>
      <td>None</td>
      <td>None</td>
      <td>-73,96</td>
      <td>40,81</td>
      <td>-73,96</td>
      <td>...</td>
      <td>2</td>
      <td>4,50</td>
      <td>1,00</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nBir</td>
      <td>6,30</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1278620</th>
      <td>2</td>
      <td>2015-01-01 05:58:10</td>
      <td>2015-01-01 06:00:55</td>
      <td>1</td>
      <td>0,45</td>
      <td>None</td>
      <td>None</td>
      <td>-73,92</td>
      <td>40,76</td>
      <td>-73,91</td>
      <td>...</td>
      <td>2</td>
      <td>4,00</td>
      <td>0,00</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nBir</td>
      <td>4,80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>348430</th>
      <td>2</td>
      <td>2015-01-17 02:20:50</td>
      <td>2015-01-17 02:41:38</td>
      <td>1</td>
      <td>0,00</td>
      <td>None</td>
      <td>None</td>
      <td>-73,81</td>
      <td>40,70</td>
      <td>-73,82</td>
      <td>...</td>
      <td>2</td>
      <td>12,50</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nBir</td>
      <td>13,80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1269627</th>
      <td>1</td>
      <td>2015-01-01 05:04:10</td>
      <td>2015-01-01 05:06:23</td>
      <td>1</td>
      <td>0,50</td>
      <td>None</td>
      <td>None</td>
      <td>-73,92</td>
      <td>40,76</td>
      <td>-73,92</td>
      <td>...</td>
      <td>2</td>
      <td>4,00</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nBir</td>
      <td>5,00</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>811755</th>
      <td>1</td>
      <td>2015-01-04 19:57:51</td>
      <td>2015-01-04 20:05:45</td>
      <td>2</td>
      <td>1,10</td>
      <td>None</td>
      <td>None</td>
      <td>-73,96</td>
      <td>40,72</td>
      <td>-73,95</td>
      <td>...</td>
      <td>2</td>
      <td>6,50</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nBir</td>
      <td>7,80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>737281</th>
      <td>1</td>
      <td>2015-01-03 12:27:31</td>
      <td>2015-01-03 12:33:52</td>
      <td>1</td>
      <td>0,90</td>
      <td>None</td>
      <td>None</td>
      <td>-73,88</td>
      <td>40,76</td>
      <td>-73,87</td>
      <td>...</td>
      <td>2</td>
      <td>6,00</td>
      <td>0,00</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nBir</td>
      <td>6,80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>113951</th>
      <td>1</td>
      <td>2015-01-09 23:25:51</td>
      <td>2015-01-09 23:39:52</td>
      <td>1</td>
      <td>3,30</td>
      <td>None</td>
      <td>None</td>
      <td>-73,96</td>
      <td>40,72</td>
      <td>-73,91</td>
      <td>...</td>
      <td>2</td>
      <td>12,50</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nBir</td>
      <td>13,80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>150436</th>
      <td>2</td>
      <td>2015-01-11 17:15:14</td>
      <td>2015-01-11 17:22:57</td>
      <td>1</td>
      <td>1,19</td>
      <td>None</td>
      <td>None</td>
      <td>-73,94</td>
      <td>40,71</td>
      <td>-73,95</td>
      <td>...</td>
      <td>1</td>
      <td>7,00</td>
      <td>0,00</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>1,75</td>
      <td>0,00</td>
      <td>nBir</td>
      <td>9,55</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>432136</th>
      <td>2</td>
      <td>2015-01-22 23:16:33</td>
      <td>2015-01-22 23:20:13</td>
      <td>1</td>
      <td>0,65</td>
      <td>None</td>
      <td>None</td>
      <td>-73,94</td>
      <td>40,71</td>
      <td>-73,94</td>
      <td>...</td>
      <td>2</td>
      <td>5,00</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nBir</td>
      <td>6,30</td>
      <td>1,00</td>
    </tr>
  </tbody>
</table>
<p>10 satır × 23 sütun</p>
</div>


Artık ilk veriler yüklendikten sonra, toplama tarih/saati alanından çeşitli zamana dayalı özellikler oluşturmak için bir işlev tanımlayın. Bu işlem, ay numarası, ay günü, haftanın günü ve günün saati için yeni alanlar oluşturur ve modelin zaman tabanlı mevsimsellik açısından çarpaya izin verir. `build_time_features()` işlevini TAXI verilerinde her satıra tekrarlayarak uygulamak için veri çerçevesindeki `apply()` işlevini kullanın.

```python
def build_time_features(vector):
    pickup_datetime = vector[0]
    month_num = pickup_datetime.month
    day_of_month = pickup_datetime.day
    day_of_week = pickup_datetime.weekday()
    hour_of_day = pickup_datetime.hour

    return pd.Series((month_num, day_of_month, day_of_week, hour_of_day))

green_taxi_df[["month_num", "day_of_month","day_of_week", "hour_of_day"]] = green_taxi_df[["lpepPickupDatetime"]].apply(build_time_features, axis=1)
green_taxi_df.head(10)
```

<div>
<style scoped>. dataframe tbody tr TH: yalnızca-of-type {Vertical-ALIGN: Middle;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Konağında VendorID</th>
      <th>lpepPickupDatetime</th>
      <th>lpepDropoffDatetime</th>
      <th>passengerCount</th>
      <th>Üçlü mesafe</th>
      <th>Pulocationıd</th>
      <th>Dolocationıd</th>
      <th>Pickupboylam</th>
      <th>Pickupenlem</th>
      <th>Açılan Boylam</th>
      <th>...</th>
      <th>improvementSurcharge</th>
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>Ehailücret</th>
      <th>totalAmount</th>
      <th>Üçlü tür</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>131969</th>
      <td>2</td>
      <td>2015-01-11 05:34:44</td>
      <td>2015-01-11 05:45:03</td>
      <td>3</td>
      <td>4,84</td>
      <td>None</td>
      <td>None</td>
      <td>-73,88</td>
      <td>40,84</td>
      <td>-73,94</td>
      <td>...</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nBir</td>
      <td>16,30</td>
      <td>1,00</td>
      <td>1</td>
      <td>11</td>
      <td>6</td>
      <td>5</td>
    </tr>
    <tr>
      <th>1129817</th>
      <td>2</td>
      <td>2015-01-20 16:26:29</td>
      <td>2015-01-20 16:30:26</td>
      <td>1</td>
      <td>0,69</td>
      <td>None</td>
      <td>None</td>
      <td>-73,96</td>
      <td>40,81</td>
      <td>-73,96</td>
      <td>...</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nBir</td>
      <td>6,30</td>
      <td>1,00</td>
      <td>1</td>
      <td>20</td>
      <td>1</td>
      <td>16</td>
    </tr>
    <tr>
      <th>1278620</th>
      <td>2</td>
      <td>2015-01-01 05:58:10</td>
      <td>2015-01-01 06:00:55</td>
      <td>1</td>
      <td>0,45</td>
      <td>None</td>
      <td>None</td>
      <td>-73,92</td>
      <td>40,76</td>
      <td>-73,91</td>
      <td>...</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nBir</td>
      <td>4,80</td>
      <td>1,00</td>
      <td>1</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
    </tr>
    <tr>
      <th>348430</th>
      <td>2</td>
      <td>2015-01-17 02:20:50</td>
      <td>2015-01-17 02:41:38</td>
      <td>1</td>
      <td>0,00</td>
      <td>None</td>
      <td>None</td>
      <td>-73,81</td>
      <td>40,70</td>
      <td>-73,82</td>
      <td>...</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nBir</td>
      <td>13,80</td>
      <td>1,00</td>
      <td>1</td>
      <td>17</td>
      <td>5</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1269627</th>
      <td>1</td>
      <td>2015-01-01 05:04:10</td>
      <td>2015-01-01 05:06:23</td>
      <td>1</td>
      <td>0,50</td>
      <td>None</td>
      <td>None</td>
      <td>-73,92</td>
      <td>40,76</td>
      <td>-73,92</td>
      <td>...</td>
      <td>0</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nBir</td>
      <td>5,00</td>
      <td>1,00</td>
      <td>1</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
    </tr>
    <tr>
      <th>811755</th>
      <td>1</td>
      <td>2015-01-04 19:57:51</td>
      <td>2015-01-04 20:05:45</td>
      <td>2</td>
      <td>1,10</td>
      <td>None</td>
      <td>None</td>
      <td>-73,96</td>
      <td>40,72</td>
      <td>-73,95</td>
      <td>...</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nBir</td>
      <td>7,80</td>
      <td>1,00</td>
      <td>1</td>
      <td>4</td>
      <td>6</td>
      <td>19</td>
    </tr>
    <tr>
      <th>737281</th>
      <td>1</td>
      <td>2015-01-03 12:27:31</td>
      <td>2015-01-03 12:33:52</td>
      <td>1</td>
      <td>0,90</td>
      <td>None</td>
      <td>None</td>
      <td>-73,88</td>
      <td>40,76</td>
      <td>-73,87</td>
      <td>...</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nBir</td>
      <td>6,80</td>
      <td>1,00</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
      <td>12</td>
    </tr>
    <tr>
      <th>113951</th>
      <td>1</td>
      <td>2015-01-09 23:25:51</td>
      <td>2015-01-09 23:39:52</td>
      <td>1</td>
      <td>3,30</td>
      <td>None</td>
      <td>None</td>
      <td>-73,96</td>
      <td>40,72</td>
      <td>-73,91</td>
      <td>...</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nBir</td>
      <td>13,80</td>
      <td>1,00</td>
      <td>1</td>
      <td>9</td>
      <td>4</td>
      <td>23</td>
    </tr>
    <tr>
      <th>150436</th>
      <td>2</td>
      <td>2015-01-11 17:15:14</td>
      <td>2015-01-11 17:22:57</td>
      <td>1</td>
      <td>1,19</td>
      <td>None</td>
      <td>None</td>
      <td>-73,94</td>
      <td>40,71</td>
      <td>-73,95</td>
      <td>...</td>
      <td>0,3</td>
      <td>1,75</td>
      <td>0,00</td>
      <td>nBir</td>
      <td>9,55</td>
      <td>1,00</td>
      <td>1</td>
      <td>11</td>
      <td>6</td>
      <td>17</td>
    </tr>
    <tr>
      <th>432136</th>
      <td>2</td>
      <td>2015-01-22 23:16:33</td>
      <td>2015-01-22 23:20:13</td>
      <td>1</td>
      <td>0,65</td>
      <td>None</td>
      <td>None</td>
      <td>-73,94</td>
      <td>40,71</td>
      <td>-73,94</td>
      <td>...</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nBir</td>
      <td>6,30</td>
      <td>1,00</td>
      <td>1</td>
      <td>22</td>
      <td>3</td>
      <td>23</td>
    </tr>
  </tbody>
</table>
<p>10 satır × 27 sütun</p>
</div>

Eğitim veya ek özellik oluşturma için ihtiyaç duymayabilmeniz gereken bazı sütunları kaldırın.

```python
columns_to_remove = ["lpepPickupDatetime", "lpepDropoffDatetime", "puLocationId", "doLocationId", "extra", "mtaTax",
                     "improvementSurcharge", "tollsAmount", "ehailFee", "tripType", "rateCodeID",
                     "storeAndFwdFlag", "paymentType", "fareAmount", "tipAmount"
                    ]
for col in columns_to_remove:
    green_taxi_df.pop(col)

green_taxi_df.head(5)
```

### <a name="cleanse-data"></a>Verileri temizleme

Her bir alanın Özet istatistiklerini görmek için yeni veri çerçevesinde `describe()` işlevini çalıştırın.

```python
green_taxi_df.describe()
```

<div>
<style scoped>. dataframe tbody tr TH: yalnızca-of-type {Vertical-ALIGN: Middle;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Konağında VendorID</th>
      <th>passengerCount</th>
      <th>Üçlü mesafe</th>
      <th>Pickupboylam</th>
      <th>Pickupenlem</th>
      <th>Açılan Boylam</th>
      <th>Açılan Enlem</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
    </tr>
    <tr>
      <th>Ortası</th>
      <td>1,78</td>
      <td>1,37</td>
      <td>2,87</td>
      <td>-73,83</td>
      <td>40,69</td>
      <td>-73,84</td>
      <td>40,70</td>
      <td>14,75</td>
      <td>6,50</td>
      <td>15,13</td>
      <td>3,27</td>
      <td>13,52</td>
    </tr>
    <tr>
      <th>STD</th>
      <td>0,41</td>
      <td>1,04</td>
      <td>2,93</td>
      <td>2,76</td>
      <td>1,52</td>
      <td>2,61</td>
      <td>1,44</td>
      <td>12,08</td>
      <td>3,45</td>
      <td>8,45</td>
      <td>1,95</td>
      <td>6,83</td>
    </tr>
    <tr>
      <th>dk</th>
      <td>1,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>-74,66</td>
      <td>0,00</td>
      <td>-74,66</td>
      <td>0,00</td>
      <td>-300,00</td>
      <td>1,00</td>
      <td>1,00</td>
      <td>0,00</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>%25</th>
      <td>2,00</td>
      <td>1,00</td>
      <td>1,06</td>
      <td>-73,96</td>
      <td>40,70</td>
      <td>-73,97</td>
      <td>40,70</td>
      <td>7,80</td>
      <td>3,75</td>
      <td>8,00</td>
      <td>2,00</td>
      <td>9,00</td>
    </tr>
    <tr>
      <th>%50</th>
      <td>2,00</td>
      <td>1,00</td>
      <td>1,90</td>
      <td>-73,94</td>
      <td>40,75</td>
      <td>-73,94</td>
      <td>40,75</td>
      <td>11,30</td>
      <td>6,50</td>
      <td>15,00</td>
      <td>3,00</td>
      <td>15,00</td>
    </tr>
    <tr>
      <th>%75</th>
      <td>2,00</td>
      <td>1,00</td>
      <td>3,60</td>
      <td>-73,92</td>
      <td>40,80</td>
      <td>-73,91</td>
      <td>40,79</td>
      <td>17,80</td>
      <td>9,25</td>
      <td>22,00</td>
      <td>5,00</td>
      <td>19,00</td>
    </tr>
    <tr>
      <th>Biçimlendir</th>
      <td>2,00</td>
      <td>9,00</td>
      <td>97,57</td>
      <td>0,00</td>
      <td>41,93</td>
      <td>0,00</td>
      <td>41,94</td>
      <td>450,00</td>
      <td>12,00</td>
      <td>30,00</td>
      <td>6,00</td>
      <td>23,00</td>
    </tr>
  </tbody>
</table>
</div>


Özet istatistiklerinde, mantıksal veya daha fazla alan içeren birkaç alanın, model doğruluğunu azaltacak şekilde olduğunu görürsünüz. İlk olarak, Manhattan alanının sınırları dahilinde olacak şekilde Enlem/Long alanlarını filtreleyin. Bu, diğer özelliklerle ilişkisine göre, daha uzun bir süre içinde daha fazla vergilenme veya gezilerin filtreleneceği.

Ek olarak, `tripDistance` alanı sıfırdan büyük ancak 31 milden küçük (iki Lat/Long çifti arasındaki haversinüsü uzaklığı) olarak filtreleyin. Bu, tutarsız seyahat maliyeti olan uzun süreli döngüleri ortadan kaldırır.

Son olarak, `totalAmount` alanı taksi Fares için negatif değerler içerir ve bu, modelimizin bağlamında mantıklı değildir ve `passengerCount` alanı minimum değerleri sıfır olan hatalı verilere sahiptir.

Sorgu işlevlerini kullanarak bu anomali filtreleme yapın ve ardından eğitim için gereksiz son sütunu kaldırın.


```python
final_df = green_taxi_df.query("pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query("pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>=0.25 and tripDistance<31")
final_df = final_df.query("passengerCount>0 and totalAmount>0")

columns_to_remove_for_training = ["pickupLongitude", "pickupLatitude", "dropoffLongitude", "dropoffLatitude"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

Temizleşildiği gibi çalıştığından emin olmak için verileri yeniden `describe()` çağırın. Artık makine öğrenimi model eğitimi için kullanılacak, hazırlanmış ve yeniden hazırlanan bir TAXI, tatil ve hava durumu verisi ayarlamış olursunuz.

```python
final_df.describe()
```

## <a name="configure-workspace"></a>Çalışma alanını yapılandırma

Mevcut çalışma alanından bir çalışma alanı nesnesi oluşturun. [Çalışma alanı](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) , Azure aboneliğinizi ve kaynak bilgilerinizi kabul eden bir sınıftır. Ayrıca, modelinizi izlemek ve izlemek için bir bulut kaynağı oluşturur. `Workspace.from_config()`, **config. JSON** dosyasını okur ve kimlik doğrulama ayrıntılarını `ws`adlı bir nesneye yükler. Bu öğreticideki kodun kalanında `ws` kullanılır.

```python
from azureml.core.workspace import Workspace
ws = Workspace.from_config()
```

## <a name="split-the-data-into-train-and-test-sets"></a>Verileri eğitme ve test kümelerine Böl

`scikit-learn` kitaplığındaki `train_test_split` işlevini kullanarak verileri eğitim ve test kümelerine ayırın. Bu işlev, model eğitimi için verileri x (**Özellikler**) veri kümesine ayırır ve test için y (**tahmin edilecek değerler**) veri kümesini gösterir.

`test_size` parametresi, teste ayrılacak verilerin yüzdesini belirler. `random_state` parametresi, tren test bölünmeleri belirleyici olacak şekilde rastgele oluşturucuya bir çekirdek ayarlar.

```python
from sklearn.model_selection import train_test_split

y_df = final_df.pop("totalAmount")
x_df = final_df

x_train, x_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=223)
```

Bu adımın amacı, doğru doğruluğu ölçmek için modeli eğmekte kullanılmamış olan tamamlanmış modeli test etmek için veri noktalarına sahip değildir.

Diğer bir deyişle, iyi eğitilen bir modelin zaten görmemiş verilerden tahmine dayalı hale getirme yapabilmesi gerekir. Artık, bir makine öğrenimi modeline otomatik eğitim için hazırlanan veriler vardır.

## <a name="automatically-train-a-model"></a>Bir modeli otomatik olarak eğitme

Bir modeli otomatik olarak eğitmek için aşağıdaki adımları uygulayın:
1. Deneme çalıştırmasının ayarlarını tanımlayın. Eğitim verilerinizi yapılandırmaya ekleyin ve eğitim sürecini denetleyen ayarları değiştirin.
1. Model ayarlama için denemeyi gönder. Deneme gönderdikten sonra işlem, farklı makine öğrenimi algoritmalarını ve hiper parametre ayarlarını kullanarak, tanımlı kısıtlamalarınız için uygun şekilde yinelenir. Doğruluk ölçüsünü en iyi duruma getirerek en uygun modeli seçer.

### <a name="define-training-settings"></a>Eğitim ayarlarını tanımlama

Eğitim için deneme parametresi ve model ayarlarını tanımlayın. [Ayarların](how-to-configure-auto-train.md)tam listesini görüntüleyin. Denemeyi bu varsayılan ayarlarla göndermek yaklaşık 5-20 dakika sürer, ancak daha kısa bir çalışma süresi istiyorsanız `experiment_timeout_minutes` parametresini küçültün.

|Özellik| Bu öğreticideki değer |Açıklama|
|----|----|---|
|**iteration_timeout_minutes**|2|Her yineleme için dakika cinsinden zaman sınırı. Toplam çalışma zamanını azaltmak için bu değeri küçültün.|
|**experiment_timeout_minutes**|20|Deneme sona ermeden önce tüm yinelemelerin birleştirilebilmesi için geçmesi gereken en uzun süre.|
|**enable_early_stopping**|True|Puan, kısa vadede iyileştirilmediğinden erken sonlandırma için bayrak.|
|**primary_metric**| spearman_correlation | İyileştirmek istediğiniz ölçüm. En uygun model bu ölçüme göre seçilecek.|
|**korturlama**| Otomatik | **Otomatik**, deneme kullanarak giriş verilerini önceden işleyebilir (eksik verileri işleme, metni sayısal olarak dönüştürme vb.)|
|**ayrıntı**| logging.INFO | Günlüğe kaydetme düzeyini denetler.|
|**n_cross_validations**|5|Doğrulama verileri belirtilmediğinde gerçekleştirilecek çapraz doğrulama bölme sayısı.|

```python
import logging

automl_settings = {
    "iteration_timeout_minutes": 2,
    "experiment_timeout_minutes": 20,
    "enable_early_stopping": True,
    "primary_metric": 'spearman_correlation',
    "featurization": 'auto',
    "verbosity": logging.INFO,
    "n_cross_validations": 5
}
```

Tanımlı eğitim ayarlarınızı bir `AutoMLConfig` nesnesine `**kwargs` parametresi olarak kullanın. Ayrıca, eğitim verilerinizi ve model türünü belirtin, bu durumda `regression`.

```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             X=x_train.values,
                             y=y_train.values.flatten(),
                             **automl_settings)
```

> [!NOTE]
> Otomatik makine öğrenimi ön işleme adımları (özellik normalleştirme, eksik verileri işleme, metni sayısal olarak dönüştürme, vb.) temel modelin bir parçası haline gelir. Tahmin için model kullanılırken, eğitim sırasında uygulanan aynı ön işleme adımları, giriş verilerinize otomatik olarak uygulanır.

### <a name="train-the-automatic-regression-model"></a>Otomatik regresyon modelini eğitme

Çalışma alanınızda bir deneme nesnesi oluşturun. Deneme, bireysel çalışmalarınız için bir kapsayıcı olarak davranır. Tanımlanan `automl_config` nesnesini denemenize geçirin ve çalıştırma sırasında ilerlemeyi görüntülemek için çıktıyı `True` olarak ayarlayın.

Denemeyi başlattıktan sonra, bu gösterilen çıkış, deney olarak canlı güncelleştirmeler çalışır. Her yineleme için model türünü, çalışma süresini ve eğitim doğruluğunu görürsünüz. Alan `BEST` ölçüm türünüz temelinde en iyi çalışan eğitim puanı ' nı izler.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, "taxi-experiment")
local_run = experiment.submit(automl_config, show_output=True)
```

    Running on local machine
    Parent Run ID: AutoML_1766cdf7-56cf-4b28-a340-c4aeee15b12b
    Current status: DatasetFeaturization. Beginning to featurize the dataset.
    Current status: DatasetEvaluation. Gathering dataset statistics.
    Current status: FeaturesGeneration. Generating features for the dataset.
    Current status: DatasetFeaturizationCompleted. Completed featurizing the dataset.
    Current status: DatasetCrossValidationSplit. Generating individually featurized CV splits.
    Current status: ModelSelection. Beginning model selection.

    ****************************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ****************************************************************************************************

     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   StandardScalerWrapper RandomForest             0:00:16       0.8746    0.8746
             1   MinMaxScaler RandomForest                      0:00:15       0.9468    0.9468
             2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.9303    0.9468
             3   StandardScalerWrapper LightGBM                 0:00:10       0.9424    0.9468
             4   RobustScaler DecisionTree                      0:00:09       0.9449    0.9468
             5   StandardScalerWrapper LassoLars                0:00:09       0.9440    0.9468
             6   StandardScalerWrapper LightGBM                 0:00:10       0.9282    0.9468
             7   StandardScalerWrapper RandomForest             0:00:12       0.8946    0.9468
             8   StandardScalerWrapper LassoLars                0:00:16       0.9439    0.9468
             9   MinMaxScaler ExtremeRandomTrees                0:00:35       0.9199    0.9468
            10   RobustScaler ExtremeRandomTrees                0:00:19       0.9411    0.9468
            11   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9077    0.9468
            12   StandardScalerWrapper LassoLars                0:00:15       0.9433    0.9468
            13   MinMaxScaler ExtremeRandomTrees                0:00:14       0.9186    0.9468
            14   RobustScaler RandomForest                      0:00:10       0.8810    0.9468
            15   StandardScalerWrapper LassoLars                0:00:55       0.9433    0.9468
            16   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9026    0.9468
            17   StandardScalerWrapper RandomForest             0:00:13       0.9140    0.9468
            18   VotingEnsemble                                 0:00:23       0.9471    0.9471
            19   StackEnsemble                                  0:00:27       0.9463    0.9471

## <a name="explore-the-results"></a>Sonuçları inceleme

[Jupyıter pencere öğesiyle](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)otomatik eğitime ilişkin sonuçları keşfet. Pencere öğesi, eğitim doğruluğu ölçümleri ve meta verileri ile birlikte tüm bireysel çalıştırma yinelemelerini bir grafik ve tablo görmenizi sağlar. Ayrıca, açılan seçiciyle, birincil ölçümünüzün farklı doğruluk ölçümlerine filtre uygulayabilirsiniz.

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

Jupyıter pencere öğesi ![](./media/tutorial-auto-train-models/automl-dash-output.png)
jupi pencere öğesi çizimi ![](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="retrieve-the-best-model"></a>En iyi modeli alma

Yinelemeinizden en iyi modeli seçin. `get_output` işlevi en iyi çalışmayı ve en son çağrıya yönelik olarak bulunan modeli döndürür. `get_output`üzerindeki aşırı yüklemeleri kullanarak, herhangi bir günlüğe kaydedilmiş ölçüm veya belirli bir yineleme için en iyi çalışma ve bağlı modeli alabilirsiniz.

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

### <a name="test-the-best-model-accuracy"></a>En iyi model doğruluğunu test edin

Taksi Fares 'yi tahmin etmek için test verileri kümesindeki tahminleri çalıştırmak için en iyi modeli kullanın. İşlevi `predict` en iyi modeli kullanır ve `x_test` veri kümesinden y, **seyahat maliyeti**değerlerini tahmin eder. İlk 10 tahmini maliyet değerini `y_predict`yazdır.

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

Sonuçların `root mean squared error` hesaplayın. Tahmin edilen değerlerle karşılaştırmak için `y_test` veri çerçevesini bir listeye dönüştürün. İşlevi `mean_squared_error` iki dizi değer alır ve aralarındaki ortalama kare içinde hata sayısını hesaplar. Sonucun kare kökünü almak, y **değişkeni ile aynı**birimlerde bir hata verir. EPI tarifeli havayolu tahminlerinin gerçek farlarından ne kadar olduğunu kabaca gösterir.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Tam `y_actual` ve `y_predict` veri kümelerini kullanarak ortalama mutlak yüzde hatası (MAPE) hesaplamak için aşağıdaki kodu çalıştırın. Bu ölçüm, tahmin edilen ve gerçek değerler arasındaki mutlak bir farkı hesaplar ve tüm farkları toplar. Ardından, bu toplamı gerçek değerlerin toplamının yüzdesi olarak ifade eder.

```python
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals
print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

    Model MAPE:
    0.14353867606052823

    Model Accuracy:
    0.8564613239394718


İki tahmin doğruluk ölçümlerinde, modelin, veri kümesinin özelliklerinden, genellikle +-$4,00 ' de ve yaklaşık %15 ' te vergilenme Fareli tahminlerinde oldukça iyi olduğunu görürsünüz.

Geleneksel makine öğrenimi modeli geliştirme süreci, yoğun kaynak yoğunluğu sağlar ve çok sayıda modelin sonuçlarını çalıştırmak ve karşılaştırmak için önemli etki alanı bilgisi ve zaman yatırımı gerektirir. Otomatik makine öğrenimi kullanmak, senaryonuza yönelik birçok farklı modeli hızlıca test etmenin harika bir yoludur.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Diğer Azure Machine Learning öğreticileri çalıştırmayı planlıyorsanız, bu bölümü tamamlamayın.

### <a name="stop-the-notebook-vm"></a>Not defteri VM 'sini durdur

[!INCLUDE [aml-stop-server](../../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Her şeyi sil

Oluşturduğunuz kaynakları kullanmayı planlamıyorsanız, herhangi bir ücret ödemezsiniz.

1. Azure portalının en sol tarafındaki **Kaynak gruplarını** seçin.
1. Listeden oluşturduğunuz kaynak grubunu seçin.
1. **Kaynak grubunu sil**'i seçin.
1. Kaynak grubu adını girin. Ardından **Sil**’i seçin.

Ayrıca, kaynak grubunu koruyabilir ancak tek bir çalışma alanını silebilirsiniz. Çalışma alanı özelliklerini görüntüleyin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu otomatik makine öğrenimi öğreticisinde aşağıdaki görevleri yaptınız:

> [!div class="checklist"]
> * Bir deneme için çalışma alanı ve hazırlanan veriler yapılandırıldı.
> * Özel parametrelerle yerel olarak otomatik regresyon modeli kullanılarak eğitilen.
> * Araştırılan ve gözden geçirilmiş eğitim sonuçları.

[Modelinizi](tutorial-deploy-models-with-aml.md) Azure Machine Learning ile dağıtın.
