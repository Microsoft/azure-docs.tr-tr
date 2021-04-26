---
title: Azure Cosmos DB sorgu dilinde GetCurrentDateTime
description: Azure Cosmos DB 'de SQL sistem işlevi GetCurrentDateTime hakkında bilgi edinin.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 12ce8beab082674cd7672713325d4b3f4322aeae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587313"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

ISO 8601 dizesi olarak geçerli UTC (Eşgüdümlü Evrensel Saat) Tarih ve saati döndürür.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentDateTime ()
```

## <a name="return-types"></a>Dönüş türleri
  
Şu biçimdeki geçerli UTC Tarih ve saat ISO 8601 dize değerini döndürür `YYYY-MM-DDThh:mm:ss.fffffffZ` :
  
|Biçimlendir|Açıklama|
|-|-|
|YYYY|dört basamaklı yıl|
|MM|iki basamaklı ay (01 = Ocak, vb.)|
|DD|iki basamaklı ayın günü (01 ile 31 arasında)|
|T|zaman öğelerinin başlangıcı için signifier|
|hh|iki basamaklı saat (00 ile 23 arasında)|
|mm|iki basamaklı dakika (00 ila 59)|
|ss|iki basamaklı saniyeler (00 ila 59)|
|. fffffff|yedi basamaklı kesirli saniye|
|Z|UTC (Eşgüdümlü Evrensel Saat) göstergesi|
  
  ISO 8601 biçimi hakkında daha fazla bilgi için bkz. [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Açıklamalar

GetCurrentDateTime () belirleyici olmayan bir işlevdir. Döndürülen sonuç UTC 'dir. Duyarlık, 100 nanosaniye değeriyle doğru 7 haneye sahiptir.

> [!NOTE]
> Bu sistem işlevi dizinden yararlanmayacak. Değerleri geçerli saate göre karşılaştırmak gerekirse, sorgu yürütmeden önce geçerli zamanı alın ve yan tümcesinde bu sabit dize değerini kullanın `WHERE` .

## <a name="examples"></a>Örnekler
  
Aşağıdaki örnek, GetCurrentDateTime () yerleşik işlevini kullanarak geçerli UTC Tarih zamanının nasıl alınacağını gösterir.
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Örnek bir sonuç kümesi aşağıda verilmiştir.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Tarih ve saat işlevleri Azure Cosmos DB](sql-query-date-time-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB'ye giriş](introduction.md)
