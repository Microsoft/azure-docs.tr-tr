---
title: Azure HDInsight 'ta yüksek kullanılabilirlik bileşenleri
description: HDInsight kümeleri tarafından kullanılan çeşitli yüksek kullanılabilirlik bileşenlerine genel bakış.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 38fb45fd339b5e2c7cab6f66a1ed6c0df73fb29e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74069620"
---
# <a name="high-availability-services-supported-by-azure-hdinsight"></a>Azure HDInsight tarafından desteklenen yüksek kullanılabilirlik Hizmetleri

 Analiz bileşenleriniz için en uygun düzeylerde kullanılabilirlik sağlamak amacıyla, HDInsight, önemli hizmetlerin yüksek kullanılabilirliğini sağlamak için benzersiz bir mimariye geliştirilmiştir. Bu mimarinin bazı bileşenleri, Microsoft tarafından otomatik yük devretme sağlamak üzere geliştirilmiştir. Diğer bileşenler, belirli hizmetleri desteklemek için dağıtılan standart Apache bileşenleridir. Bu makalede, HDInsight 'ta HA hizmeti modelinin mimarisi, HDInsight 'ın HA Hizmetleri için yük devretmeyi nasıl desteklediği ve diğer hizmet kesintilerinden kurtarmak için en iyi uygulamalar açıklanmaktadır.

## <a name="high-availability-infrastructure"></a>Yüksek kullanılabilirlik altyapısı

HDInsight, dört birincil hizmetin otomatik yük devretme özellikleri ile yüksek kullanılabilirliğe sahip olduğundan emin olmak için özelleştirilmiş altyapı sağlar:

- Apache ambarı sunucusu
- Apache YARN için Uygulama Zaman Çizelgesi sunucusu
- Hadoop MapReduce için iş geçmişi sunucusu
- Apache Livy

Bu altyapı, bazıları Microsoft tarafından tasarlanan bir dizi hizmet ve yazılım bileşeninden oluşur. Aşağıdaki bileşenler HDInsight platformu için benzersizdir:

- Bağımlı yük devretme denetleyicisi
- Ana yük devretme denetleyicisi
- Bağımlı yüksek kullanılabilirlik hizmeti
- Ana yüksek kullanılabilirlik hizmeti

![Yüksek kullanılabilirlik altyapısı](./media/hdinsight-high-availability-components/high-availability-architecture.png)

Açık kaynaklı Apache güvenilirlik bileşenleri tarafından desteklenen diğer yüksek kullanılabilirlik hizmetleri de vardır. Bu bileşenler HDInsight kümelerinde da mevcuttur:

- Hadoop dosya sistemi (bir) süs Yot
- YARN ResourceManager
- HBase Master

Aşağıdaki bölümler, bu hizmetlerin birlikte nasıl çalıştığı hakkında daha fazla ayrıntı sağlayacaktır.

## <a name="hdinsight-high-availability-services"></a>HDInsight yüksek kullanılabilirlik Hizmetleri

Microsoft, HDInsight kümelerinde aşağıdaki tabloda bulunan dört Apache hizmeti için destek sağlar. Bileşenlere Apache tarafından desteklenen yüksek kullanılabilirlik hizmetlerinden ayırt etmek için, *HDıNSIGHT ha Hizmetleri*olarak adlandırılırlar.

| Hizmet | Küme düğümleri | Küme türleri | Amaç |
|---|---|---|---|
| Apache ambarı sunucusu| Etkin baş düğümüne | Tümü | Kümeyi izler ve yönetir.|
| Apache YARN için Uygulama Zaman Çizelgesi sunucusu | Etkin baş düğümüne | Kafka dışında tümü | Kümede çalışan YARN işleri hakkında hata ayıklama bilgilerini tutar.|
| Hadoop MapReduce için iş geçmişi sunucusu | Etkin baş düğümüne | Kafka dışında tümü | MapReduce işleri için hata ayıklama verilerini tutar.|
| Apache Livy | Etkin baş düğümüne | Spark | REST arabirimi üzerinde Spark kümesiyle kolay etkileşim imkanı sunar |

