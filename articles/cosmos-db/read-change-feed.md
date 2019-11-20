---
title: Azure Cosmos DB Azure Cosmos DB değişiklik akışına erişme
description: Bu makalede, Azure Cosmos DB Azure Cosmos DB ' de değişiklik akışını okuma ve erişme için kullanabileceğiniz farklı seçenekler açıklanmaktadır.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 3d30c9f946f97f06c1a3ba1cd2e77e1ab151a572
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754879"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Azure Cosmos DB değişiklik akışı okunuyor

Aşağıdaki seçeneklerden herhangi birini kullanarak Azure Cosmos DB değişiklik akışı ile çalışabilirsiniz:

* Azure Işlevleri 'ni kullanma
* Değişiklik akışı işlemci kitaplığını kullanma
* Azure Cosmos DB SQL API SDK 'sını kullanma

## <a name="using-azure-functions"></a>Azure Işlevleri 'ni kullanma

Azure Işlevleri, en basit ve önerilen seçenektir. Cosmos DB için bir Azure Işlevleri tetikleyicisi oluşturduğunuzda, bağlanacak kapsayıcıyı seçebilirsiniz ve kapsayıcıda her değişiklik yapıldığında Azure Işlevi tetiklenir. Tetikleyiciler, Azure Işlevleri portalı, Azure Cosmos DB portalı veya SDK 'lar aracılığıyla program aracılığıyla oluşturulabilir. Visual Studio ve VS Code Azure Işlevleri yazmak için destek sağlar ve platformlar arası geliştirme için Azure Işlevleri CLı 'yi de kullanabilirsiniz. Masaüstünüzdeki kodu yazabilir ve hata ayıklamanıza ve sonra işlevi tek bir tıklama ile dağıtmanıza de tıklayabilirsiniz. Daha fazla bilgi için bkz. [Azure işlevleri 'ni kullanarak sunucusuz veritabanı hesaplama](serverless-computing-database.md) ve [Azure işlevleri Ile değişiklik akışını kullanma](change-feed-functions.md)) makaleleri.

## <a name="using-the-change-feed-processor-library"></a>Değişiklik akışı işlemci kitaplığını kullanma

Değişiklik akışı işlemci kitaplığı karmaşıklığı gizler ve yine de değişiklik akışına ilişkin kapsamlı bir denetim sağlar. Kitaplık, işleme işlevinizin kitaplık tarafından çağrıldığı gözlemci düzeniyle uyar. Yüksek verimlilik değişikliği akışınız varsa, değişiklik akışını okumak için birden çok istemci örneği oluşturabilirsiniz. Değişiklik akışı işlemci kitaplığını kullandığınız için, bu mantığı uygulamak zorunda kalmadan yükü farklı istemciler arasında otomatik olarak böler. Tüm karmaşıklıklar kitaplık tarafından işlenir. Kendi yük dengeleyicinizi kullanmak istiyorsanız, değişiklik akışını işlemek üzere özel bir bölüm stratejisi için `IPartitionLoadBalancingStrategy` uygulayabilirsiniz. Daha fazla bilgi edinmek için bkz. [değişiklik akışı işlemci Kitaplığı kullanma](change-feed-processor.md).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Azure Cosmos DB SQL API SDK 'sını kullanma

SDK ile, değişiklik akışında düşük düzey bir denetim edinirsiniz. Denetim noktasını yönetebilir, belirli bir mantıksal bölüm anahtarına erişebilir, vb. Birden çok okuyucularınız varsa, okuma yükünü farklı iş parçacıklarına veya farklı istemcilere dağıtmak için `ChangeFeedOptions` kullanabilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelerde değişiklik akışı hakkında daha fazla bilgi edinmek için devam edebilirsiniz:

* [Değişiklik akışına genel bakış](change-feed.md)
* [Azure Işlevleri ile değişiklik akışını kullanma](change-feed-functions.md)
* [Değişiklik akışı işlemci kitaplığını kullanma](change-feed-processor.md)
