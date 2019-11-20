---
title: Azure Cosmos DB 'de Kullanıcı tanımlı işlevler (UDF 'ler)
description: Azure Cosmos DB içindeki kullanıcı tanımlı işlevler hakkında bilgi edinin.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: b67202da7293ef55cfe3390ca676f7944da80fba
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614323"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Azure Cosmos DB 'de Kullanıcı tanımlı işlevler (UDF 'ler)

SQL API 'SI, Kullanıcı tanımlı işlevler (UDF 'ler) için destek sağlar. Skaler UDF 'ler ile sıfır veya daha fazla bağımsız değişken geçirebilir ve tek bir bağımsız değişken sonucu döndürebilirsiniz. API, her bağımsız değişkeni geçerli JSON değerleri olarak denetler.  

API, UDF 'Leri kullanarak özel uygulama mantığını desteklemek için SQL sözdizimini genişletir. UDF 'Leri SQL API ile kaydedebilir ve SQL sorgularında bunlara başvurabilirsiniz. Aslında, UDF'ler exquisitely sorgularından çağırmak için tasarlanmıştır. Bir eş olarak, UDF 'ler, saklı yordamlar ve tetikleyiciler gibi diğer JavaScript türleri gibi bağlam nesnesine erişemez. Sorgular salt okunurdur ve birincil ya da ikincil çoğaltmalarda çalıştırılabilir. Diğer JavaScript türlerinden farklı olarak UDF 'ler, ikincil çoğaltmalarda çalışacak şekilde tasarlanmıştır.

Aşağıdaki örnek, Cosmos veritabanındaki bir öğe kapsayıcısı altına bir UDF kaydeder. Örnek, `REGEX_MATCH`adı olan bir udf oluşturur. İki JSON dize değerini `input` `pattern`kabul eder ve ilk kez, JavaScript 'in `string.match()` işlevi kullanılarak ikinci öğesinde belirtilen Düzenle eşleşip eşleşmediğini denetler.

## <a name="examples"></a>Örnekler

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

Şimdi bu UDF 'yi bir sorgu projeksiyonu içinde kullanın. UDF 'leri sorgular içerisinden çağırırken büyük/küçük harfe duyarlı `udf.` önekiyle nitelemeniz gerekir.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Sonuçlar şunlardır:

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

Aşağıdaki örnekte olduğu gibi, bir filtrenin içindeki `udf.` önekiyle nitelenmiş UDF 'yi kullanabilirsiniz:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Sonuçlar şunlardır:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

Temelde, UDF 'ler, hem projeksiyonde hem de filtrelerde kullanabileceğiniz geçerli skaler ifadeleridir.

UDF 'nin gücünden birini genişletmek için koşullu mantığa başka bir örneğe bakın:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

Aşağıdaki örnek UDF 'yi alıştırmalar:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Sonuçlar şunlardır:

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

UDF parametreleri tarafından başvurulan Özellikler JSON değerinde kullanılamıyorsa, parametre tanımsız olarak değerlendirilir ve UDF çağrısı atlanır. Benzer şekilde, UDF 'nin sonucu tanımsızdır, sonuca dahil değildir.

Yukarıdaki örneklerde gösterildiği gibi, UDF 'ler, JavaScript dilinin gücünden SQL API 'SI ile tümleşir. UDF 'ler, yerleşik JavaScript çalışma zamanı özellikleri yardımıyla karmaşık yordamla, koşullu mantığa yönelik zengin bir programlanabilir arabirim sağlar. SQL API 'SI, geçerli WHERE veya SELECT yan tümcesi aşamasında her kaynak öğe için UDF 'ler için bağımsız değişkenler sağlar. Sonuç, genel yürütme ardışık düzenine sorunsuz bir şekilde dahil edilir. Özet olarak, UDF 'ler, sorguların bir parçası olarak karmaşık iş mantığı yapmak için harika araçlardır.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Cosmos DB giriş](introduction.md)
- [Sistem işlevleri](sql-query-system-functions.md)
- [Toplar](sql-query-aggregates.md)