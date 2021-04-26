---
title: Azure HDInsight sanal ağ mimarisi
description: Bir Azure sanal ağında HDInsight kümesi oluştururken kullanılabilir kaynakları öğrenin.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 43640a9de91b6b44a236090ff029a49313ee0247
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871682"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Azure HDInsight sanal ağ mimarisi

Bu makalede, bir HDInsight kümesini özel bir Azure sanal ağına dağıtırken mevcut olan kaynaklar açıklanmaktadır. Bu bilgiler, şirket içi kaynaklarınızı Azure 'daki HDInsight kümenize bağlamanıza yardımcı olur. Azure sanal ağları hakkında daha fazla bilgi için bkz. [Azure sanal ağı nedir?](../virtual-network/virtual-networks-overview.md).

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Azure HDInsight kümelerinde kaynak türleri

Azure HDInsight kümelerinde farklı türlerde sanal makineler veya düğümler vardır. Her düğüm türü, sistem işleminde bir rol oynar. Aşağıdaki tabloda bu düğüm türleri ve bunların rolleri kümede özetlenmektedir.

| Tür | Description |
| --- | --- |
| Baş düğüm |  Apache Storm dışındaki tüm küme türleri için baş düğümler, dağıtılmış uygulamanın yürütülmesini yöneten süreçler barındırır. Baş düğüm aynı zamanda, SSH ve küme kaynakları genelinde çalışacak şekilde koordine ettiğiniz uygulamaları yürütebilmeniz için de düğümdür. Baş düğümlerin sayısı, tüm küme türleri için iki olarak düzeltilir. |
| ZooKeeper düğümü | Zookeeper, veri işleme yapan düğümler arasındaki görevleri düzenler. Ayrıca baş düğümün öncü seçimi de yapar ve hangi baş düğümün belirli bir ana hizmetin çalıştığını izler. ZooKeeper düğümlerinin sayısı üç olarak düzeltilir. |
| Çalışan düğümü | Veri işleme işlevselliğini destekleyen düğümleri temsil eder. İşlem yeteneklerini ölçeklendirmek ve maliyetleri yönetmek için çalışan düğümleri kümeden eklenebilir veya kaldırılabilir. |
| R Server Edge düğümü | R Server Edge düğümü, daha sonra küme kaynaklarında çalışacak şekilde koordine ettiğiniz ve bu uygulamaları yürütebilmeniz gereken düğümü temsil eder. Bir Edge düğümü, küme içindeki veri analizine katılmaz. Bu düğüm, r Studio Server 'ı da barındırır ve bir tarayıcı kullanarak R uygulaması çalıştırmanızı sağlar. |
| Bölge düğümü | HBase küme türü için bölge düğümü (veri düğümü olarak da anılır) bölge sunucusunu çalıştırır. Bölge sunucuları HBase tarafından yönetilen verilerin bir kısmını sunar ve yönetir. Bilgi işlem yeteneğini ölçeklendirmek ve maliyetleri yönetmek için, bölge düğümleri kümeden eklenebilir veya kaldırılabilir.|
| Nimbus düğümü | Nimbus düğümü, fırtınası kümesi türü için baş düğüme benzer işlevler sağlar. Nimbus düğümü, Zookeeper aracılığıyla bir kümedeki diğer düğümlere görevler atar ve bu da halka topolojilerinin çalıştırılmasını koordine eder. |
| Gözetmen düğümü | Fırtınası küme türü için, gözetmen düğümü, işlemi yapmak için Nimbus düğümü tarafından sunulan yönergeleri yürütür. |

## <a name="resource-naming-conventions"></a>Kaynak adlandırma kuralları

Kümenizdeki düğümleri adreslarken tam etki alanı adlarını (FQDN) kullanın. [AMBARı API](hdinsight-hadoop-manage-ambari-rest-api.md)'sini kullanarak kümenizdeki çeşitli düğüm türleri için FQDN 'leri edinebilirsiniz.

Bu FQDN 'Ler form olur `<node-type-prefix><instance-number>-<abbreviated-clustername>.<unique-identifier>.cx.internal.cloudapp.net` .