>[!Note]
> HDInsight Kurumsal Güvenlik Paketi (ESP) kümeleri Şu anda yalnızca bir ambarı sunucusu yüksek kullanılabilirliği sağlar.

### <a name="architecture"></a>Mimari

Her HDInsight kümesinin sırasıyla etkin ve bekleme modlarında iki yayın düğümü vardır. HDInsight ha Hizmetleri yalnızca baş üzerinde çalışır. Bu hizmetler her zaman etkin headnode üzerinde çalışmalıdır ve bekleme konumuna durdurulup bakım moduna konur.

HA hizmetlerinin doğru durumlarını korumak ve hızlı yük devretme sağlamak için HDInsight, dağıtılmış uygulamalar için bir düzenleme hizmeti olan Apache ZooKeeper kullanır ve bu da etkin baş düğüm seçimi yürütmek için kullanılır. HDInsight, HDInsight HA Hizmetleri için yük devretme yordamını koordine eden bazı arka plan Java işlemleri de sağlar. Bu hizmetler şunlardır: Ana yük devretme denetleyicisi, bağımlı yük devretme denetleyicisi, *Master-ha-Service*ve *bağımlı-ha-Service*.

### <a name="apache-zookeeper"></a>Apache ZooKeeper

Apache ZooKeeper, dağıtılmış uygulamalar için yüksek performanslı bir koordinasyon hizmetidir. Üretimde, ZooKeeper genellikle çoğaltılan modda çalışır ve bu da çoğaltılmış bir ZooKeeper sunucuları grubunun bir çekirdek oluşturur. Her HDInsight kümesinde, üç ZooKeeper sunucusuna çekirdek oluşturma izni veren üç ZooKeeper düğümü vardır. HDInsight ile paralel olarak çalışan iki ZooKeeper çekirdeklerine vardır. Bir çekirdek, HDInsight HA hizmetlerinin çalışması gereken bir kümede etkin olan yayın düğümüne karar verir. Başka bir çekirdek, daha sonraki bölümlerde açıklandığı gibi Apache tarafından sunulan HA hizmetlerini koordine etmek için kullanılır.

### <a name="slave-failover-controller"></a>Bağımlı yük devretme denetleyicisi

Bağımlı yük devretme denetleyicisi, HDInsight kümesindeki her düğümde çalışır. Bu denetleyici, her düğümde ambarı Aracısı ve *bağımlı-ha hizmeti* başlatmaktan sorumludur. Etkin yayın düğümü hakkında düzenli olarak ilk ZooKeeper çekirdeğini sorgular. Etkin ve hazır olan yayın düğümleri değiştiğinde, bağımlı yük devretme denetleyicisi şunları gerçekleştirir:

1. Ana bilgisayar yapılandırma dosyasını güncelleştirir.
1. Ambarı Aracısı 'nı yeniden başlatır.

*Bağımlı-ha-Service* , bekleyen headnode 'DA HDInsight ha Hizmetleri 'ni (örneğin, ambarı sunucusu hariç) durdurmaktan sorumludur.

### <a name="master-failover-controller"></a>Ana yük devretme denetleyicisi

Ana yük devretme denetleyicisi her iki yayın düğümünde çalışır. Her iki ana yük devretme denetleyicisi, etkin yayın düğümü olarak üzerinde çalıştıkları baş düğümüne 'u aday şekilde ilk ZooKeeper çekirdekle iletişim kurar.

Örneğin, ana düğüm 0 üzerinde ana yük devretme denetleyicisi seçimi WINS ise, aşağıdaki değişiklikler gerçekleşir:

1. 0\. headnode etkin hale gelir.
1. Ana yük devretme denetleyicisi, ana düğüm 0 ' da ambarı sunucusunu ve *Master-ha-Service* ' i başlatır.
1. Diğer ana yük devretme denetleyicisi, ana düğüm 1 ' de ambarı sunucusunu ve *Master-ha-Service* ' i durduruyor.

