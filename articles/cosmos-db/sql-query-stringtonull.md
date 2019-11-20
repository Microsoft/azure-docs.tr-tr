---
title: Azure Cosmos DB sorgu dilinde StringToNull
description: Azure Cosmos DB 'de SQL sistem işlevi StringToNull hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c7ea3ca82cadb1351d8581f3d652ce060d67969b
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349254"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull (Azure Cosmos DB)
 Null değere çevrilmiş bir ifade döndürür. İfade çevrilemez, tanımsız döndürür.  
  
## <a name="syntax"></a>Sözdizimi
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
*str_expr*  
   , Null bir ifade olarak ayrıştırılacak bir dize ifadesidir.
  
## <a name="return-types"></a>Dönüş türleri
  
  Null veya tanımsız bir ifade döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, `StringToNull` ' ın farklı türler genelinde nasıl davranacağını gösterir. 

Aşağıda, geçerli girişi olan örnekler verilmiştir.

 Boşluk yalnızca "null" veya sonraki bir değere izin verilir.

```sql
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
[{"n1": null, "n2": null, "n3": true}]
```  

Aşağıda, geçersiz girişe sahip örnekler verilmiştir.

Null, büyük/küçük harfe duyarlıdır ve "null" olarak tüm küçük karakterlerle yazılması gerekir.

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
[{}]
```  

Geçirilen ifade null bir ifade olarak ayrıştırılacak; Bu girişler null türü olarak değerlendirilmez ve bu nedenle tanımsız döndürür.

```sql
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
[{}]
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Dize işlevleri Azure Cosmos DB](sql-query-string-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
