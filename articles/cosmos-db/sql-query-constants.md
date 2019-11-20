---
title: Azure Cosmos DB SQL sabitleri
description: Azure Cosmos DB 'de SQL sabitleri hakkında bilgi edinin
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: 41cafeb76b7b9481a78ffcff179676d0aa7bf6fd
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002040"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>SQL sorgu sabitlerini Azure Cosmos DB  

 Bir sabit olarak da bilinen bir sabit değer ya da bir skalar değer belirli bir veri değerini temsil eden bir semboldür. Biçimi bir sabiti temsil ettiği değerin veri türüne göre değişir.  
  
 **Skaler türleri desteklenir:**  
  
|**Tür**|**Değerleri sırasını**|  
|-|-|  
|**Tanımsız**|Tek değer: **tanımlanmamış**|  
|**Null**|Tek değer: **null**|  
|**Boole değeri**|Değerler: **false**, **true**.|  
|**Sayı**|Bir çift duyarlıklı kayan noktalı sayı, standart IEEE 754.|  
|**dize**|Sıfır veya daha fazla Unicode karakter dizisi. Dizeleri tek veya çift tırnak içine alınmalıdır.|  
|**Dizi**|Sıfır veya daha fazla öğe dizisi. Her öğe, **tanımsız**bir değer hariç herhangi bir skaler veri türü değeri olabilir.|  
|**Nesne**|Sırasız bir sıfır veya daha fazla ad/değer çiftleri kümesi. Adı bir Unicode dize, değer dışında herhangi bir skaler veri türde olabilir **tanımlanmamış**.|  
  
## <a name="bk_syntax"></a>Sözdizimi
  
```sql  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
##  <a name="bk_arguments"></a>Değişkenlerinden
  
* `<undefined_constant>; Undefined`  
  
  Türü tanımlanmamış değerini tanımsız temsil eder.  
  
* `<null_constant>; null`  
  
  Temsil eden **null** türünün değerini **Null**.  
  
* `<boolean_constant>`  
  
  Boole türünde bir sabiti temsil eder.  
  
* `false`  
  
  Temsil eden **false** türü Boolean değeri.  
  
* `true`  
  
  Temsil eden **true** türü Boolean değeri.  
  
* `<number_constant>`  
  
  Bir sabiti temsil eder.  
  
* `decimal_literal`  
  
  Ondalık sabit değerleri ondalık gösterim veya bilimsel gösterim kullanılarak temsil sayılardır.  
  
* `hexadecimal_literal`  
  
  Onaltılık değişmez değerler, önek '0 x'ı bir veya daha fazla onaltılık basamağın geldiği' kullanılarak temsil sayılardır.  
  
* `<string_constant>`  
  
  String türünde bir sabiti temsil eder.  
  
* `string _literal`  
  
  Dize değişmez değerleri, sıfır veya daha fazla Unicode karakter dizisi veya kaçış dizileri tarafından temsil edilen Unicode dizelerdir. Dize sabit değerlerinin tek tırnak içine alınmış (kesme işareti: ') veya çift tırnak (tırnak işareti: ").  
  
  Aşağıdaki kaçış dizileri izin verilir:  
  
|**Kaçış sırası**|**Açıklama**|**Unicode karakter**|  
|-|-|-|  
|\\'|kesme işareti (')|U + 0027|  
|\\"|tırnak işareti (")|U + 0022|  
|\\\ |ters eğik çizgi\\()|U + 005C|  
|\\/|solidus (/)|U + 002F|  
|\b|Geri Al|U + 0008|  
|\f|form besleme|U + 000C|  
|\n|satır besleme|U + 000A|  
|\r|satır başı|U + 000D|  
|\t|sekme|U + 0009|  
|\uXXXX|4 onaltılık basamak tarafından tanımlanan Unicode karakter.|U + XXXX|  

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Cosmos DB .NET örnekleri](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Belge verilerini modelleme](modeling-data.md)