Master-ha-Service yalnızca etkin baş düğümüne üzerinde çalışır, bekleyen baş düğümüne 'da HDInsight ha hizmetlerini (hizmet ambarı sunucusu hariç) durdurup etkin bir yayın düğümünde başlatır.

### <a name="the-failover-process"></a>Yük devretme işlemi

![Yük devretme işlemi](./media/hdinsight-high-availability-components/failover-steps.png)

Bir sistem durumu İzleyicisi, Zookeeper çekirdeğe sinyal bildirimleri göndermek için ana yük devretme denetleyicisiyle birlikte her bir baş düğümüne üzerinde çalışır. Bu senaryoda, baş düğümüne bir ha hizmeti olarak kabul edilir. Sistem durumu İzleyicisi, her bir yüksek kullanılabilirlik hizmetinin sağlıklı olup olmadığını ve liderlik seçimi 'ne katılmayı hazır olup olmadığını denetler. Yanıt Evet ise, bu baş düğümüne seçim içinde rekabet eder. Aksi takdirde, tekrar hazır olana kadar seçimi çıkar.

Bekleyen yayın düğümü liderlik devreder ve etkin hale gelirse (önceki etkin düğüm ile ilgili bir başarısızlık durumunda olduğu gibi), ana yük devretme denetleyicisi tüm HDInsight HA Hizmetleri üzerinde başlatılır. Ana yük devretme denetleyicisi aynı zamanda diğer headnode üzerinde bu hizmetleri de durdurur.

Bir hizmetin düşük veya kötü durumda olduğu gibi HDInsight ha hizmeti hatalarında, ana yük devretme denetleyicisinin otomatik olarak yeniden başlatılması veya hizmeti, baş düğümüne durumuna göre durdurması gerekir. Kullanıcılar, her iki baş düğümde de HDInsight HA hizmetlerini el ile başlatmamalıdır. Bunun yerine, hizmetin kurtarılmasını sağlamak için otomatik veya el ile yük devretmeye izin verin.

### <a name="inadvertent-manual-intervention"></a>Yanlışlıkla el ile müdahale

HDInsight HA Hizmetleri yalnızca etkin headnode üzerinde çalışmalıdır ve gerektiğinde otomatik olarak yeniden başlatılır. Bireysel HA hizmetleri kendi sistem durumu izleyicisine sahip olmadığından, yük devretme tek tek hizmet düzeyinde tetiklenemez. Yük devretme, düğüm düzeyinde değildir ve hizmet düzeyinde değildir.

### <a name="some-known-issues"></a>Bazı bilinen sorunlar

- Bekleme konumuna el ile bir HA hizmeti başlattığınızda, bir sonraki yük devretme gerçekleşene kadar durmayacaktır. Her iki yayın düğümünde de HA hizmetleri çalışırken, bazı olası sorunlar şunlardır: ambarı Kullanıcı arabirimine erişilemez, ambarı hatalar, YARN, Spark ve Oozie işleri takılmış olabilir.

- Etkin baş düğümüne üzerinde bir ha hizmeti durdurulduğunda, bir sonraki yük devretme gerçekleşene veya ana yük devretme denetleyicisi/Master-ha-Service yeniden başlatmaları tamamlanana kadar yeniden başlatmaz. Etkin yayın düğümünde bir veya daha fazla HA hizmeti durdurulduğunda, özellikle de ambarı sunucusu durdurulduğunda, ambarı Kullanıcı arabirimine erişilemez, diğer olası sorunlar YARN, Spark ve Oozie işleri hataları içerir.

## <a name="apache-high-availability-services"></a>Apache yüksek kullanılabilirlik Hizmetleri

