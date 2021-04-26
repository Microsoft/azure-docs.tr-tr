---
title: Tüm veri kümeleri API 'sini al
description: Ticari Market Analytics için tüm kullanılabilir veri kümelerini almak üzere bu API 'yi kullanın.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 23aac2c94ffd909ca132cbc481998b9eda317156
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584176"
---
# <a name="get-all-datasets-api"></a>Tüm veri kümeleri API 'sini al

Tüm veri kümelerini al API 'SI kullanılabilir tüm veri kümelerini alır. Veri kümeleri tabloları, sütunları, ölçümleri ve zaman aralıklarını listeler.

**İstek sözdizimi**

| **Yöntem** | **İstek URI'si** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset?datasetName={Dataset Name}` |

**İstek üst bilgisi**

| **Üst bilgi** | **Tür** | **Açıklama** |
| --- | --- | --- |
| Yetkilendirme | string | Gereklidir. Formdaki Azure Active Directory (Azure AD) erişim belirteci `Bearer <token>` |
| İçerik Türü | string | `Application/JSON` |

**Yol parametresi**

Yok

**Sorgu parametresi**

| **Parametre adı** | **Tür** | **Gerekli** | **Açıklama** |
| --- | --- | --- | --- |
| `datasetName` | dize | No | Yalnızca bir veri kümesinin ayrıntılarını almak için filtrele |

**İstek yükü**

Yok

**Sözlük**

Yok

**Response**

Yanıt yükü aşağıdaki şekilde yapılandırılır:

Yanıt kodu: 200, 400, 401, 403, 404, 500

Yanıt yükü örneği:

```json
{
   "Value":[
      {
         "DatasetName ":"string",
         "SelectableColumns":[
            "string"
         ],
         "AvailableMetrics":[
            "string"
         ],
         "AvailableDateRanges ":[
            "string"
         ]
      }
   ],
   "TotalCount":int,
   "Message":"<Error Message>",
   "StatusCode": int
}
```

**Sözlük**

Bu tablo, yanıttaki anahtar öğeleri tanımlar:

| **Parametre** | **Açıklama** |
| --- | --- |
| `DatasetName` | Bu dizi nesnesinin tanımladığı veri kümesinin adı |
| `SelectableColumns` | Seçme sütunlarında belirtilenebilir ham sütunlar |
| `AvailableMetrics` | Seçme sütunlarında belirtilenebilir toplama/ölçüm sütun adları |
| `AvailableDateRanges` | Veri kümesi için rapor sorgularında kullanılabilecek tarih aralığı |
| `NextLink` | Veriler sayfalandırılır ise sonraki sayfaya bağlantı |
| `TotalCount` | Değer dizisindeki veri kümesi sayısı |
| `StatusCode` | Sonuç kodu. Olası değerler 200, 400, 401, 403, 500 |