, İş `<node-type-prefix>` düğümleri için *hn* , çalışan düğümleri için *WN* ve Zookeeper düğümleri için *Zn* olacaktır.

Yalnızca ana bilgisayar adına ihtiyacınız varsa, FQDN 'nin yalnızca ilk kısmını kullanın: `<node-type-prefix><instance-number>-<abbreviated-clustername>`

## <a name="basic-virtual-network-resources"></a>Temel sanal ağ kaynakları

Aşağıdaki diyagramda HDInsight düğümlerinin ve ağ kaynaklarının Azure 'da yerleştirilmesi gösterilmektedir.

:::image type="content" source="./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png" alt-text="Azure özel VNET 'te oluşturulan HDInsight varlıklarının diyagramı" border="false":::

Bir Azure sanal ağındaki varsayılan kaynaklar, önceki tabloda belirtilen küme düğümü türlerini içerir. Ve sanal ağ ve dış ağlar arasındaki iletişimi destekleyen ağ cihazları.

Aşağıdaki tabloda, HDInsight özel bir Azure sanal ağına dağıtıldığında oluşturulan dokuz küme düğümü özetlenmektedir.

| Kaynak türü | Sayı var | Ayrıntılar |
| --- | --- | --- |
|Baş düğüm | iki |    |
|Zookeeper düğümü | Üç | |
|Çalışan düğümü | iki | Bu sayı, küme yapılandırmasına ve ölçeklendirilmesine göre farklılık gösterebilir. Apache Kafka için en az üç çalışan düğümü gerekir.  |
|Ağ geçidi düğümü | iki | Ağ Geçidi düğümleri Azure 'da oluşturulan ancak aboneliğinizde görünmeyen Azure sanal makinelerdir. Bu düğümleri yeniden başlatmanız gerekiyorsa desteğe başvurun. |

Mevcut olan aşağıdaki ağ kaynakları, HDInsight ile kullanılan sanal ağ içinde otomatik olarak oluşturulur:

| Ağ kaynağı | Sayı var | Ayrıntılar |
| --- | --- | --- |
|Yük dengeleyici | Üç | |
|Ağ Arabirimleri | lusu | Bu değer, her düğümün kendi ağ arabirimine sahip olduğu normal bir kümeyi temel alır. Dokuz arabirim şunlardır: iki baş düğüm, üç Zookeeper düğümü, iki çalışan düğümü ve önceki tabloda belirtilen iki ağ geçidi düğümü. |
|Genel IP Adresleri | iki |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>HDInsight 'a bağlanmak için uç noktalar

HDInsight kümenize üç yolla erişebilirsiniz:

- Üzerinde sanal ağın dışında bir HTTPS uç noktası `CLUSTERNAME.azurehdinsight.net` .
- Konumundaki baş düğümüne 'a doğrudan bağlanmak için bir SSH uç noktası `CLUSTERNAME-ssh.azurehdinsight.net` .
- Sanal ağ içinde bir HTTPS uç noktası `CLUSTERNAME-int.azurehdinsight.net` . `-int`Bu URL 'de "" olduğuna dikkat edin. Bu uç nokta, bu sanal ağdaki özel bir IP 'ye çözümlenir ve genel İnternet 'ten erişilemez.

Bu üç uç noktaya her biri bir yük dengeleyici atanır.

Genel IP adresleri, sanal ağın dışından bağlantıya izin veren iki uç noktaya de sağlanır.

1. Bir genel IP, kümeye internet 'ten bağlanırken kullanılacak tam etki alanı adı (FQDN) için yük dengeleyiciye atanır `CLUSTERNAME.azurehdinsight.net` .
1. İkinci genel IP adresi yalnızca SSH etki alanı adı için kullanılır `CLUSTERNAME-ssh.azurehdinsight.net` .

## <a name="next-steps"></a>Sonraki adımlar

- [Özel uç nokta ile bir sanal ağdaki HDInsight kümelerine gelen trafiği güvenli hale getirme](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