Apache, Ayrıca, HDInsight kümelerinde de kullanılabilen,, rBu Code, YARN ResourceManager ve HBase Master için yüksek kullanılabilirlik sağlar. HDInsight HA hizmetlerinden farklı olarak, bunlar ESP kümelerinde desteklenirler. Apache HA Hizmetleri, etkin/bekleme durumlarını seçip otomatik yük devretmeyi yürütmek için ikinci ZooKeeper çekirdekle (yukarıdaki bölümde açıklanmıştır) iletişim kurar. Aşağıdaki bölümlerde bu hizmetlerin nasıl çalıştığı ayrıntılı bilgiler verilmektedir.

### <a name="hadoop-distributed-file-system-hdfs-namenode"></a>Hadoop Dağıtılmış Dosya Sistemi (bir) süs Yot

Apache Hadoop 2,0 veya üzeri bir sürüme göre HDInsight kümeleri, süs Yot yüksek kullanılabilirlik sağlar. Otomatik yük devretme için yapılandırılmış olan, headnodes üzerinde çalışan iki süs odes vardır. Süs 'ler, Zookeeper ile iletişim kurmak için *Zkfailovercontroller* kullanarak etkin/bekleme durumunu seçin. *Zkfailovercontroller* her iki yayın düğümünde çalışır ve yukarıdaki ana yük devretme denetleyicisiyle aynı şekilde çalışır.

İkinci Zookeeper çekirdeği ilk çekirdekden bağımsızdır, bu nedenle etkin bir süs kodu etkin headnode üzerinde çalışmayabilir. Etkin bir süs kodu ölü veya sağlıksız olduğunda, bekleyen süs kodu, seçimi kazanır ve etkin hale gelir.

### <a name="yarn-resourcemanager"></a>YARN ResourceManager

Apache Hadoop 2,4 veya üzeri bir sürüme göre HDInsight kümeleri, YARN ResourceManager yüksek kullanılabilirliğini destekler. RM1 ve RM2, sırasıyla, 1. ve 1. baş düğümüne üzerinde çalışan iki ResourceManager vardır. Ayrıca, süs Yot gibi YARN ResourceManager otomatik yük devretme için de yapılandırılır. Geçerli etkin ResourceManager kaldığında veya yanıt vermediğinde, başka bir ResourceManager otomatik olarak etkin olacak şekilde seçilir.

YARN ResourceManager, kendi katıştırılmış *ActiveStandbyElector* bir hata algılayıcısı ve öncü Elector olarak kullanır. Bu, YARN ResourceManager 'un aksine, ayrı bir ZKFC cini gerekmez. Etkin ResourceManager, durumlarını Apache Zookeeper 'a yazar.

YARN ResourceManager 'nun yüksek kullanılabilirliği, süs Yot ve diğer HDInsight HA hizmetlerinden bağımsızdır. Etkin ResourceManager, etkin bir başlık düğümünde veya etkin bir süs ODE 'ın çalıştığı headdüğümünde çalışmayabilir. YARN ResourceManager yüksek kullanılabilirliği hakkında daha fazla bilgi için bkz. [ResourceManager yüksek kullanılabilirlik](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html).

### <a name="hbase-master"></a>HBase Master

HDInsight HBase kümeleri, yüksek kullanılabilirlik HBase Master destekler. Headnodes üzerinde çalışan diğer HA hizmetlerinden farklı olarak, HBase yöneticileri üç Zookeeper düğümünde çalışır ve bunlardan biri etkin ana, diğeri ise bekleme olur. Süs Yot gibi HBase Master, öncü seçimi için Apache Zookeeper ile eşgüdümünü sağlar ve geçerli etkin yöneticisinde sorun olduğunda otomatik yük devretme yapar. Dilediğiniz zaman yalnızca bir etkin HBase Master vardır.

## <a name="next-steps"></a>Sonraki adımlar

- [HDInsight 'ta Apache Hadoop kümelerinin kullanılabilirliği ve güvenilirliği](hdinsight-high-availability-linux.md)
- [Azure HDInsight sanal ağ mimarisi](hdinsight-virtual-network-architecture.md)
