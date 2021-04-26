---
title: Azure Cosmos DB için Azure PowerShell örnekleri Cassandra API
description: Azure Cosmos DB ortak görevleri gerçekleştirmek için Azure PowerShell örnekleri alın Cassandra API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 01/20/2021
ms.author: mjbrown
ms.openlocfilehash: 15e0f221141062b2239b588889036849d3b1d91e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102505214"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB için Azure PowerShell örnekleri Cassandra API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Aşağıdaki tabloda, Azure Cosmos DB için yaygın olarak kullanılan Azure PowerShell betiklerin bağlantıları yer almaktadır. API 'ye özel örneklere gitmek için sağdaki bağlantıları kullanın. Ortak örnekler tüm API 'lerde aynıdır. Tüm Azure Cosmos DB PowerShell cmdlet 'lerinin başvuru sayfaları [Azure PowerShell başvurusunda](/powershell/module/az.cosmosdb)bulunabilir. `Az.CosmosDB`Modül artık modülün bir parçasıdır `Az` . Azure Cosmos DB cmdlet 'lerini almak için az Module ' un en son sürümünü [indirip yükleyin](/powershell/azure/install-az-ps) . [PowerShell Galerisi](https://www.powershellgallery.com/packages/Az/5.4.0)en son sürümü de edinebilirsiniz. GitHub depomızdan Cosmos DB için bu PowerShell örneklerini de çatalla Cosmos DB, [GitHub 'Da PowerShell örnekleri](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb)' ni kullanabilirsiniz.

## <a name="common-samples"></a>Ortak örnekler

|Görev | Açıklama |
|---|---|
|[Hesap güncelleştirme](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB hesabının varsayılan tutarlılık düzeyini güncelleştirin. |
|[Hesabın bölgelerini güncelleştirme](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB hesabının bölgelerini güncelleştirin. |
|[Yük devretme önceliğini değiştirme veya yük devretmeyi tetikleme](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos hesabının bölgesel yük devretme önceliğini değiştirin veya el ile yük devretme tetikleyin. |
|[Hesap anahtarları veya bağlantı dizeleri](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Birincil ve ikincil anahtarları, bağlantı dizelerini alın veya Azure Cosmos DB hesabının hesap anahtarını yeniden oluşturun. |
|[IP güvenlik duvarı ile Cosmos hesabı oluşturma](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| IP Güvenlik Duvarı etkin olan bir Azure Cosmos DB hesabı oluşturun. |
|||

## <a name="cassandra-api-samples"></a>Cassandra API örnekleri

|Görev | Açıklama |
|---|---|
|[Hesap, anahtar alanı ve tablo oluşturma](scripts/powershell/cassandra/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos hesabı, anahtar alanı ve tablo oluşturur. |
|[Otomatik ölçeklendirme ile hesap, anahtar alanı ve tablo oluşturma](scripts/powershell/cassandra/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Otomatik ölçeklendirme ile bir Azure Cosmos hesabı, anahtar alanı ve tablo oluşturur. |
|[Anahtar alanları veya tabloları listeleme veya edinme](scripts/powershell/cassandra/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Anahtar alanları veya tabloları listeleyin veya alın. |
|[Aktarım hızı işlemleri](scripts/powershell/cassandra/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Anahtar uzayı veya tablo için, otomatik ölçeklendirme ve standart verimlilik arasında al, Güncelleştir ve geçir dahil işleme işlemleri. |
|[Kaynakları silinmeye karşı kilitle](scripts/powershell/cassandra/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Kaynakların kaynak kilitleri ile silinmesini engelleyin. |
|||
