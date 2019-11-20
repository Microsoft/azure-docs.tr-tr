---
title: Azure Cosmos DB sorgu dilinde IS_PRIMITIVE
description: Azure Cosmos DB 'de SQL sistem işlevi IS_PRIMITIVE hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 960c6cbe6b60ad477f630b14ce0953601e71c34e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349813"
---
# <a name="is_primitive-azure-cosmos-db"></a>IS_PRIMITIVE (Azure Cosmos DB)
 Belirtilen ifadenin türü basit bir tür olup olmadığını gösteren bir Boole değeri döndürür (, Boole, sayısal ya da boş dize).  
  
## <a name="syntax"></a>Sözdizimi
  
```sql
IS_PRIMITIVE(<expr>)  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
*ifadeyi*  
   Herhangi bir ifadedir.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Bir Boolean ifadesi döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, `IS_PRIMITIVE` işlevini kullanarak JSON Boole, sayı, dize, null, nesne, dizi ve tanımsız türlerin nesnelerini denetler.  
  
```sql
SELECT   
           IS_PRIMITIVE(true) AS isPrim1,   
           IS_PRIMITIVE(1) AS isPrim2,  
           IS_PRIMITIVE("value") AS isPrim3,   
           IS_PRIMITIVE(null) AS isPrim4,  
           IS_PRIMITIVE({prop: "value"}) AS isPrim5,   
           IS_PRIMITIVE([1, 2, 3]) AS isPrim6,  
           IS_PRIMITIVE({prop: "value"}.prop2) AS isPrim7  
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
[{"isPrim1": true, "isPrim2": true, "isPrim3": true, "isPrim4": true, "isPrim5": false, "isPrim6": false, "isPrim7": false}]  
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Tür denetimi işlevleri Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
