---
title: Azure HDInsight sanal ağ mimarisi
description: Bir Azure sanal ağında HDInsight kümesi oluştururken kullanılabilir kaynakları öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 0a1139f7bf1711a5f6d980e67a8a9027bfd3af52
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73665317"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Azure HDInsight sanal ağ mimarisi

Bu makalede, bir HDInsight kümesini özel bir Azure sanal ağına dağıtırken mevcut olan kaynaklar açıklanmaktadır. Bu bilgiler, şirket içi kaynaklarınızı Azure 'daki HDInsight kümenize bağlamanıza yardımcı olur. Azure sanal ağları hakkında daha fazla bilgi için bkz. [Azure sanal ağı nedir?](../virtual-network/virtual-networks-overview.md).

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Azure HDInsight kümelerinde kaynak türleri

Azure HDInsight kümelerinde farklı türlerde sanal makineler veya düğümler vardır. Her düğüm türü, sistem işleminde bir rol oynar. Aşağıdaki tabloda bu düğüm türleri ve bunların rolleri kümede özetlenmektedir.

| Tür | Açıklama |
| --- | --- |
| Baş düğüm |  Apache Storm dışındaki tüm küme türleri için baş düğümler, dağıtılmış uygulamanın yürütülmesini yöneten süreçler barındırır. Baş düğüm aynı zamanda, SSH ve küme kaynakları genelinde çalışacak şekilde koordine ettiğiniz uygulamaları yürütebilmeniz için de düğümdür. Baş düğümlerin sayısı, tüm küme türleri için iki olarak düzeltilir. |
| ZooKeeper düğümü | Zookeeper, veri işleme yapan düğümler arasındaki görevleri düzenler. Ayrıca baş düğümün öncü seçimi de yapar ve hangi baş düğümün belirli bir ana hizmetin çalıştığını izler. ZooKeeper düğümlerinin sayısı üç olarak düzeltilir. |
| Çalışan düğümü | Veri işleme işlevselliğini destekleyen düğümleri temsil eder. İşlem yeteneklerini ölçeklendirmek ve maliyetleri yönetmek için çalışan düğümleri kümeden eklenebilir veya kaldırılabilir. |
| R Server Edge düğümü | R Server Edge düğümü, daha sonra küme kaynaklarında çalışacak şekilde koordine ettiğiniz ve bu uygulamaları yürütebilmeniz gereken düğümü temsil eder. Bir Edge düğümü, küme içindeki veri analizine katılmaz. Bu düğüm, r Studio Server 'ı da barındırır ve bir tarayıcı kullanarak R uygulaması çalıştırmanızı sağlar. |
| Bölge düğümü | HBase küme türü için bölge düğümü (veri düğümü olarak da anılır) bölge sunucusunu çalıştırır. Bölge sunucuları HBase tarafından yönetilen verilerin bir kısmını sunar ve yönetir. Bilgi işlem yeteneğini ölçeklendirmek ve maliyetleri yönetmek için, bölge düğümleri kümeden eklenebilir veya kaldırılabilir.|
| Nimbus düğümü | Nimbus düğümü, fırtınası kümesi türü için baş düğüme benzer işlevler sağlar. Nimbus düğümü, Zookeeper aracılığıyla bir kümedeki diğer düğümlere görevler atar ve bu da halka topolojilerinin çalıştırılmasını koordine eder. |
| Gözetmen düğümü | Halka küme türü için, gözetmen düğümü, istenen işlemi gerçekleştirmek için Nimbus düğümü tarafından sunulan yönergeleri yürütür. |

## <a name="basic-virtual-network-resources"></a>Temel sanal ağ kaynakları

Aşağıdaki diyagramda HDInsight düğümlerinin ve ağ kaynaklarının Azure 'da yerleştirilmesi gösterilmektedir.

![Azure özel VNET 'te oluşturulan HDInsight varlıklarının diyagramı](./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png)

HDInsight bir Azure sanal ağına dağıtıldığında mevcut olan varsayılan kaynaklar, önceki tabloda belirtilen küme düğümü türlerini ve sanal ağ ile dış ağlar arasındaki iletişimi destekleyen ağ cihazlarını içerir.

Aşağıdaki tabloda, HDInsight özel bir Azure sanal ağına dağıtıldığında oluşturulan dokuz küme düğümü özetlenmektedir.

| Kaynak türü | Sayı var | Ayrıntılar |
| --- | --- | --- |
|Baş düğüm | iki |    |
|Zookeeper düğümü | üç | |
|Çalışan düğümü | iki | Bu sayı, küme yapılandırmasına ve ölçeklendirilmesine göre farklılık gösterebilir. Apache Kafka için en az üç çalışan düğümü gerekir.  |
|Ağ geçidi düğümü | iki | Ağ Geçidi düğümleri Azure 'da oluşturulan ancak aboneliğinizde görünmeyen Azure sanal makinelerdir. Bu düğümleri yeniden başlatmanız gerekiyorsa desteğe başvurun. |

Mevcut olan aşağıdaki ağ kaynakları, HDInsight ile kullanılan sanal ağ içinde otomatik olarak oluşturulur:

| Ağ kaynağı | Sayı var | Ayrıntılar |
| --- | --- | --- |
|Yük dengeleyici | üç | |
|Ağ Arabirimleri | lusu | Bu değer, her düğümün kendi ağ arabirimine sahip olduğu normal bir kümeyi temel alır. Dokuz arabirim iki baş düğüm, üç Zookeeper düğümü, iki çalışan düğümü ve önceki tabloda belirtilen iki ağ geçidi düğümü içindir. |
|Genel IP Adresleri | iki |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>HDInsight 'a bağlanmak için uç noktalar

HDInsight kümenize üç yolla erişebilirsiniz:

- `CLUSTERNAME.azurehdinsight.net`konumundaki sanal ağın dışında bir HTTPS uç noktası.
- `CLUSTERNAME-ssh.azurehdinsight.net`konumundaki baş düğümüne 'a doğrudan bağlanmak için bir SSH uç noktası.
- Sanal ağ içinde bir HTTPS uç noktası `CLUSTERNAME-int.azurehdinsight.net`. Bu URL 'de "-int" olduğuna dikkat edin. Bu uç nokta, bu sanal ağdaki özel bir IP 'ye çözümlenir ve genel İnternet 'ten erişilemez.

Bu üç uç noktaya her biri bir yük dengeleyici atanır.

Genel IP adresleri, sanal ağın dışından bağlantıya izin veren iki uç noktaya de sağlanır.

1. Internet `CLUSTERNAME.azurehdinsight.net`kümeye bağlanırken kullanılacak tam etki alanı adı (FQDN) için yük dengeleyiciye tek bir genel IP atanır.
1. İkinci genel IP adresi, yalnızca SSH etki alanı adı `CLUSTERNAME-ssh.azurehdinsight.net`için kullanılır.

## <a name="next-steps"></a>Sonraki adımlar

- [Özel uç nokta ile bir sanal ağdaki HDInsight kümelerine gelen trafiği güvenli hale getirme](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
