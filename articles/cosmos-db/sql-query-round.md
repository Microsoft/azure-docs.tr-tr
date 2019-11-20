---
title: Azure Cosmos DB sorgu dilinde yuvarla
description: Azure Cosmos DB SQL sistem işlevi YUVARLA hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4bad140ea1c6b1c59c8f752bc5336bbd49952e16
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349524"
---
# <a name="round-azure-cosmos-db"></a>YUVARLAK (Azure Cosmos DB)
 En yakın tamsayı değerine yuvarlanır sayısal bir değer döndürür.  
  
## <a name="syntax"></a>Sözdizimi
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
*numeric_expr*  
   Sayısal bir ifadedir.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Sayısal bir ifade döndürür.  
  
## <a name="remarks"></a>Açıklamalar
  
  Yuvarlama işlemi, sıfırdan uzağa yuvarlanan orta nokta yuvarlama işlemini gerçekleştirdi. Giriş iki tamsayı arasında tam olarak denk gelen sayısal bir ifadesiyse sonuç, sıfırdan bir en yakın tamsayı değeri olacaktır.  
  
  |< numeric_expr >|Yuvarlak|
  |-|-|
  |-6,5000|-7|
  |-0,5|-1|
  |0,5|1\.|
  |6,5000|7||
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, aşağıdaki pozitif ve negatif sayıları en yakın tamsayıya yuvarlar.  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Sonuç kümesini burada verilmiştir.  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Matematik işlevleri Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
