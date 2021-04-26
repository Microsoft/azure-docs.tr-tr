---
title: include dosyası
description: include dosyası
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/05/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 448c445f49fb4baa300ce6636288a39080da3baf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026499"
---
Verilerinizi almak ve filtrelemek için Veri Gezgini sorguları kullanabilirsiniz.

1. Veri Gezgini **öğeleri** sekmesinin en üstünde, varsayılan sorguyu gözden geçirin `SELECT * FROM c` . Bu sorgu, KIMLIĞE göre sıralanmış kapsayıcıdan tüm belgeleri alır ve görüntüler. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png" alt-text="Veri Gezgini 'de varsayılan sorgu SELECT * FROM c":::
   
1. Sorguyu değiştirmek için **Filtreyi Düzenle**' yi seçin, varsayılan sorguyu ile değiştirin `ORDER BY c._ts DESC` ve sonra **Filtre Uygula**' yı seçin.
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png" alt-text="ORDER BY c._ts DESC ekleyerek ve Filtre Uygula’ya tıklayarak varsayılan sorguyu değiştirin":::

   Değiştirilen sorgu, belgeleri zaman damgalarına göre azalan sırada görüntüler, bu nedenle artık ikinci belgeniz önce listelenir. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png" alt-text="Sorgu ORDER c._ts DESC olarak değiştirildi ve Filtre Uygula ' ya tıklayın":::

SQL söz dizimini biliyorsanız, sorgu koşulu kutusuna desteklenen herhangi bir [SQL](../articles/cosmos-db/sql-query-getting-started.md) sorgusunu girebilirsiniz. Ayrıca, sunucu tarafı iş mantığı için saklı yordamlar, UDF 'ler ve Tetikleyiciler oluşturmak üzere Veri Gezgini de kullanabilirsiniz. 

Veri Gezgini, API 'lerde kullanılabilen tüm yerleşik programlı veri erişim özelliklerine kolay Azure portal erişim sağlar. Ayrıca, iş üretimini ölçeklendirmek, anahtar ve bağlantı dizelerini almak ve Azure Cosmos DB hesabınız için ölçümleri ve SLA 'Ları gözden geçirmek üzere portalını de kullanabilirsiniz.