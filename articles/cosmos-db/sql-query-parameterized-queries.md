---
title: Azure Cosmos DB parametreli sorgular
description: SQL parametreli sorgular hakkında bilgi edinin
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 45c1344c32e35f60f35ba8ed105e912d92574cce
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003604"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Azure Cosmos DB parametreli sorgular

Cosmos DB tanıdık @ gösterimi tarafından ifade edilen parametrelere sahip sorguları destekler. Parametreli SQL, Kullanıcı girişi için güçlü işleme ve kaçış sağlar ve SQL ekleme aracılığıyla verilerin yanlışlıkla açıklanmasını önler.

## <a name="examples"></a>Örnekler

Örneğin, parametreleri `lastName` alan ve `address.state` olarak bir sorgu yazabilir ve kullanıcı girişine göre çeşitli değerleri `lastName` ve `address.state` bunları çalıştırabilirsiniz.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Daha sonra bu isteği aşağıdaki gibi parametreli bir JSON sorgusu olarak Cosmos DB gönderebilirsiniz:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

Aşağıdaki örnek, en üstteki bağımsız değişkeni parametreli bir sorgu ile ayarlar: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Parametre değerleri geçerli bir JSON olabilir: dizeler, sayılar, Boole değerleri, null, hatta diziler veya iç içe geçmiş JSON. Cosmos DB şemaya daha az olduğundan, parametreler hiçbir türe göre doğrulanmaz.


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Cosmos DB .NET örnekleri](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Belge verilerini modelleme](modeling-data.md)
