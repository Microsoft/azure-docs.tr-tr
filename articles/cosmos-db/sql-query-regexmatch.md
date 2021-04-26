---
title: Azure Cosmos DB sorgu dilinde RegexMatch
description: Azure Cosmos DB 'de RegexMatch SQL sistem işlevi hakkında bilgi edinin
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 7f5b2831f45e902b312636e4133557a16ee7ec95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341646"
---
# <a name="regexmatch-azure-cosmos-db"></a>REGEXMATCH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Normal ifade özellikleri sağlar. Normal ifadeler metin desenleri bulmaya yönelik kısa ve esnek bir simgedir. Azure Cosmos DB, [Perl uyumlu normal ifadeler (PCRE)](http://www.pcre.org/)kullanır. 

## <a name="syntax"></a>Söz dizimi
  
```sql
RegexMatch(<str_expr1>, <str_expr2>, [, <str_expr3>])  
```  
  
## <a name="arguments"></a>Bağımsız değişkenler
  
*str_expr1*  
   , Aranacak dize ifadesidir.  
  
*str_expr2*  
   Normal ifade.

*str_expr3*  
   , Normal ifadeyle kullanılacak seçili değiştiricilerin dizesidir. Bu dize değeri isteğe bağlıdır. RegexMatch 'i değiştirici olmadan çalıştırmak istiyorsanız boş bir dize ekleyebilir veya tamamen atlayabilirsiniz. 

[Perl içinde normal ifadeler oluşturmak için söz dizimi](https://perldoc.perl.org/perlre)hakkında bilgi edinebilirsiniz. 

Azure Cosmos DB aşağıdaki dört değiştiricilerini destekler:

| Değiştirici | Description |
| ------ | ----------- |
| `m` | Dize ifadesini birden çok satır olarak aranmak üzere değerlendirin. Bu seçenek olmadan, "^" ve "$" dizenin başlangıcında veya sonunda, her satırda değil, her zaman eşleşmeyecektir. |
| `s` | Yeni satır karakteri de dahil olmak üzere herhangi bir karakterle eşleştirmek için "." izin verin. | 
| `i` | Kalıp eşleştirilirken büyük/küçük harf 'i yoksay. |
| `x` | Tüm boşluk karakterlerini yoksay. |

## <a name="return-types"></a>Dönüş türleri
  
  Boole ifadesi döndürür. Aranacak dize ifadesi, normal ifade veya seçili değiştiriciler geçersiz ise tanımsız döndürür.
  
## <a name="examples"></a>Örnekler
  
Aşağıdaki basit RegexMatch örneği, birkaç farklı değiştirici kullanarak normal ifade eşleşmesi için "abcd" dizesini denetler.
  
```sql
SELECT RegexMatch ("abcd", "ABC", "") AS NoModifiers, 
RegexMatch ("abcd", "ABC", "i") AS CaseInsensitive, 
RegexMatch ("abcd", "ab.", "") AS WildcardCharacter,
RegexMatch ("abcd", "ab c", "x") AS IgnoreWhiteSpace, 
RegexMatch ("abcd", "aB c", "ix") AS CaseInsensitiveAndIgnoreWhiteSpace 
```  
  
 Sonuç kümesini burada bulabilirsiniz.  
  
```json
[
    {
        "NoModifiers": false,
        "CaseInsensitive": true,
        "WildcardCharacter": true,
        "IgnoreWhiteSpace": true,
        "CaseInsensitiveAndIgnoreWhiteSpace": true
    }
]
```

RegexMatch ile StartsWith, EndsWith, Contains veya Strıngequals sistem işlevleri ile mümkün olmayan daha karmaşık dize aramaları yapmak için metakarakterleri kullanabilirsiniz. Aşağıda, [Azure Cosmos db sorgu deneme alanı](https://www.documentdb.com/sql/demo)aracılığıyla kullanılabilen beslenme veri kümesini kullanarak çalıştırabileceğiniz bazı ek örnekler verilmiştir. 

> [!NOTE] 
> Normal ifadede bir meta karakter kullanmanız gerekiyorsa ve bunun özel anlamlara sahip olmasını istemiyorsanız, kullanarak meta karakteri atlamanız gerekir `\` .

**"Anahtar" sözcüğünü içeren bir açıklama olan öğeleri tam olarak bir kez denetleyin:**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "salt{1}","")
```

**0 ile 99 arasında bir sayı içeren açıklamaları olan öğeleri denetleyin:**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "[0-99]","")
```

**"S" veya "s" ile başlayan dört harfli sözcükleri içeren açıklamaları olan öğeleri denetleyin:**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, " s... ","i")
```

## <a name="remarks"></a>Açıklamalar

Normal ifade StartsWith, EndsWith, Contains veya Strıngequals sistem işlevlerine bölünebiliyorsanız, bu sistem işlevi bir [Aralık dizininden](index-policy.md#includeexclude-strategy) faydalanır.

## <a name="next-steps"></a>Sonraki adımlar

- [Dize işlevleri Azure Cosmos DB](sql-query-string-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB'ye giriş](introduction.md)
