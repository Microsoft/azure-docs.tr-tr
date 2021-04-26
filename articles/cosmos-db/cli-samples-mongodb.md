---
title: MongoDB için Azure Cosmos DB API 'SI için Azure CLı örnekleri
description: MongoDB için Azure Cosmos DB API 'SI için Azure CLı örnekleri
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: b17d3b0072d893751586f87d9a4ceb7ac8607416
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93342105"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-api-for-mongodb"></a>MongoDB için Azure Cosmos DB API 'SI için Azure CLı örnekleri
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Aşağıdaki tablo, Azure Cosmos DB’ye yönelik örnek Azure CLI betiklerinin bağlantılarını içerir. API 'ye özel örneklere gitmek için sağdaki bağlantıları kullanın. Ortak örnekler tüm API 'lerde aynıdır. Tüm Azure Cosmos DB CLı komutlarına yönelik başvuru sayfaları, [Azure CLI başvurusunda](/cli/azure/cosmosdb)bulunabilir. Azure Cosmos DB CLı betik örnekleri, [Azure Cosmos DB CLI GitHub deposunda](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)da bulunabilir.

Bu örnekler için Azure CLı sürüm 2.12.1 veya üzeri gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse bkz. [Azure CLI 'Yı yüklemek](/cli/azure/install-azure-cli)

## <a name="common-samples"></a>Ortak örnekler

Bu örnekler tüm Azure Cosmos DB API 'Leri için geçerlidir

|Görev | Açıklama |
|---|---|
| [Yük devretme bölgesi ekleme veya](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Bir bölge ekleyin, yük devretme önceliğini değiştirin, el ile yük devretme tetikleyin.|
| [Hesap anahtarları ve bağlantı dizeleri](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Hesap anahtarlarını listeleyin, salt okunurdur, anahtarları yeniden oluşturun ve bağlantı dizelerini listeleyin.|
| [IP güvenlik duvarıyla güvenli hale getirme](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| IP güvenlik duvarı yapılandırılmış bir Cosmos hesabı oluşturun.|
| [Hizmet uç noktaları ile güvenli yeni hesap](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Hizmet uç noktaları ile Cosmos hesabı oluşturun ve güvenli hale getirin.|
| [Mevcut hesabı hizmet uç noktalarıyla güvenli hale getirin](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Alt ağ sonunda yapılandırıldığında hizmet uç noktalarıyla güvenli hale getirmek için Cosmos hesabını güncelleştirin.|
|||

## <a name="mongodb-api-samples"></a>MongoDB API örnekleri

|Görev | Açıklama |
|---|---|
| [Azure Cosmos hesabı, veritabanı ve koleksiyonu oluşturma](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| MongoDB API 'SI için bir Azure Cosmos DB hesabı, veritabanı ve koleksiyonu oluşturur. |
| [Paylaşılan verimlilik içeren bir Azure Cosmos hesabı, otomatik ölçeklendirme ve iki koleksiyon ile veritabanı oluşturma](scripts/cli/mongodb/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| MongoDB API 'SI için paylaşılan iş hızına sahip bir Azure Cosmos DB hesap, otomatik ölçeklendirme ve iki koleksiyon içeren bir veritabanı oluşturur. |
| [Aktarım hızı işlemleri](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Bir veritabanında ve koleksiyonda bulunan otomatik ölçeklendirme ve standart verimlilik arasında okuma, güncelleştirme ve geçirme.|
| [Kaynakları silinmeye karşı kilitle](scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Kaynakların kaynak kilitleri ile silinmesini engelleyin.|
|||
