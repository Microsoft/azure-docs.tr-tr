---
title: Azure Cosmos DB sorgu dilinde StringToNumber
description: Azure Cosmos DB SQL sistem işlevi StringToNumber 'ı hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b9596738d9b02fa26f9c363287323b905654a1f
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349231"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber (Azure Cosmos DB)
 Sayıya çevrilmiş bir ifade döndürür. İfade çevrilemez, tanımsız döndürür.  
  
## <a name="syntax"></a>Sözdizimi
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
*str_expr*  
   , JSON sayı ifadesi olarak ayrıştırılacak bir dize ifadesidir. JSON 'daki Sayılar tamsayı veya kayan nokta olmalıdır. JSON biçimi hakkında daha fazla bilgi için bkz. [JSON.org](https://json.org/)  
  
## <a name="return-types"></a>Dönüş türleri
  
  Bir sayı ifadesi veya tanımsız döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, `StringToNumber` ' ın farklı türler genelinde nasıl davranacağını gösterir. 

Boşluğa yalnızca sayıdan önce veya sonra izin verilir.

```sql
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

JSON içinde geçerli bir sayı ya bir tamsayı ya da kayan nokta numarası olmalıdır.

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
{{}}
```  

Geçirilen ifade sayı ifadesi olarak ayrıştırılacak; Bu girişler tür numarası olarak değerlendirilmez ve tanımsız döndürür. 

```sql
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
{{}}
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Dize işlevleri Azure Cosmos DB](sql-query-string-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
