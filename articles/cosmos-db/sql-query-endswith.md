---
title: Azure Cosmos DB sorgu dilinde EndsWith
description: İlk dize ifadesinin ikinci ile bitip bitmediğini gösteren bir Boole değeri döndürmek için Azure Cosmos DB içindeki ENDSWITH SQL System işlevi hakkında bilgi edinin
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c0cc93fee8aacc711a797925cb2e2808b73cafd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93338841"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

İlk dize ifadesinin ikinciyle sonlanıp bitmediğini gösteren bir Boole değeri döndürür.  
  
## <a name="syntax"></a>Söz dizimi
  
```sql
ENDSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])
```  
  
## <a name="arguments"></a>Bağımsız değişkenler
  
*str_expr1*  
   Bir dize ifadesidir.  
  
*str_expr2*  
   *Str_expr1* sonuna kıyasla bir dize ifadesi.

*bool_expr* Büyük/küçük harf yoksayma için isteğe bağlı değer. True olarak ayarlandığında, ENDSWITH, büyük/küçük harfe duyarsız bir arama yapılır. Belirtilmediğinde, bu değer false 'tur.
  
## <a name="return-types"></a>Dönüş türleri
  
  Boole ifadesi döndürür.  
  
## <a name="examples"></a>Örnekler
  
Aşağıdaki örnek, "abc" dizesinin "b" ve "bC" ile bitip bitmediğini denetler.  
  
```sql
SELECT ENDSWITH("abc", "b", false) AS e1, ENDSWITH("abc", "bC", false) AS e2, ENDSWITH("abc", "bC", true) AS e3
```  
  
 Sonuç kümesini burada bulabilirsiniz.  
  
```json
[
    {
        "e1": false,
        "e2": false,
        "e3": true
    }
]
```  

## <a name="remarks"></a>Açıklamalar

Bu sistem işlevi, bir [Aralık dizininden](index-policy.md#includeexclude-strategy)faydalanır.

System işlevindeki özelliğin kardinalitesi arttıkça, EndsWith 'ın RU tüketimi artar. Diğer bir deyişle, bir özellik değerinin belirli bir dizeyle bitip bitmediğini denetlereniz, bu özellik için bu özellik için olası değer sayısına göre RU ücreti uygulanır.

Örneğin, iki özellik düşünün: Town ve ülke. Şehir kardinalitesi 5.000 ve ülkenin kardinalitesi 200 ' dir. İki örnek sorgu aşağıda verilmiştir:

```sql
    SELECT * FROM c WHERE ENDSWITH(c.town, "York", false)
```

```sql
    SELECT * FROM c WHERE ENDSWITH(c.country, "States", false)
```

İlk sorgu büyük olasılıkla ikinci sorgudan daha fazla bir değer kullanacaktır çünkü kasabanın kardinalitesi ülkeden daha yüksektir.

EndsWith özellik boyutu bazı belgeler için 1 KB 'tan büyükse, sorgu altyapısının bu belgeleri yüklemesi gerekir. Bu durumda, sorgu altyapısı EndsWith 'ı bir dizinle tamamen değerlendiremeyecektir. Özellik boyutları 1 KB 'den büyük olan çok sayıda belgeniz varsa, EndsWith RU ücreti yüksek olacaktır.

## <a name="next-steps"></a>Sonraki adımlar

- [Dize işlevleri Azure Cosmos DB](sql-query-string-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB'ye giriş](introduction.md)
