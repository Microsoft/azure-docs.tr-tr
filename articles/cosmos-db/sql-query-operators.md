---
title: Azure Cosmos DB için SQL sorgu işleçleri
description: Azure Cosmos DB için SQL işleçleri hakkında bilgi edinin.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: 899355ad7331a3df8cd5d647a573dc15e3a0bb14
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003376"
---
# <a name="operators-in-azure-cosmos-db"></a>Azure Cosmos DB işleçler

Bu makalede Azure Cosmos DB tarafından desteklenen çeşitli işleçler ayrıntılı olarak açıklanır.

## <a name="equality-and-comparison-operators"></a>Eşitlik ve karşılaştırma Işleçleri

Aşağıdaki tabloda, her iki JSON türünden SQL API eşitlik karşılaştırmaları sonucunu gösterir.

| **OP** | **Tanımsız** | **Null** | **Boole değeri** | **Sayı** | **dize** | **Nesne** | **Dizi** |
|---|---|---|---|---|---|---|---|
| **Tanımsız** | Tanımlanmadı | Tanımlanmadı | Tanımlanmadı | Tanımlanmadı | Tanımlanmadı | Tanımlanmadı | Tanımlanmadı |
| **Null** | Tanımlanmadı | **Tamam** | Tanımlanmadı | Tanımlanmadı | Tanımlanmadı | Tanımlanmadı | Tanımlanmadı |
| **Boole değeri** | Tanımlanmadı | Tanımlanmadı | **Tamam** | Tanımlanmadı | Tanımlanmadı | Tanımlanmadı | Tanımlanmadı |
| **Sayı** | Tanımlanmadı | Tanımlanmadı | Tanımlanmadı | **Tamam** | Tanımlanmadı | Tanımlanmadı | Tanımlanmadı |
| **dize** | Tanımlanmadı | Tanımlanmadı | Tanımlanmadı | Tanımlanmadı | **Tamam** | Tanımlanmadı | Tanımlanmadı |
| **Nesne** | Tanımlanmadı | Tanımlanmadı | Tanımlanmadı | Tanımlanmadı | Tanımlanmadı | **Tamam** | Tanımlanmadı |
| **Dizi** | Tanımlanmadı | Tanımlanmadı | Tanımlanmadı | Tanımlanmadı | Tanımlanmadı | Tanımlanmadı | **Tamam** |

`>` `Undefined`, ,,`>=`Ve gibikarşılaştırmaişleçleriiçin,türlerarasındaveyaikinesneveyadiziarasındakarşılaştırmaüretir.`<=` `!=` `<`  

Skaler ifadenin sonucu ise `Undefined`, eşit `true`olmadığı `Undefined` için öğe sonuca dahil değildir.

## <a name="logical-and-or-and-not-operators"></a>Mantıksal (AND, OR ve NOT) işleçleri

Mantıksal işleçler Boole değerleri üzerinde çalışır. Aşağıdaki tablolarda bu işleçler için mantıksal Truth tabloları gösterilmektedir:

**OR işleci**

| OR | True | False | Tanımlanmadı |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Tanımlanmadı |
| Tanımlanmadı |True |Tanımlanmadı |Tanımlanmadı |

**AND işleci**

| VE | True | False | Tanımlanmadı |
| --- | --- | --- | --- |
| True |True |False |Tanımlanmadı |
| False |False |False |False |
| Tanımlanmadı |Tanımlanmadı |False |Tanımlanmadı |

**NOT işleci**

| DEĞİL |  |
| --- | --- |
| True |False |
| False |True |
| Tanımlanmadı |Tanımlanmadı |


## <a name="-operator"></a>* işleci

\* Özel işleci, tüm öğeyi olduğu gibi projeler. Kullanıldığında yansıtılan tek alan olması gerekir. Benzer `SELECT * FROM Families f` bir sorgu geçerli, `SELECT *, f.id FROM Families f` ancak `SELECT VALUE * FROM Families f` geçerli değil.

## <a name="-and--operators"></a>? ve?? işletmenlerinin

C# Ve JavaScript gibi programlama dillerinde olduğu gibi, Koşullu ifadeler oluşturmak için üçlü (?) ve birleşim (??) işleçlerini kullanabilirsiniz. 

Kullanabilirsiniz. anında yeni JSON özellikleri oluşturmaya yönelik operatör. Örneğin, aşağıdaki sorgu, sınıf düzeylerini veya `elementary` `other`içine sınıflandırır:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Ayrıca, çağrılarını iç içe geçirebilirsiniz. işlecini, aşağıdaki sorguda olduğu gibi: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Diğer sorgu işleçleri gibi? başvurulan Özellikler eksikse veya karşılaştırılan türler farklıysa işleç öğeleri dışlar.

??? yarı yapılandırılmış veya karma tür verilere göre sorgulama yaparken bir öğedeki özelliği etkin bir şekilde denetlemek için işleç. Örneğin, aşağıdaki sorgu varsa döndürür `lastName` `lastName` veya `surname` yoksa.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Cosmos DB .NET örnekleri](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [anahtar sözcükler](sql-query-keywords.md)
- [SELECT yan tümcesi](sql-query-select.md)
