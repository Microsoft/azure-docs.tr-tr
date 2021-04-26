---
title: Metin Analizi v3 'de model sürümünü belirtin
titleSuffix: Azure Cognitive Services
description: Verilerinizde kullanılan Metin Analizi API'si modelini belirtmeyi öğrenin.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/17/2021
ms.author: aahi
ms.openlocfilehash: ee3a56fed87f493d79796c0d91e5324c94e87eca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599366"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>Metin Analizi API'si model sürümü oluşturma

Metin Analizi API'si sürüm 3, verilerinizde kullanılan model sürümünü seçmenizi sağlar. `model-version`API isteklerinizin modelin sürümünü seçmek için isteğe bağlı parametresini kullanın. Örneğin: `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`. Bu parametre belirtilmemişse, API varsayılan olarak en son kararlı sürüme sahip olur. 

## <a name="available-versions"></a>Kullanılabilir sürümler

Her barındırılan uç nokta tarafından hangi model sürümlerinin desteklendiğini bulmak için aşağıdaki tabloyu kullanın.


| Uç Nokta                        | Desteklenen Sürümler                                     | en son sürüm |
|---------------------------------|--------------------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`                             | `2020-04-01`   |
| `/languages`                    | `2019-10-01`, `2020-07-01`, `2020-09-01`, `2021-01-05` | `2021-01-05`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`                             | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2021-01-15`  | `2021-01-15`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2020-07-01`, `2021-01-15`  | `2021-01-15`   |
| `/entities/health`              | `2021-03-01`                           | `2021-03-01`   |
| `/keyphrases`                   | `2019-10-01`, `2020-07-01`                             | `2020-07-01`   |


Bu modellerin güncelleştirmeleriyle ilgili ayrıntıları, [Yenilikler bölümünde bulabilirsiniz](../whats-new.md).

## <a name="text-analytics-for-health"></a>Sistem durumu için Metin Analizi

[Sistem durumu kapsayıcısı için metin analizi](../how-tos/text-analytics-for-health.md) , yukarıdaki API uç noktalarından farklı model sürümü kullanır.  Kapsayıcı görüntüsü başına yalnızca bir model sürümünün kullanılabilir olduğunu lütfen unutmayın.

| Uç Nokta                        | Kapsayıcı görüntüsü etiketi                     | Model sürümü |
|---------------------------------|-----------------------------------------|---------------|
| `/entities/health`              | `3.0.015370001-onprem-amd64` veya en son          | `2021-03-01`  |
| `/entities/health`              | `1.1.013530001-amd64-preview`           | `2020-09-03`  |
| `/entities/health`              | `1.1.013150001-amd64-preview`           | `2020-07-24`  |
| `/domains/health`               | `1.1.012640001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |


## <a name="next-steps"></a>Sonraki adımlar

* [Metin Analizine genel bakış](../overview.md)
* [Yaklaşım analizi](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Varlık tanıma](../how-tos/text-analytics-how-to-entity-linking.md)
