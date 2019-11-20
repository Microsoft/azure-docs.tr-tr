---
title: Azure Cosmos DB çok yöneticili avantajları
description: Azure Cosmos DB'de çok yöneticili avantajlarını öğrenin.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: mjbrown
ms.openlocfilehash: c78e5e4f8d396d777738bddfd6baf086c0b2ecf4
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789299"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Azure Cosmos DB'de çok yöneticili yararlarını

Cosmos DB hesap operatörleri, gecikme süresi, kullanılabilirlik ve uygulamalarını RTO gereksinimleri sağlamak için uygun genel dağıtım yapılandırmasını seçmeniz gerekir. Azure Cosmos hesapları birden fazla yazma konumları ile yapılandırılmış tek bir yazma konumunu da dahil olmak üzere ile hesapları üzerinden önemli avantajlar sunar, % 99,999 kullanılabilirlik SLA'sı, yazma < 10 ms yazma gecikme süresi SLA'sı 99. yüzdebirlik ve RTO = 0 bölgesel bir olağanüstü durum içinde.

## <a name="comparison-of-features"></a>Özelliklerin karşılaştırması

|Uygulama dağıtımı gereksinimi|Birden çok yazma konumları|Tek bir yazma konumu|Not|
|---|---|---|---|
|Yazma gecikme süresi SLA'sı < p99 10ms|**Evet**|Hayır|Hesapları tek yazma konumuna sahip ek bölgeler arası ağ gecikme süresi için her bir yazma yansıtılmaz.|
|Okuma gecikme süresi SLA'sı < p99 10ms|**Evet**|Evet| |
|% 99,999 SLA'sı yazma|**Evet**|Hayır|Hesapları tek yazma konumuna sahip % 99,99 SLA'sı garanti|
|RTO = 0|**Evet**|Hayır|Bölgesel bir olağanüstü durumunda yazma işlemleri için kesinti sıfır. RTO 15 dakika, tek bir yazma konumuna sahip hesapları vardır.|

## <a name="next-steps"></a>Sonraki Adımlar

Yine de Azure Cosmos hesabınızdaki EnableMultipleWriteLocations devre dışı bırakmak istiyorsanız, lütfen [bir destek bileti açın](https://azure.microsoft.com/support/create-ticket/).
