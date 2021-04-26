---
title: Azure Arc etkin PostgreSQL Hyperscale nedir?
description: Azure Arc etkin PostgreSQL Hyperscale nedir?
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: 8edf540c3f67a3a8bee075569f0a2588cae18a62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100390020"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc etkin PostgreSQL Hyperscale nedir?

Azure Arc etkin PostgreSQL hiper ölçek, Azure Arc etkin veri Hizmetleri 'nin bir parçası olarak sunulan veritabanı hizmetlerinden biridir. Azure Arc, Azure veri Hizmetleri 'ni şirket içinde, kenarda ve Kubernetes ve tercih ettiğiniz altyapıyı kullanarak genel bulutlarda çalıştırmanızı olanaklı kılar. Azure Arc etkin veri hizmetlerinin değer teklifi şu şekilde ifade edilecek:
- Her zaman geçerli
- Esnek ölçek
- Self Servis sağlama
- Birleşik yönetim
- Bağlantısı kesik senaryo desteği

Ayrıntılar hakkında daha fazla bilgi edinin:
- [Azure Arc etkin veri Hizmetleri nedir?](overview.md)
- [Bağlantı modları ve gereksinimler](connectivity.md)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="compare-solutions"></a>Çözümleri karşılaştırma

Bu bölümde, Azure Arc 'ın PostgreSQL hiper ölçeklendirmenin, PostgreSQL için Azure veritabanı hiper ölçek (Citus) ile farkı açıklanmaktadır?

## <a name="azure-database-for-postgresql-hyperscale-citus"></a>PostgreSQL için Azure Veritabanı Hiper Ölçek (Citus)

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="PostgreSQL için Azure SQL veritabanı hiper ölçek (Citus)":::

Bu, Azure 'da hizmet olarak veritabanı olarak kullanılabilen Postgres veritabanı altyapısının hiper ölçek formu etkendir (PaaS). Hiper ölçek deneyimini sağlayan Citus uzantısı tarafından desteklenir. Bu form faktöründe, hizmet Microsoft veri merkezlerinde çalışır ve Microsoft tarafından işletilir.

## <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc etkin PostgreSQL hiper ölçeği

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="Azure Arc etkin PostgreSQL hiper ölçeği":::

Bu, Azure Arc etkin veri Hizmetleri ile kullanılabilen Postgres veritabanı altyapısının hiper ölçekli form faktördür. Ayrıca, hiper ölçek deneyimini sağlayan Citus uzantısı tarafından da desteklenir. Bu form faktöründe, müşterilerimiz sistemleri barındıran altyapıyı sağlar ve bunları çalışır.

## <a name="next-steps"></a>Sonraki adımlar
- **Deneyin.** Azure Kubernetes Service (AKS), AWS elastik Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) veya bir Azure VM 'de [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) ile hızlıca çalışmaya başlayın. 

- **Kendinizinkini oluşturun.** Kendi Kubernetes kümenizde oluşturmak için aşağıdaki adımları izleyin: 
   1. [İstemci araçları 'nı yükler](install-client-tools.md)
   2. [Azure Arc veri denetleyicisi oluşturma](create-data-controller.md)
   3. [Azure Arc 'da PostgreSQL için Azure veritabanı hiper ölçek sunucu grubu oluşturma](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Azure Arc etkin veri hizmetleri hakkında daha fazla bilgi edinin](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Azure Arc hakkında bilgi edinin](https://aka.ms/azurearc)
