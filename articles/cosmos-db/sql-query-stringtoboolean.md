---
title: Azure Cosmos DB sorgu dilinde StringToBoolean
description: Azure Cosmos DB 'de SQL sistem işlevi StringToBoolean hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8719857dca16585a045f8174dbac8df455f38f38
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349263"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB)
 Boole değerine çevrilmiş bir ifade döndürür. İfade çevrilemez, tanımsız döndürür.  
  
## <a name="syntax"></a>Sözdizimi
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
*str_expr*  
   , Boole ifadesi olarak ayrıştırılacak bir dize ifadesidir.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Boole ifadesi veya tanımsız döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, `StringToBoolean` ' ın farklı türler genelinde nasıl davranacağını gösterir. 
 
 Aşağıda, geçerli girişi olan örnekler verilmiştir.

Boşluğa yalnızca "true"/"false" ile önce veya sonra izin verilir.

```sql
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
[{"b1": true, "b2": false, "b3": false}]
```  

Aşağıda, geçersiz girişe sahip örnekler verilmiştir.

 Boole değerleri büyük küçük harfe duyarlıdır ve "true" ve "false" gibi tüm küçük karakterlerle yazılması gerekir.

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Sonuç kümesini burada verilmiştir.  
  
```json
[{}]
``` 

Geçirilen ifade Boole ifadesi olarak ayrıştırılacak; Bu girişler Boolean türü olarak değerlendirilmez ve tanımsız döndürür.

```sql
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

Sonuç kümesini burada verilmiştir.  
  
```json
[{}]
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Dize işlevleri Azure Cosmos DB](sql-query-string-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
