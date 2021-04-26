---
title: Azure portal 'den Apache Cassandra için Azure yönetilen örneği hakkında sık sorulan sorular
description: Apache Cassandra için Azure yönetilen örneği hakkında sık sorulan sorular. Bu makalede, yönetilen örnekler, avantajlar, üretilen iş sınırları, desteklenen bölgeler ve diğer yapılandırma ayrıntılarının ne zaman kullanılacağı hakkındaki sorular ele alınmaktadır.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 1ba2b7d648c86912118b83a566bf2eb0800baee2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748952"
---
# <a name="frequently-asked-questions-about-azure-managed-instance-for-apache-cassandra-preview"></a>Apache Cassandra için Azure yönetilen örneği hakkında sık sorulan sorular (Önizleme)

Bu makalede Apache Cassandra için Azure yönetilen örneği hakkında sık sorulan sorular ele alınmaktadır. Yönetilen örneklerin ne zaman kullanılacağını, avantajları, verimlilik sınırlarını, desteklenen bölgeleri ve bunların yapılandırma ayrıntılarını öğreneceksiniz.

> [!IMPORTANT]
> Apache Cassandra için Azure yönetilen örneği şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="general-faq"></a>Genel SSS

### <a name="what-are-the-benefits-azure-managed-instance-for-apache-cassandra"></a>Apache Cassandra için Azure yönetilen örnek avantajları nelerdir?

Apache Cassandra veritabanı, performansı tehlikeye atmadan ölçeklenebilirlik ve yüksek kullanılabilirlik gerektiğinde doğru seçenektir. Bu, doğrusal ölçeklenebilirlik ve emtia donanım ya da bulut altyapısında kanıtlanmış hata toleransı nedeniyle görev açısından kritik verilere yönelik harika bir platformdur. Apache Cassandra için Azure yönetilen örneği, Azure 'da dağıtılan açık kaynaklı Apache Cassandra veri merkezlerinin örneklerini yönetmek için bir hizmettir.

Tamamen bulutta ya da karma bulutun ve şirket içi kümenin bir parçası olarak kullanılabilir. Bu hizmet, açık kaynaklı Apache Cassandra 'da, herhangi bir bakım yükü olmadan daha ayrıntılı yapılandırma ve denetim sağlamak istediğinizde harika bir seçenektir.

### <a name="why-should-i-use-this-service-instead-of-azure-cosmos-db-cassandra-api"></a>Neden bu hizmeti Azure Cosmos DB Cassandra API yerine kullanmalıyım?

Apache Cassandra için Azure yönetilen örneği Azure Cosmos DB ekibi tarafından dağıtılır. Açık kaynaklı Apache Cassandra veri merkezleri ve kümelerinin dağıtımı, sürdürülmesi ve ölçeklendirilmesi için tek başına yönetilen bir hizmettir. Diğer yandan [Azure Cosmos DB Cassandra API](../cosmos-db/cassandra-introduction.md) , Apache Cassandra tel protokolü için birlikte çalışabilirlik katmanı sağlayan bir hizmet olarak platform ' dır. Beklentiğinizi, her Apache Cassandra kümesiyle tamamen aynı şekilde davranması için ise, yönetilen örnek hizmetini seçmeniz gerekir. Daha fazla bilgi için bkz. [Apache Cassandra Vs Azure Cosmos DB Cassandra API Için Azure yönetilen örneği](compare-cosmosdb-managed-instance.md) .

### <a name="is-azure-managed-instance-for-apache-cassandra-dependent-on-azure-cosmos-db"></a>Apache Cassandra için Azure yönetilen örneği Azure Cosmos DB bağımlıdır mi?

Hayır, Apache Cassandra için Azure yönetilen örneği ve Azure Cosmos DB arka ucu arasında mimari bağımlılığı yoktur. 

#### <a name="can-i-deploy-azure-managed-instance-for-apache-cassandra-in-any-region"></a>Apache Cassandra için Azure yönetilen örneğini herhangi bir bölgede dağıtabilir miyim?

Yönetilen örnek, önizleme sırasında sınırlı sayıda bölgede kullanılabilir olacaktır.

### <a name="what-are-the-storage-and-throughput-limits-of-azure-managed-instance-for-apache-cassandra"></a>Apache Cassandra için Azure yönetilen örneğinin depolama ve aktarım hızı sınırları nelerdir?

Bu sınırlar, seçtiğiniz sanal makine SKU 'Larına bağlıdır.

### <a name="what-is-the-cost-of-azure-managed-instance-for-apache-cassandra"></a>Apache Cassandra için Azure yönetilen örneği 'nin maliyeti nedir?

