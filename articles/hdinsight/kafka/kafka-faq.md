---
title: Azure HDInsight 'ta Apache Kafka hakkında SSS
description: Yönetilen bir Hadoop bulut hizmeti olan Azure HDInsight üzerinde Apache Kafka hakkında sık sorulan soruların yanıtlarını alın.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 851d7ceb44d2466ed31e26c1442fde9acea9fd22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939097"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>Azure HDInsight'ta Apache Kafka hakkında sık sorulan sorular

Bu makalede, Azure HDInsight üzerinde Apache Kafka kullanmayla ilgili bazı yaygın soruların ele alınmaktadır.

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>HDInsight tarafından desteklenen Kafka sürümleri nelerdir?

HDInsight [ile kullanılabilen Apache Hadoop bileşenleri ve sürümleri nelerdir?](../hdinsight-component-versioning.md#supported-hdinsight-versions)HDInsight 'ta resmi olarak desteklenen bileşen sürümleri hakkında daha fazla bilgi edinin. En iyi olası performans ve Kullanıcı deneyimini sağlamak için her zaman en son sürümü kullanmanızı öneririz.

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>HDInsight Kafka kümesinde hangi kaynaklar sunulur ve ne kadar ücret ödersiniz?

HDInsight Kafka kümesi aşağıdaki kaynakları içerir:

* Baş düğümler
* Zookeeper düğümleri
* Aracı (çalışan) düğümleri 
* Aracı düğümlerine eklenen Azure yönetilen diskler
* Ağ Geçidi düğümleri

Bu kaynakların tümü, ağ geçidi düğümleri hariç, [HDInsight fiyatlandırma modelinize](https://azure.microsoft.com/pricing/details/hdinsight/)göre ücretlendirilir. Ağ Geçidi düğümleri için ücretlendirilirsiniz.

Çeşitli düğüm türlerinin daha ayrıntılı bir açıklaması için bkz. [Azure HDInsight sanal ağ mimarisi](../hdinsight-virtual-network-architecture.md). Fiyatlandırma, dakika başına düğüm kullanımını temel alır. Fiyatlar, düğüm boyutuna, düğüm sayısına, kullanılan yönetilen disk türüne ve bölgeye göre farklılık gösterir.

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>Apache Kafka API 'Leri HDInsight ile çalışıyor mu?

Evet, HDInsight yerel Kafka API 'Lerini kullanır. İstemci uygulama kodunuzun değiştirilmesi gerekmiyor. Bkz. Öğretici: küme ile Java tabanlı üretici/tüketici API 'Lerini nasıl kullanabileceğinizi görmek için [Apache Kafka Producer ve tüketici API 'Lerini kullanın](./apache-kafka-producer-consumer-api.md) .

## <a name="can-i-change-cluster-configurations"></a>Küme yapılandırmasını değiştirebilir miyim?

Evet, ambarı portalı üzerinden. Portaldaki her bileşen, bileşen yapılandırmalarını değiştirmek için kullanılabilecek bir **yapılandırmalarını** bölümüne sahiptir. Bazı değişiklikler, aracı yeniden başlatmaları gerektirebilir.

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>HDInsight ne tür bir kimlik doğrulaması Apache Kafka destekler?

[Kurumsal güvenlik paketi (ESP)](../domain-joined/apache-domain-joined-architecture.md)kullanarak, Kafka kümeleri için konu düzeyi güvenlik sağlayabilirsiniz. Daha fazla bilgi için bkz. [öğreticide HDInsight 'ta Apache Kafka Ilkeleri yapılandırma: kurumsal güvenlik paketi (Önizleme)](../domain-joined/apache-domain-joined-run-kafka.md).

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>Verilerim şifrelenmez mi? Kendi anahtarlarımı kullanabilir miyim?

Yönetilen disklerdeki tüm Kafka iletileri [Azure depolama hizmeti şifrelemesi (SSE)](../../storage/common/storage-service-encryption.md)ile şifrelenir. Aktarım verileri (örneğin, istemcilerden aracılardan ve diğer şekilde) aktarılan veriler varsayılan olarak şifrelenmez. [Kendi kendınıze TLS ayarlayarak](./apache-kafka-ssl-encryption-authentication.md)bu trafiği şifrelemek mümkündür. Ayrıca HDInsight, bekleyen verileri şifrelemek için kendi anahtarlarını yönetmenizi sağlar. Daha fazla bilgi için bkz. [müşteri tarafından yönetilen anahtar disk şifrelemesi](../disk-encryption.md).

## <a name="how-do-i-connect-clients-to-my-cluster"></a>Nasıl yaparım? istemcileri kümeme bağlanacak mı?

Kafka istemcilerinin Kafka aracıları ile iletişim kurması için, ağ üzerinden aracılara ulaşabilmesi gerekir. HDInsight kümeleri için, sanal ağ (VNet) güvenlik sınırıdır. Bu nedenle, istemcileri HDInsight kümenize bağlanmanın en kolay yolu, kümeyle aynı VNet içinde istemci oluşturmaktır. Diğer senaryolar şunlardır:

* İstemcileri farklı bir Azure sanal ağı ile bağlama – küme VNet ve istemci VNet 'i eşler ve [IP tanıtımı](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising)için kümeyi yapılandırın. IP tanıtımı kullanılırken, Kafka istemcilerinin, tam etki alanı adları (FQDN) yerine aracılarla bağlantı kurmak için aracı IP adreslerini kullanması gerekir.

* Şirket içi istemcileri bağlama – bir VPN ağı kullanarak ve [Azure HDInsight için bir sanal ağ planlayın](../hdinsight-plan-virtual-network-deployment.md)bölümünde açıklandığı gıbı özel DNS sunucuları ayarlama.

* Kafka hizmetiniz için genel bir uç nokta oluşturma – kurumsal güvenlik gereksinimleriniz buna izin verirseniz, Kafka aracılarınız için genel bir uç nokta veya genel bir uç nokta ile otomatik olarak yönetilen bir açık kaynak REST uç noktası dağıtabilirsiniz.

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>Var olan bir kümeye daha fazla disk alanı ekleyebilir miyim?

Kafka iletileri için kullanılabilir alan miktarını artırmak için düğümlerin sayısını artırabilirsiniz. Şu anda var olan bir kümeye daha fazla disk eklenmesi desteklenmez.

## <a name="can-a-kafka-cluster-work-with-databricks"></a>Kafka kümesi, Databricks ile çalışabilir mi? 

Evet, Kafka kümeler Databricks ile birlikte çalışarak aynı VNet 'te oldukları sürece bu şekilde çalışabilir. Databricks ile bir Kafka kümesi kullanmak için, bir HDInsight Kafka kümesiyle VNet oluşturun ve sonra Databricks çalışma alanınızı oluşturup VNet ekleme 'yi kullandığınızda bu sanal ağı belirtin. Daha fazla bilgi için bkz. [Azure sanal ağınızda Azure Databricks dağıtma (VNet ekleme)](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject). Databricks çalışma alanını oluştururken Kafka kümesinin önyükleme Aracısı adlarını sağlamanız gerekir. Kafka Broker adlarını alma hakkında daha fazla bilgi için bkz. [Apache Zookeeper ve aracı ana bilgisayar bilgilerini alma](./apache-kafka-get-started.md#getkafkainfo).

## <a name="how-can-i-have-maximum-data-durability"></a>Maksimum veri dayanıklılığını nasıl kullanabilirim?

Veri dayanıklılığı, en düşük ileti kaybı riskini elde etmenizi sağlar. En yüksek veri dayanıklılık düzeyine ulaşmak için aşağıdaki ayarları öneririz:

* çoğu bölgede en az 3 çoğaltma faktörü kullanın
* yalnızca iki hata etki alanı olan bölgelerde en az 4 çoğaltma faktörü kullanın
* temizleyen önmaları devre dışı bırak
* **Min. InSync. çoğaltmalar** sayısını 2 veya daha fazla olarak ayarlayın. Bu, bir yazma işleminin devam edebilmesi için, öncü ile tamamen eşitlenmesi gereken çoğaltmaların sayısını değiştirir
* **ACK** özelliğini **All** olarak ayarlama-bu özellik tüm çoğaltmaların tüm iletileri kabul etmesi gerekir

Daha yüksek veri tutarlılığı için Kafka yapılandırmak, isteklerin üretilmesi için aracıların kullanılabilirliğini etkiler.

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>Verilerimi birden çok kümeye çoğaltabilir miyim?

Evet, veriler Kafka MirrorMaker kullanılarak birden çok kümeye çoğaltılabiliyor. Bkz. MirrorMaker ayarlama ayrıntıları, [yansıtma Apache Kafka konularında](apache-kafka-mirroring.md)bulunabilir. Ayrıca, [Brooklin](https://github.com/linkedin/Brooklin/)gibi birden çok kümeye çoğaltma sağlamaya yardımcı olabilecek, otomatik olarak yönetilen diğer açık kaynaklı teknolojiler ve satıcılar de mevcuttur.

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>Kümemi yükseltebilir miyim? Kümemi nasıl yükseltmem gerekir?

Şu anda yerinde küme sürümü yükseltmelerini desteklemiyoruz. Kümenizi daha yüksek bir Kafka sürümüne güncelleştirmek için, istediğiniz sürümle yeni bir küme oluşturun ve Kafka istemcilerinizi yeni kümeyi kullanacak şekilde geçirin.

## <a name="how-do-i-monitor-my-kafka-cluster"></a>Kafka kümemi izleme Nasıl yaparım??

[Kafka günlüklerinizi](./apache-kafka-log-analytics-operations-management.md)çözümlemek için Azure izleyici 'yi kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure HDInsight 'ta Apache Kafka için TLS şifrelemesini ve kimlik doğrulamasını ayarlama](./apache-kafka-ssl-encryption-authentication.md)
* [MirrorMaker kullanarak Apache Kafka konularını HDInsight üzerinde Kafka ile çoğaltma](./apache-kafka-mirroring.md)