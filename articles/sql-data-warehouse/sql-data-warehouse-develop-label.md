---
title: Sorguları işaretlemek için etiketleri kullanma
description: Azure SQL veri ambarı 'nda çözüm geliştirmeye yönelik sorguları işaretlemek için Etiketler kullanma ipuçları.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 4ca0b3564418aafe774158057bc3efd541f71f66
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692826"
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Azure SQL veri ambarı 'nda sorguları işaretlemek için etiketleri kullanma
Azure SQL veri ambarı 'nda çözüm geliştirmeye yönelik sorguları işaretlemek için Etiketler kullanma ipuçları.


## <a name="what-are-labels"></a>Etiketler nedir?
SQL veri ambarı, sorgu etiketleri adlı bir kavramı destekler. Herhangi bir derinliğe geçmeden önce bir örneğe göz atalım:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Son çizgi ' My Query Tag ' dizesini sorguya etiketleyin. Etiket, DMVs aracılığıyla sorgu özellikli olduğundan bu etiket özellikle yararlıdır. Etiketleri sorgulamak, sorun sorgularını bulmaya ve bir ELT çalıştırması aracılığıyla ilerlemeyi belirlemesine yardımcı olmaya yönelik bir mekanizma sağlar.

İyi bir adlandırma kuralı aslında yardımcı olur. Örneğin, etiketi PROJECT, PROCEDURE, DEYIMLE ve açıklama ile başlatmak, sorguyu kaynak denetimindeki tüm kodlar arasında benzersiz şekilde tanımlamanızı sağlar.

Aşağıdaki sorgu, etikete göre arama yapmak için dinamik bir yönetim görünümü kullanır.

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Sorgulama sırasında, sözcük etiketinin çevresine köşeli ayraç veya çift tırnak koymak gereklidir. Etiket, ayrılmış bir sözcüktür ve sınırlandırılmamış bir hata oluşmasına neden olur. 
> 
> 

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla geliştirme ipucu için bkz. [geliştirmeye genel bakış](sql-data-warehouse-overview-develop.md).


