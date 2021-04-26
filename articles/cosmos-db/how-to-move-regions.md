---
title: Bir Azure Cosmos DB hesabını başka bir bölgeye taşıma
description: Azure Cosmos DB hesabını başka bir bölgeye taşımayı öğrenin.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: 29b5c5d3cf55cd11fe505c0d9ab9b894dc2ad267
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93342037"
---
# <a name="move-an-azure-cosmos-db-account-to-another-region"></a>Bir Azure Cosmos DB hesabını başka bir bölgeye taşıma
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Bu makalede aşağıdakilerden birini açıklanmaktadır:

- Verilerin Azure Cosmos DB çoğaltılacağı bölgeyi taşıyın.
- Hesap (Azure Resource Manager) meta verilerini ve verileri bir bölgeden diğerine geçirin.

## <a name="move-data-from-one-region-to-another"></a>Verileri bir bölgeden diğerine taşıma

Azure Cosmos DB veri çoğaltmasını yerel olarak destekler, bu nedenle verileri bir bölgeden diğerine taşımak basittir. Azure portal, Azure PowerShell veya Azure CLı kullanarak bunu yapabilirsiniz. Aşağıdaki adımları içerir:

1. Hesaba yeni bir bölge ekleyin.

    Azure Cosmos DB hesabına yeni bir bölge eklemek için, bkz. [bir Azure Cosmos DB hesabına bölge ekleme/kaldırma](how-to-manage-database-account.md#addremove-regions-from-your-database-account).

1. Yeni bölgeye el ile yük devretme işlemi gerçekleştirin.

    Kaldırılmakta olan bölge şu anda hesap için yazma bölgesidir, önceki adımda eklenen yeni bölgeye bir yük devretme başlatmanız gerekir. Bu sıfır kapalı kalma bir işlemdir. Çok bölgeli bir hesapta bir okuma bölgesi taşıyorsanız, bu adımı atlayabilirsiniz. 
    
    Yük devretmeyi başlatmak için bkz. [Azure Cosmos hesabında el ile yük devretme gerçekleştirme](how-to-manage-database-account.md#manual-failover).

1. Özgün bölgeyi kaldırın.

    Bir Azure Cosmos DB hesabından bölge kaldırmak için, bkz. [Azure Cosmos DB hesabınızdan bölge ekleme/kaldırma](how-to-manage-database-account.md#addremove-regions-from-your-database-account).

## <a name="migrate-azure-cosmos-db-account-metadata"></a>Azure Cosmos DB hesabı meta verilerini geçirme

Azure Cosmos DB, hesap meta verilerinin bir bölgeden diğerine geçirilmesini yerel olarak desteklemez. Hem hesap meta verilerini hem de müşteri verilerini bir bölgeden diğerine geçirmek için, istenen bölgede yeni bir hesap oluşturmanız ve ardından verileri el ile kopyalamanız gerekir. 

SQL API 'SI için neredeyse sıfır kapalı kalma süresi, [değişiklik akışı](change-feed.md) veya onu kullanan bir araç kullanılmasını gerektirir. MongoDB API 'sini, Cassandra API veya başka bir API 'yi geçiriyorsanız veya hesapların arasında veri geçirmeye yönelik seçenekler hakkında daha fazla bilgi edinmek için, bkz. Şirket [içi veya bulut verilerinizi Azure Cosmos DB geçirme seçenekleri](cosmosdb-migrationchoices.md). 

Aşağıdaki adımlarda, SQL API ve verileri için bir Azure Cosmos DB hesabının bir bölgeden diğerine nasıl geçirileceği gösterilmektedir:

1. İstenen bölgede yeni bir Azure Cosmos DB hesabı oluşturun.

    Azure portal, PowerShell veya Azure CLı aracılığıyla yeni bir hesap oluşturmak için bkz. [Azure Cosmos DB hesabı oluşturma](how-to-manage-database-account.md#create-an-account).

1. Yeni bir veritabanı ve kapsayıcı oluşturun.

    Yeni bir veritabanı ve kapsayıcı oluşturmak için bkz. [Azure Cosmos kapsayıcısı oluşturma](how-to-create-container.md).

1. Azure Cosmos DB Live Data Migrator aracını kullanarak verileri geçirin.

    Neredeyse sıfır kapalı kalma süresine sahip verileri geçirmek için bkz. [canlı veri Migrator aracı Azure Cosmos DB](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator).

1. Uygulama bağlantı dizesini güncelleştirin.

    Canlı veri Migrator aracı çalışmaya devam ediyorsa, uygulamanızın yeni dağıtımında bağlantı bilgilerini güncelleştirin. Uygulamanıza ait uç noktaları ve anahtarları Azure portal alabilirsiniz.

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="NoSQL veritabanı güvenliğini gösteren Azure portal erişim denetimi.":::

1. İstekleri yeni uygulamaya yeniden yönlendirin.

    Yeni uygulama Azure Cosmos DB bağlandıktan sonra, istemci isteklerini yeni dağıtımınıza yeniden yönlendirebilirsiniz.

1. Artık ihtiyacınız olmayan kaynakları silin.

    Artık yeni örneğe tamamen yeniden yönlendirilen isteklerle, eski Azure Cosmos DB hesabını ve canlı veri Migrator aracını silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos hesabının yanı sıra veritabanlarının ve kapsayıcıların nasıl yönetileceği hakkında daha fazla bilgi ve örnek için aşağıdaki makaleleri okuyun:

* [Azure Cosmos hesabını yönetme](how-to-manage-database-account.md)
* [Azure Cosmos DB akışı değiştirme](change-feed.md)
