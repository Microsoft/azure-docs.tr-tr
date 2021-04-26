---
title: Sorguları işaretlemek için etiketleri kullanma
description: Azure SYNAPSE Analytics 'te adanmış SQL havuzları için sorguları işaretlemek üzere etiketleri kullanma ipuçları.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 7958caf71658dcdcbf31bac84697931e9049452f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462750"
---
# <a name="using-labels-to-instrument-queries-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış SQL havuzlarının sorgularını işaretlemek için etiketleri kullanma

Bu makalede, adanmış SQL havuzlarındaki sorguları işaretlemek için Etiketler kullanarak çözüm geliştirmeye yönelik ipuçları yer almaktadır.

## <a name="what-are-labels"></a>Etiketler nedir?

Adanmış SQL havuzu sorgu etiketleri adlı bir kavramı destekler. Herhangi bir derinliğe geçmeden önce bir örneğe göz atalım:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Son çizgi ' My Query Tag ' dizesini sorguya etiketleyin. Etiket, DMVs aracılığıyla sorgu özellikli olduğundan bu etiket yararlıdır.

Etiketleri sorgulamak, sorun sorgularını bulmaya ve bir ELT çalıştırması aracılığıyla ilerlemeyi belirlemesine yardımcı olmaya yönelik bir mekanizma sağlar.

İyi bir adlandırma kuralı aslında yardımcı olur. Örneğin, etiketi PROJECT, PROCEDURE, DEYIMLE ve açıklama ile başlatmak, sorguyu kaynak denetimindeki tüm kodlar arasında benzersiz şekilde tanımlar.

Aşağıdaki sorgu, etikete göre aramak için dinamik bir yönetim görünümü kullanır:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Sorgulama sırasında, sözcük etiketinin çevresine köşeli ayraç veya çift tırnak koymak gereklidir. Etiket, ayrılmış bir sözcüktür ve sınırlandırılmamış bir hata oluşmasına neden olur.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla geliştirme ipucu için bkz. [geliştirmeye genel bakış](sql-data-warehouse-overview-develop.md).
