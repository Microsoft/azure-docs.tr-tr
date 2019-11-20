---
title: NOAA verileri kullanan örnek jupi Not defterleri
titleSuffix: Azure Open Datasets
description: Açık veri kümelerini nasıl yükleyeceğinizi ve tanıtım verilerini zenginleştirmek için bunları nasıl kullanacağınızı öğrenmek için Azure açık veri kümeleri için örnek jupi not defterlerini kullanın. Teknikler, verileri işlemek için Spark ve Pandas kullanımını içerir.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 11/04/2019
ms.openlocfilehash: 8b96a35db91a282be1fb5e4c6143e6bd0a0203f2
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606135"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>Örnek jupi Not defterleri açık veri kümeleriyle verilerin nasıl zenginleştiralınacağını gösterir 
Azure açık veri kümelerine yönelik örnek jupi Not defterleri, açık veri kümelerinin nasıl yükleneceğini ve bunları zenginleştirmek için bunları nasıl kullanacağınızı gösterir. Teknikler, verileri işlemek için Apache Spark ve Pandas kullanımını içerir.

>[!IMPORTANT]
>Spark olmayan bir ortamda çalışırken, açık veri kümeleri, büyük veri kümeleriyle MemoryError önlemek için belirli sınıflarla tek seferde yalnızca bir ay indirmeyi sağlar.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>NOAA tümleşik yüzey veritabanı (ICD) verilerini yükle 
|Not Defteri        | Açıklama                                    |
|----------------|------------------------------------------------|
|[Hava durumu verilerinin son ayını bir Pandas dataframe 'e yükleme](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | Geçmiş hava durumu verilerini en sevdiğiniz Pandas dataframe 'e yüklemeyi öğrenin. |
|[Bir Spark veri çerçevesine en son bir hava durumu verileri yükleme](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | Geçmiş hava durumu verilerini en sevdiğiniz Spark dataframe 'e yüklemeyi öğrenin.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>NOAA ISD verileri ile tanıtım verilerini birleştirin 
|Not Defteri        | Açıklama                                    |
|----------------|------------------------------------------------|
|[Tanıtım verileri ile hava durumu verilerini birleştirin-Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | Bir Pandas dataframe 'teki Hava durumu okumalarıyla algılayıcı konumlarının 1 aylık tanıtım veri kümesine katın.  |
|[Hava durumu verileriyle tanıtım verileri ekleme – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | Bir Spark veri çerçevesindeki Hava durumu okumalarıyla algılayıcı konumlarının tanıtım veri kümesini birleştirin. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>NOAA ıMD verileri ile NYC TAXI verilerini birleştirin 
|Not Defteri        | Açıklama                                    |
|----------------|------------------------------------------------|
|[Vergilenme verileri hava durumu verileri ile zenginleştirdim-Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | NYC yeşil TAXI verilerini yükleyin (1 aydan fazla) ve bir Pandas dataframe içindeki hava durumu verileriyle zenginleştirin. Bu örnek, `get_pandas_limit` ve veri yükü performansını veri miktarıyla dengeleyen yöntemi geçersiz kılar.|
|[Vergilenme verileri hava durumu verileriyle zenginleştirir – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | Spark dataframe 'de NYC yeşil TAXI verilerini yükleyin ve hava durumu verileriyle zenginleştirin.  |

## <a name="next-steps"></a>Sonraki adımlar

* [Öğretici: otomatik makine öğrenimi ve açık bir veri kümesi ile gerileme modelleme](/azure/machine-learning/service/tutorial-auto-train-models?context=azure/open-datasets/context/open-datasets-context)
* [Açık veri kümeleri için Python SDK](/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)
* [Azure açık veri kümeleri kataloğu](https://azure.microsoft.com/services/open-datasets/catalog/)
