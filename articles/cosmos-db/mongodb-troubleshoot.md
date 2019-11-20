---
title: Mongo DB için Azure Cosmos DB API 'sindeki yaygın hataların sorunlarını giderme
description: Bu belgede, MongoDB için Azure Cosmos DB API 'sinde karşılaşılan yaygın sorunları gidermeye yönelik yollar ele alınmaktadır.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: roaror
ms.openlocfilehash: ece975fa37e500b1c160210684a0cb46e719c48b
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754965"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB için Azure Cosmos DB API 'sindeki yaygın sorunları giderme

Azure Cosmos DB, MongoDB dahil olmak üzere yaygın NoSQL veritabanlarının tel protokollerini uygular. Kablo protokol uygulamasına bağlı olarak, NoSQL veritabanlarıyla çalışan mevcut istemci SDK 'larını, sürücüleri ve araçları kullanarak Azure Cosmos DB ile şeffaf bir şekilde etkileşim kurabilirsiniz. Azure Cosmos DB, NoSQL veritabanlarının herhangi biri için kablo ile uyumlu API 'Ler sağlamak üzere veritabanlarının herhangi bir kaynak kodunu kullanmaz. Tel Protokolü sürümlerini anlayan tüm MongoDB istemci sürücüleri, Azure Cosmos DB bağlanabilir.

MongoDB için Azure Cosmos DB API 'SI, MongoDB 'nin kablo protokolünün 3,2 sürümüyle uyumlu olsa da (sürüm 3,4 ' de eklenen sorgu işleçleri ve Özellikler Şu anda önizleme olarak kullanılabilir), Azure Cosmos DB karşılık gelen bazı özel hata kodları vardır belirli hatalar. Bu makalede farklı hatalar, hata kodları ve bu hataları gidermek için gereken adımlar açıklanmaktadır.

## <a name="common-errors-and-solutions"></a>Yaygın hatalar ve çözümler

| Hata               | Kodlayın  | Açıklama  | Çözüm  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Tüketilen istek birimlerinin toplam sayısı, koleksiyonun sağlanan istek birimi hızından daha fazla ve kısıtlanmış. | Azure portal bir kapsayıcıya veya bir kapsayıcı kümesine atanmış üretilen işi ölçeklendirebilir veya işlemi yeniden deneyebilirsiniz. |
| ExceededMemoryLimit | 16501 | Çok kiracılı bir hizmet olarak, işlem istemcinin bellek işlem biriminden çıktı. | Daha kısıtlayıcı sorgu ölçütleri aracılığıyla işlemin kapsamını azaltın veya [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)destek ekibiyle iletişime geçin. Örnek: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Belirtilen order by öğesine karşılık gelen dizin yolu hariç tutulur/order by sorgusunda, kendisine sunulabilecek bir bileşik dizin yoktur. | 2 | Sorgu, dizini oluşturulmamış bir alana sıralama isteğinde bulunur. | Denenen sıralama sorgusu için eşleşen bir dizin (veya bileşik dizin) oluşturun. |
| MongoDB hat sürümü sorunları | - | MongoDB sürücülerinin eski sürümleri, bağlantı dizelerindeki Azure Cosmos hesabının adını algılayamaz. | MongoDB bağlantı dizesi için Cosmos DB API 'sinin sonundaki *@AppName = @**AccountName***  , burada ***AccountName*** Cosmos DB hesabınızın adıdır. |


## <a name="next-steps"></a>Sonraki adımlar

- [Studio 3T](mongodb-mongochef.md) 'ı Azure Cosmos DB MongoDB IÇIN API 'si ile nasıl kullanacağınızı öğrenin.
- MongoDB için Azure Cosmos DB API 'SI ile [Robo 3T kullanmayı](mongodb-robomongo.md) öğrenin.
- MongoDB için Azure Cosmos DB API 'siyle MongoDB [örneklerini](mongodb-samples.md) gezin.

