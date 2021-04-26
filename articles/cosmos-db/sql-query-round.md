---
title: Azure Cosmos DB sorgu dilinde yuvarla
description: Azure Cosmos DB SQL sistem işlevi YUVARLA hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7dc4d78f7af1086f9a4de9aa7392acb388df966e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104590679"
---
# <a name="round-azure-cosmos-db"></a>YUVARLAK (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 En yakın tamsayı değerine yuvarlanmış sayısal bir değer döndürür.  
  
## <a name="syntax"></a>Söz dizimi
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız değişkenler
  
*numeric_expr*  
   Sayısal bir ifadedir.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Sayısal bir ifade döndürür.  
  
## <a name="remarks"></a>Açıklamalar
  
Yuvarlama işlemi, sıfırdan uzağa yuvarlanan orta nokta yuvarlama işlemini gerçekleştirdi. Giriş iki tamsayı arasında tam olarak denk gelen sayısal bir ifadesiyse sonuç, sıfırdan bir en yakın tamsayı değeri olacaktır. Bu sistem işlevi, bir [Aralık dizininden](index-policy.md#includeexclude-strategy)faydalanır.
  
|<numeric_expr>|Yuvarlanır|
|-|-|
|-6,5000|-7|
|-0,5|-1|
|0,5|1|
|6,5000|7|
  
## <a name="examples"></a>Örnekler
  
Aşağıdaki örnek, aşağıdaki pozitif ve negatif sayıları en yakın tamsayıya yuvarlar.  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
Sonuç kümesini burada bulabilirsiniz.  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Matematik işlevleri Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB'ye giriş](introduction.md)
