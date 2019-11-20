---
title: Azure Cosmos DB diğer ad
description: Azure Cosmos DB SQL sorgularında diğer ad değerleri hakkında bilgi edinin
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: 3b17cbc7710647b1e1875025a1db1849034ec1dc
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002081"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Azure Cosmos DB diğer ad

Sorgularda açıkça diğer ad değerleri ekleyebilirsiniz. Sorgunun aynı ada sahip iki özelliği varsa, bir veya her ikisini de yeniden adlandırmak için diğer adları kullanın.

## <a name="examples"></a>Örnekler

Diğer ad için kullanılan AS anahtar sözcüğü, ikinci değeri `NameInfo`aşağıdaki örnekte gösterildiği gibi isteğe bağlıdır:

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Sonuçlar şunlardır:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Cosmos DB .NET örnekleri](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT yan tümcesi](sql-query-select.md)
- [FROM yan tümcesi](sql-query-from.md)
