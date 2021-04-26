---
title: Apache Cassandra için Azure yönetilen örneğine giriş
description: Apache Cassandra için Azure yönetilen örneği hakkında bilgi edinin. Bu hizmet, Azure 'da Apache Cassandra 'ın yerel açık kaynaklı örneklerinin dağıtımını ve ölçeğini yönetir.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: overview
ms.date: 03/02/2021
ms.openlocfilehash: d99c62e7d88510c351f87d85b4f8c5c271767cb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748933"
---
# <a name="what-is-azure-managed-instance-for-apache-cassandra-preview"></a>Apache Cassandra için Azure yönetilen örneği nedir? (Önizleme)

> [!IMPORTANT]
> Apache Cassandra için Azure yönetilen örneği şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Apache Cassandra hizmeti için Azure yönetilen örneği, yönetilen açık kaynaklı Apache Cassandra veri merkezleri için otomatik dağıtım ve ölçeklendirme işlemleri sağlar. Bu hizmet, karma senaryoları hızlandırmanıza ve devam eden bakımın azaltılmasına yardımcı olur. Genel yayını üzerinde [Azure Cosmos DB Cassandra API](../cosmos-db/cassandra-introduction.md) kapsamlı tümleştirme ve veri taşıma özelliklerine sahip olacaktır.

:::image type="content" source="./media/introduction/icon.gif" alt-text="Apache Cassandra için Azure yönetilen örneği, Apache Cassandra için yönetilen bir hizmettir." border="false":::

## <a name="key-benefits"></a>Önemli avantajlar

### <a name="hybrid-deployments"></a>Karma dağıtımlar

Bu hizmeti kullanarak, sanal makine ölçek kümeleri olarak otomatik olarak dağıtılan Apache Cassandra datacenters 'ın yönetilen örneklerini yeni veya mevcut bir Azure sanal ağına kolayca yerleştirebilirsiniz. Bu veri merkezleri, şirket içinde çalışan mevcut Apache Cassandra halkasına Azure 'da Azure [ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute) aracılığıyla veya başka bir bulut ortamında eklenebilir.

- **Basitleştirilmiş dağıtım:** Karma Bağlantı kurulduktan sonra, GOSSIP protokolü aracılığıyla kolayca dağıtım yapılır.
- **Barındırılan ölçümler:** Ölçümler, kümenizin tamamında etkinlik izlemek için [Prometheus](https://prometheus.io/docs/introduction/overview/) içinde barındırılır.

### <a name="simplified-scaling"></a>Basitleştirilmiş ölçekleme

Yönetilen örnekte, bir veri merkezinde ölçek artırma ve ölçek azaltma düğümlerini tamamen yönetilirler. İhtiyacınız olan düğüm sayısını girersiniz ve ölçeklendirme Orchestrator, işlerini Cassandra halkası içinde oluşturma işlemini gerçekleştirir.

### <a name="managed-and-cost-effective"></a>Yönetilen ve ekonomik

Hizmet aşağıdaki genel Apache Cassandra görevleri için yönetim işlemleri sağlar:

- Küme sağlama
- Veri merkezi sağlama
- Veri merkezini ölçeklendirme
- Veri merkezini silme
- Anahtar alanı üzerinde onarım eylemi başlatma
- Bir veri merkezinin yapılandırmasını değiştirme
- Yedeklemeleri kurulum

Fiyatlandırma modeli esnektir, isteğe bağlı, örnek tabanlıdır ve lisans ücreti yoktur. Bu fiyatlandırma modeli, belirli iş yükü gereksinimlerinize göre ayarlamanıza olanak sağlar. Kaç çekirdek, sanal makine SKU 'SU, hangi bellek boyutu ve ihtiyacınız olan disk alanı boyutunu seçebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Hızlı başlangıçlardan birini kullanmaya başlayın:

* [Azure portal yönetilen örnek kümesi oluşturma](create-cluster-portal.md)
* [Azure Databricks ile yönetilen Apache Spark kümesi dağıtma](deploy-cluster-databricks.md)
* [Azure CLı kullanarak Apache Cassandra kaynakları için Azure yönetilen örneğini yönetme](manage-resources-cli.md)
