---
title: Azure Cosmos DB sorgu dilinde GetCurrentDateTime
description: Azure Cosmos DB 'de SQL sistem işlevi GetCurrentDateTime hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8a2c3dcd3c8ca6dc9d751e50a7862fe98e6de510
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351030"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
 ISO 8601 dizesi olarak geçerli UTC (Eşgüdümlü Evrensel Saat) Tarih ve saati döndürür.
  
## <a name="syntax"></a>Sözdizimi
  
```sql
GetCurrentDateTime ()
```
  
## <a name="return-types"></a>Dönüş türleri
  
  Şu biçimdeki geçerli UTC Tarih ve saat ISO 8601 dize değerini döndürür `YYYY-MM-DDThh:mm:ss.sssZ`:
  
  |||
  |-|-|
  |YYYY|dört basamaklı yıl|
  |MM|iki basamaklı ay (01 = Ocak, vb.)|
  |GG|iki basamaklı ayın günü (01 ile 31 arasında)|
  |T|zaman öğelerinin başlangıcı için signifier|
  |ss|iki basamaklı saat (00 ile 23 arasında)|
  |d|iki basamaklı dakika (00 ila 59)|
  |ss|iki basamaklı saniye (00 ila 59)|
  |. SSS|saniyenin üç basamaklı ondalık kesirleri|
  |KADAR|UTC (Eşgüdümlü Evrensel Saat) göstergesi||
  
  ISO 8601 biçimi hakkında daha fazla bilgi için bkz. [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Açıklamalar

  GetCurrentDateTime () belirleyici olmayan bir işlevdir. 
  
  Döndürülen sonuç UTC 'dir.

## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, GetCurrentDateTime () yerleşik işlevini kullanarak geçerli UTC Tarih zamanının nasıl alınacağını gösterir.
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Örnek bir sonuç kümesi aşağıda verilmiştir.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.784Z"
}]  
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Tarih ve saat işlevleri Azure Cosmos DB](sql-query-date-time-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
