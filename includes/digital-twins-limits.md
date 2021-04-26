---
author: baanders
description: Azure dijital TWINS sınırları için dosya ekleme
ms.service: digital-twins
ms.topic: include
ms.date: 4/8/2021
ms.author: baanders
ms.openlocfilehash: 34fec713c3764987f07bc7fb89ecb0a0d770a840
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728033"
---
### <a name="functional-limits"></a>İşlevsel sınırlar

Aşağıdaki tabloda Azure dijital TWINS 'in işlevsel sınırları listelenmektedir. 

> [!TIP]
> Bu işlev sınırları içinde çalışacak modelleme önerileri için bkz. [model tasarlamak Için en iyi uygulamalar](../articles/digital-twins/concepts-models.md#best-practices-for-designing-models).

| Alan | Özellik | Varsayılan limit | Ayarlanabilir? |
| --- | --- | --- | --- |
| Azure kaynağı | Bir bölgedeki Azure dijital TWINS örneği sayısı, abonelik başına | 10 | Yes |
| Digital Twins | Bir Azure dijital TWINS örneğindeki TWINS sayısı | 200,000 | Yes |
| Digital Twins | Tek bir ikizi gelen ilişki sayısı | 5.000 | No |
| Digital Twins | Tek bir ikizi giden ilişki sayısı | 5.000 | No |
| Digital Twins | Tek bir ikizi en büyük boyut (PUT veya PATCH isteğindeki JSON gövdesi) | 32 KB | No |
| Digital Twins | Maksimum istek yükü boyutu | 32 KB | No | 
| Yönlendirme | Tek bir Azure dijital TWINS örneği için uç nokta sayısı | 6 | No |
| Yönlendirme | Tek bir Azure dijital TWINS örneği için yol sayısı | 6 | Yes |
| Modeller | Tek bir Azure dijital TWINS örneği içindeki model sayısı | 10,000 | Yes |
| Modeller | Tek bir API çağrısında karşıya yüklenebilen model sayısı | 250 | No |
| Modeller | Tek bir modelin en büyük boyutu (PUT veya PATCH isteğindeki JSON gövdesi) | 1 MB | No |
| Modeller | Tek bir sayfada döndürülen öğelerin sayısı | 100 | No |
| Sorgu | Tek bir sayfada döndürülen öğelerin sayısı | 100 | Yes |
| Sorgu | `AND`  /  `OR` Sorgudaki ifade sayısı | 50 | Yes |
| Sorgu | Bir `IN`  /  `NOT IN` yan tümcedeki dizi öğelerinin sayısı | 50 | Yes |
| Sorgu | Sorgudaki karakter sayısı | 8,000 | Yes |
| Sorgu | Sorgudaki sayı `JOINS` | 5 | Yes |

### <a name="rate-limits"></a>Hız sınırları

Aşağıdaki tablo, farklı API 'lerin hız sınırlarını yansıtır.

| API | Özellik | Varsayılan limit | Ayarlanabilir? |
| --- | --- | --- | --- |
| Modeller API 'SI | Saniye başına istek sayısı | 100 | Yes |
| Dijital TWINS API 'SI | Saniye başına okuma isteği sayısı | 1.000 | Yes |
| Dijital TWINS API 'SI | Saniyedeki düzeltme isteği sayısı | 1.000 | Yes |
| Dijital TWINS API 'SI | **Tüm TWINS ve ilişkiler** genelinde saniye başına oluşturma/silme işlemi sayısı | 50 | Yes |
| Dijital TWINS API 'SI | **Tek bir ikizi** veya onun ilişkilerinde saniye başına oluşturma/güncelleştirme/silme işlemi sayısı | 10 | No |
| Sorgu API'si | Saniye başına istek sayısı | 500 | Yes |
| Sorgu API'si | Saniye başına [sorgu birimi](../articles/digital-twins/concepts-query-units.md) | 4.000 | Yes |
| Olay rotaları API 'SI | Saniye başına istek sayısı | 100 | Yes |

### <a name="other-limits"></a>Diğer sınırlar

Azure Digital TWINS modellerine yönelik DTDL belgelerindeki veri türleri ve alanları için sınırlamalar, GitHub: [*dijital TWINS tanım dili (DTDL)-sürüm 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)' deki kendi Özellikler belgelerinde bulunabilir.
 
Sorgu gecikmesi ayrıntıları ve diğer sorgu sınırlamaları [*, nasıl yapılır: ikizi grafiğini sorgulama*](../articles/digital-twins/how-to-query-graph.md)bölümünde bulunabilir.