Yönetilen örnek ücretleri, küçük bir biçimlendirme ile temel alınan VM maliyetine göre yapılır. Daha fazla bilgi edinmek için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/managed-instance-apache-cassandra/).

### <a name="can-i-use-yaml-file-settings-to-configure-behavior"></a>Davranışı yapılandırmak için YAML dosya ayarlarını kullanabilir miyim?

Evet, YAML dosya yapılandırmalarının Azure Resource Manager şablon dağıtımının bir parçası olarak katıştırılabilir.

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>İş verimini birlikte altyapıyı nasıl izleyebilirim?

[Prometheus](https://prometheus.io/docs/introduction/overview/) sunucusu, kümenizin içindeki etkinlikleri izlemek için barındırılır ve bir uç nokta kullanıma sunuyor. Bu, 10 dakika veya 10 GB veri tutar (öncelikle eşiğe ulaşıldığında). Bu izlemeyi kullanmak için, bir [Federasyon](https://prometheus.io/docs/prometheus/latest/federation/) ve Grafana gibi uygun bir pano aracı ayarlamanız gerekir.

### <a name="does-azure-managed-instance-for-apache-cassandra-provide-full-backups"></a>Apache Cassandra için Azure yönetilen örneği tam yedeklemeler sağlar mi?

Evet, Azure depolama 'ya tam yedeklemeler sağlar ve yeni bir kümeye geri yükler

### <a name="how-can-i-migrate-data-from-my-existing-apache-cassandra-cluster-to-azure-managed-instance-for-apache-cassandra"></a>Mevcut Apache Cassandra kümemdeki verileri Apache Cassandra için Azure yönetilen örneği 'ne nasıl geçirebilirim?

Apache Cassandra için Azure yönetilen örneği, veri merkezleri arasında verileri çoğaltmak ve akışa almak için Apache Cassandra 'daki tüm özellikleri destekler.

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-azure-managed-instance-for-apache-cassandra"></a>Bir şirket içi Apache Cassandra kümesini Apache Cassandra için Azure yönetilen örneğiyle eşlebilirim miyim?

Evet, hizmet tarafından dağıtılan Azure sanal ağı eklenmiş veri merkezleri ile bir karma küme yapılandırabilirsiniz. Yönetilen örnek veri merkezleri, aynı küme halkası içindeki şirket içi veri merkezleriyle iletişim kurabilir.

### <a name="where-can-i-give-feedback-on-azure-managed-instance-for-apache-cassandra-features"></a>Apache Cassandra özellikleri için Azure yönetilen örneği hakkında geri bildirimde bulunabilirim?

"Managed Apache Cassandra" kategorisini kullanarak [Kullanıcı sesli geri bildirimi](https://feedback.azure.com/forums/263030-azure-cosmos-db?category_id=398548) aracılığıyla geri bildirim sağlayın.

Hesabınızla ilgili bir sorun gidermek için Azure portalda bir [destek isteği](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) oluşturun.

## <a name="deployment-specific-faq"></a>Dağıtıma özgü SSS

### <a name="will-the-managed-instance-support-node-addition-cluster-status-and-node-status-commands"></a>Yönetilen örnek düğüm ekleme, küme durumu ve düğüm durumu komutlarını destekliyor mu?

Gibi tüm *salt okunurdur* nodetool komutları `status` Azure CLI aracılığıyla kullanılabilir. Ancak, yönetilen örnekteki düğümlerin sistem durumunu yönettiğimiz için *düğüm ekleme* gibi işlemler kullanılamaz. Karma modda, *Nodetool* ile kümeye bağlanabilirsiniz. Ancak, Nodetool 'un kullanılması, kümeyi bozmadığından önerilmez. Ayrıca, kümedeki yönetilen örnek veri merkezlerinin durumuyla ilgili üretim desteği SLA 'sını geçersiz kılabilir.

### <a name="what-happens-with-various-settings-for-table-metadata"></a>Tablo meta verileri için çeşitli ayarlarla ne olur?

Bloom filtresi, önbelleğe alma, okuma onarım olasılığı, gc_grace ve sıkıştırma memtable_flush_period gibi tablo meta verileri ayarları, şirket içinde barındırılan Apache Cassandra ortamıyla birlikte tam olarak desteklenmektedir.

## <a name="next-steps"></a>Sonraki adımlar

Diğer API 'lerde sıkça sorulan sorular hakkında bilgi edinmek için bkz.:

* [Azure portal yönetilen örnek kümesi oluşturma](create-cluster-portal.md)
* [Azure Databricks ile yönetilen Apache Spark kümesi dağıtma](deploy-cluster-databricks.md)
* [Azure CLı kullanarak Apache Cassandra kaynakları için Azure yönetilen örneğini yönetme](manage-resources-cli.md)