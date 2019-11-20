---
title: Azure Cosmos DB 'de SQL sorguları ile çalışmaya başlama
description: SQL sorgularına giriş
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 8de5140d0146ccbb18f41867e1c716aa2f3897b7
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001901"
---
# <a name="getting-started-with-sql-queries"></a>SQL sorgularıyla çalışmaya başlama

Azure Cosmos DB SQL API hesapları, Yapılandırılmış Sorgu Dili (SQL) kullanan öğeleri JSON sorgu dili olarak sorgulamayı destekler. Azure Cosmos DB sorgu dilinin tasarım amaçları şunlardır:

* Yeni bir sorgu dili almak yerine, en tanıdık ve popüler sorgu dillerinden biri olan SQL 'i destekler. SQL, JSON öğeleri üzerinde zengin sorgular için resmi bir programlama modeli sağlar.  

* Sorgu dili için temel olarak JavaScript 'in programlama modelini kullanın. JavaScript 'in tür sistemi, ifade değerlendirmesi ve işlev çağırma SQL API 'sinin köklerdir. Bu kökler, ilişkisel projeksiyonlar, JSON öğelerinde hiyerarşik gezinti, kendinden birleşimler, uzamsal sorgular ve tamamen JavaScript 'te yazılmış Kullanıcı tanımlı işlevlerin (UDF 'ler) çağrılması gibi özellikler için doğal bir programlama modeli sağlar.

## <a name="upload-sample-data"></a>Örnek verileri karşıya yükle

SQL API Cosmos DB hesabınızda adlı `Families`bir kapsayıcı oluşturun. Kapsayıcıda iki basit JSON öğesi oluşturun. Bu veri kümesini kullanarak Azure Cosmos DB sorgu docs ' de örnek sorguların çoğunu çalıştırabilirsiniz.

### <a name="create-json-items"></a>JSON öğeleri oluşturma

Aşağıdaki kod, aileler hakkında iki basit JSON öğesi oluşturur. Andersen ve Wakefield ailelerinin basit JSON öğeleri, ebeveynler, alt öğeler, adresler ve kayıt bilgilerini içerir. İlk öğede dizeler, sayılar, Boole değerleri, diziler ve iç içe geçmiş özellikler vardır.


```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

İkinci öğe `givenName` ve`firstName` `familyName` yerine`lastName`ve kullanır.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>JSON öğelerini sorgulama

Azure Cosmos DB SQL sorgu dilinin bazı önemli yönlerini anlamak için JSON verilerinde birkaç sorgu deneyin.

Aşağıdaki sorgu, `id` alanın eşleştiği `AndersenFamily`öğeleri döndürür. `SELECT *` Sorgu olduğundan, sorgunun çıktısı tamamlanmış JSON öğesidir. Sözdizimi Seç hakkında daha fazla bilgi için bkz. [select deyimleri](sql-query-select.md). 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Sorgu sonuçları şunlardır: 

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

Aşağıdaki sorgu JSON çıkışını farklı bir şekle yeniden biçimlendirir. Sorgu, seçili iki `Family` `Name` alana sahip yeni bir JSON nesnesi ve `City`adres şehri durum ile aynı olduğunda. "NY, NY" Bu durumda eşleşir.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

Sorgu sonuçları şunlardır:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

Aşağıdaki sorgu, aile içindeki alt öğelerin verilen tüm adlarını (şehre göre `id` sıralanmış `WakefieldFamily`olan) döndürür.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

Sonuçlar şunlardır:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>Açıklamalar

Yukarıdaki örneklerde Cosmos DB sorgu dilinin çeşitli yönleri gösterilmektedir:  

* SQL API 'SI JSON değerleri üzerinde çalıştığından, satırlar ve sütunlar yerine Ağaç şekilli varlıklarla ilgilenir. Ağaç düğümlerine, ANSI SQL `Node1.Node2.Node3…..Nodem` `<table>.<column>` ' in iki parçalı başvurusuna benzer şekilde herhangi bir rastgele derinlikte başvurabilirsiniz.

* Sorgu dili şeless verilerle çalıştığından, tür sistemi dinamik olarak bağlanmalıdır. Farklı türlerde farklı öğeye aynı ifadesi üretebilir. Bir sorgunun sonucu geçerli bir JSON değeridir, ancak sabit bir şema olması garanti edilmez.  

* Azure Cosmos DB, yalnızca Katı JSON öğelerini destekler. Tür sistemi ve ifadeleri yalnızca JSON türleriyle uğraşmak üzere kısıtlanmıştır. Daha fazla bilgi için bkz. [JSON belirtimi](https://www.json.org/).  

* Cosmos kapsayıcısı, JSON öğelerinin şema içermeyen bir koleksiyonudur. Kapsayıcı öğeleri içindeki ve içindeki ilişkiler, birincil anahtar ve yabancı anahtar ilişkilerine göre değil, kapsama göre örtük olarak yakalanır. Bu özellik, bu makalenin ilerleyen kısımlarında açıklanan öğe içi birleşimler için önemlidir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Cosmos DB giriş](introduction.md)
- [Azure Cosmos DB .NET örnekleri](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT yan tümcesi](sql-query-select.md)
