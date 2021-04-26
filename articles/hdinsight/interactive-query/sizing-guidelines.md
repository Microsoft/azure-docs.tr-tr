---
title: Azure HDInsight 'ta etkileşimli sorgu kümesi boyutlandırma Kılavuzu
description: Azure HDInsight 'ta etkileşimli sorgu boyutlandırma Kılavuzu
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: a7baa9340a1f0a99b94bfcbe535c73d0b502e2a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933055"
---
# <a name="interactive-query-cluster-sizing-guide-in-azure-hdinsight"></a>Azure HDInsight 'ta etkileşimli sorgu kümesi boyutlandırma Kılavuzu

Bu belgede, makul bir performansa ulaşmak için tipik bir iş yükü için HDInsight etkileşimli sorgu kümesi 'nin (LLAP) boyutlandırılması açıklanmaktadır. Bu belgede sunulan öneriler geneldir ve belirli iş yükleri belirli ayarlamalar gerektirebilir.

## <a name="default-vm-types-for-interactive-query"></a>Etkileşimli sorgu için varsayılan VM türleri

| Düğüm türü | Örnek | Boyut |
|---|---|---|
| Head | D13 v2 | 8 VCPU, 56-GB RAM, 400 GB SSD |
| Indan | D14 v2 | 16 VCPU, 112-GB RAM, 800 GB SSD |
| ZooKeeper | A4 v2 | 4 VCPU, 8 GB RAM, 40 GB SSD |

## <a name="recommended-configurations"></a>Önerilen yapılandırma

Önerilen yapılandırma değerleri D14 v2 türü çalışan düğümüne dayalıdır.

| Anahtar | Değer | Açıklama |
|---|---|---|
| Yarn. NodeManager. Resource. Memory-MB | 102400 (MB) | Bir düğümdeki tüm YARN kapsayıcıları için, MB olarak verilen toplam bellek. |
| Yarn. Scheduler. maksimum ayırma-MB | 102400 (MB) | RM 'de, MB cinsinden her kapsayıcı isteği için en yüksek ayırma. Bu değerden daha yüksek bellek istekleri devreye girer. |
| Yarn. Scheduler. maksimum ayırma-sanal çekirdek | 12 |Kaynak Yöneticisi her kapsayıcı isteği için en fazla CPU çekirdeği sayısı. Bu değerden daha yüksek istekler etkili olmayacaktır. |
| Yarn. Scheduler. Capacity. root. LLAP. Capacity | %90 | LLAP kuyruğu için YARN kapasite ayırması.  |
| Hive. Sunucu2. tez. Sessions. per. default. Queue | number_of_worker_nodes |Hive. Sunucu2. tez. default. Queues adlı her bir kuyruğun oturum sayısı. Bu sayı, sorgu koordinatörleri sayısına (Tez AMs) karşılık gelir. |
| Tez. har. Resource. Memory. MB | 4096 (MB) | Tez AppMaster tarafından kullanılacak bellek miktarı (MB). |
| Hive. tez. Container. size | 4096 (MB) | Belirtilen tez kapsayıcı boyutu MB olarak. |
| hive.llap.daemon.num.execılar | 12 | LLAP Daemon başına yürütme sayısı. |
| Hive. LLAP. IO. ThreadPool. size | 12 | Yürüticileri için iş parçacığı havuzu boyutu. |
| Hive. LLAP. Daemon. yarn. Container. MB | 86016 (MB) | Bireysel LLAP Daemon 'ları tarafından kullanılan MB cinsinden toplam bellek (daemon başına bellek).|
| Hive. LLAP. IO. Memory. size | 409600 (MB) | LLAP arka plan programı başına MB cinsinden önbellek boyutu belirtilen SSD önbelleği etkin. |
| Hive. Auto. Convert. JOIN. noconditionaltask. size | 2048 (MB) | Eşleme birleştirmesini yapmak için MB cinsinden bellek boyutu. |

## <a name="llap-daemon-size-estimations"></a>LLAP Daemon boyut tahminleri  

### <a name="yarnnodemanagerresourcememory-mb"></a>Yarn. NodeManager. Resource. Memory-MB

Bu değer, her düğümdeki YARN kapsayıcıları tarafından kullanılan MB cinsinden maksimum bellek toplamını gösterir. Bu düğümde YARN 'nin kullanabileceği bellek miktarını belirtir, bu nedenle bu değer bu düğümdeki toplam bellekten daha az olmalıdır.

Bu değeri ayarlayın = [düğümdeki toplam fiziksel bellek] – [OS + diğer hizmetler için bellek].

Bu değeri kullanılabilir RAM 'in yaklaşık %90 ' i olarak ayarlamanız önerilir. D14 v2 için önerilen değer **102400 MB**'dir.

### <a name="yarnschedulermaximum-allocation-mb"></a>Yarn. Scheduler. maksimum ayırma-MB

Bu değer, Kaynak Yöneticisi her kapsayıcı isteği için MB cinsinden maksimum ayırmayı gösterir. Belirtilen değerden daha yüksek bellek istekleri etkili olmayacak. Kaynak Yöneticisi yalnızca, kapsayıcılardan yalnızca artışlarla bellek verebilir `yarn.scheduler.minimum-allocation-mb` ve tarafından belirtilen boyutu aşamaz `yarn.scheduler.maximum-allocation-mb` . Bu değer, düğümün verilen toplam bellekten daha fazla olmamalıdır ve tarafından belirtilir `yarn.nodemanager.resource.memory-mb` .

D14 v2 çalışan düğümleri için önerilen değer **102400 MB** 'dir

### <a name="yarnschedulermaximum-allocation-vcores"></a>Yarn. Scheduler. maksimum ayırma-sanal çekirdek

Bu yapılandırma Kaynak Yöneticisi her kapsayıcı isteği için en fazla sanal CPU çekirdeği sayısını belirtir. Bu yapılandırmadan daha yüksek bir değer istemek etkili olmayacaktır. Bu yapılandırma, YARN Scheduler 'ın genel bir özelliğidir. LLAP Daemon kapsayıcısı için, bu değer toplam kullanılabilir sanal çekirdekten (VÇEKIRDEKLER) %75 ' e ayarlanabilir. Kalan %25 ' i NodeManager, Davtanode ve çalışan düğümlerinde çalışan diğer hizmetler için ayrılmalıdır.  

D14 v2 çalışan düğümleri için 16 sanal çekirdek vardır ve 16 sanal çekirdekten %75 verilebilir. Bu nedenle, LLAP Daemon kapsayıcısı için önerilen değer **12**' dir.

### <a name="hiveserver2tezsessionsperdefaultqueue"></a>Hive. Sunucu2. tez. Sessions. per. default. Queue

Bu yapılandırma değeri, tarafından belirtilen sıraların her biri için paralel olarak başlatılması gereken tez oturumlarının sayısını belirler `hive.server2.tez.default.queues` . Değer, tez AMs (sorgu koordinatörleri) sayısına karşılık gelir. Düğüm başına bir tez olması için çalışan düğüm sayısıyla aynı olması önerilir. Tez AMs sayısı LLAP Daemon düğümlerinin sayısından daha yüksek olabilir. Birincil sorumluluğu, sorgu yürütmeyi koordine etmek ve sorgu planı parçalarını yürütme için karşılık gelen LLAP Daemon 'ları atamaya yöneliktir. Daha yüksek performans elde etmek için bir dizi LLAP Daemon düğümünün birden çok katı olarak tutulması önerilir.  

Varsayılan HDInsight kümesinde dört çalışan düğümünde çalışan dört LLAP Daemon 'ları bulunur, bu nedenle önerilen değer **4**' tür.  

### <a name="tezamresourcememorymb-hivetezcontainersize"></a>Tez. har. Resource. Memory. MB, Hive. tez. Container. size

`tez.am.resource.memory.mb` Tez uygulama ana boyutunu tanımlar.  
Önerilen değer **4096 MB**'dir.

`hive.tez.container.size` Tez kapsayıcısı için verilen bellek miktarını tanımlar. Bu değer, YARN en az kapsayıcı boyutu ( `yarn.scheduler.minimum-allocation-mb` ) Ile Yarn maksimum kapsayıcı boyutu () arasında ayarlanmalıdır `yarn.scheduler.maximum-allocation-mb` .  
**4096 MB** olarak ayarlanması önerilir.  

Genel bir kural, her kapsayıcı için bir işlemciyi göz önünde bulundurarak, işlemci başına bellek miktarından daha az tutulacağız. `Rreserve` LLAP Daemon için bellek vermeden önce bir düğümdeki tez AMs sayısı için bellek. Örneğin, düğüm başına iki adet tez AMs (her biri 4 GB) kullanıyorsanız, iki tez AMs için, LLAP arka plan programı 8 GB için 82 GB 'ye 90 GB verin.

### <a name="yarnschedulercapacityrootllapcapacity"></a>Yarn. Scheduler. Capacity. root. LLAP. Capacity

Bu değer, LLAP kuyruğu için verilen kapasitenin yüzdesini gösterir. Hdınsights etkileşimli sorgu kümesi, LLAP kuyruğu için toplam kapasitenin %90 ' ını sağlar ve kalan %10 ' un diğer kapsayıcı ayırmaları için varsayılan sıraya ayarlanır.  
D14v2 Worker düğümleri için, LLAP kuyruğu için önerilen değer **90** ' dir.

### <a name="hivellapdaemonyarncontainermb"></a>Hive. LLAP. Daemon. yarn. Container. MB

LLAP Daemon için toplam bellek boyutu aşağıdaki bileşenlere bağlıdır:

* YARN kapsayıcısı boyutunun yapılandırması ( `yarn.scheduler.maximum-allocation-mb` , `yarn.scheduler.maximum-allocation-mb` , `yarn.nodemanager.resource.memory-mb` )

* Yürüticileri (XMX) tarafından kullanılan yığın belleği

    Yayın odası boyutu alındıktan sonra kullanılabilir RAM miktarı.  
    D14 v2 için HDI 4,0-Bu değer (86 GB-6 GB) = 80 GB  
    D14 v2 için HDI 3,6-Bu değer (84 GB-6 GB) = 78 GB

* Daemon başına bellek içi belleğe alma (Hive. LLAP. IO. Memory. size)

* Boşluk payı

    Java VM yükü (meta alan, iş parçacıkları yığını, GC veri yapıları vb.) için kullanılan yığın dışı belleğin bir parçasıdır. Bu bölüm, yığın boyutunun (XMX) %6 ' ından yaklaşık olarak gözlemlenmiştir. Daha güvenli bir tarafta olması için toplam LLAP Daemon bellek boyutunun %6 ' ı kadar hesaplanabilir. SSD önbelleği etkinleştirildiğinde mümkün olduğundan, LLAP Daemon 'ın yalnızca yığın için kullanılmak üzere tüm kullanılabilir bellek içi alanı kullanmasına izin verilir.  
    D14 v2 için önerilen değer CEIL 'dir (86 GB x 0,06) ~ = **6 GB**.  

Daemon başına bellek = [bellek Içi önbellek boyutu] + [yığın boyutu] + [Headodi].

Bu, aşağıdaki gibi hesaplanabilir:

Tez düğüm başına bellek = [(Tez AMs/LLAP Daemon düğümü sayısı) * Tez PM boyutu].
LLAP Daemon kapsayıcı boyutu = [%90/YARN en fazla kapsayıcı belleği] – [düğüm başına tez belleği].

D14 v2 çalışan düğümü için, HDI 4,0-önerilen değer (90-(1/1 * 4 GB)) = **86 GB**.
(HDI 3,6 için önerilen değer **84 GB** 'tır, çünkü kaydırıcı IÇIN 2 GB ayırmanız gerekir.)  

### <a name="hivellapiomemorysize"></a>Hive. LLAP. IO. Memory. size

Bu yapılandırma, LLAP Daemon için önbellek olarak kullanılabilir bellek miktarıdır. LLAP Daemon 'ları, SSD 'yi bir önbellek olarak kullanabilir. Setting `hive.llap.io.allocator.mmap` = true, SSD önbelleğe almayı etkinleştirir. D14 v2, ~ 800 GB SSD ile birlikte gelir ve SSD önbelleğe alma, etkileşimli sorgu kümesi (LLAP) için varsayılan olarak etkindir. Yığın dışı önbellek için SSD alanının %50 ' ü kullanacak şekilde yapılandırılmıştır.

D14 v2 için önerilen değer **409600 MB**'dir.  

Diğer VM 'Lerde, SSD önbelleği etkin olmadan, daha iyi performans elde etmek için LLAP önbelleğe alma için kullanılabilir RAM 'in bir bölümünü vermeniz yararlı olur. LLAP Daemon için toplam bellek boyutunu şu şekilde ayarlayın:  

Toplam LLAP Daemon belleği = [LLAP önbellek boyutu] + [yığın boyutu] + [Headodi].

İş yükünüz için en uygun önbellek boyutunu ve yığın boyutunu ayarlamanız önerilir.  

### <a name="hivellapdaemonnumexecutors"></a>hive.llap.daemon.num.execılar

Bu yapılandırma, görevleri her LLAP arka plan programı başına paralel olarak yürütebilmesi gereken yürütme sayısını denetler. Bu değer, kullanılabilir sanal çekirdek sayısı, yürütücü başına verilen bellek miktarı ve LLAP Daemon başına kullanılabilir toplam bellek dengesidir. Genellikle, bu değerin çekirdek sayısına olabildiğince yakın olmasını istiyoruz.

D14 v2 için kullanılabilir olan 16 sanal çekirdek mevcuttur, ancak tüm sanal çekirdekler verilemez. Çalışan düğümleri NodeManager, DataNode ve ölçüm Izleyicisi gibi kullanılabilir sanal çekirdeklerin bir bölümünü gerektiren diğer hizmetleri de çalıştırır. Bu değer, bu düğümde bulunan toplam sanal çekirdekler için %75 ' e kadar yapılandırılabilir.

D14 v2 için önerilen değer (. 75 X 16) = **12**

Yürütücü başına yaklaşık 6 GB yığın alanı ayırmanız önerilir. Kullanılabilir LLAP Daemon boyutunu ve düğüm başına kullanılabilir sanal çekirdek sayısını temel alarak yürüticilerinin sayısını ayarlayın.  

### <a name="hivellapiothreadpoolsize"></a>Hive. LLAP. IO. ThreadPool. size

Bu değer, yürüticileri için iş parçacığı havuzu boyutunu belirtir. Yürüticileri belirtilen şekilde sabitlendiği için, LLAP Daemon başına yürüticilerinin sayısıyla aynı olacaktır.

D14 v2 için, bu değerin **12** olarak ayarlanması önerilir.

Bu yapılandırma değeri aşamaz `yarn.nodemanager.resource.cpu-vcores` .

### <a name="hiveautoconvertjoinnoconditionaltasksize"></a>Hive. Auto. Convert. JOIN. noconditionaltask. size

Bu parametrenin etkin olması için etkinleştirdiğinizden emin olun `hive.auto.convert.join.noconditionaltask` . Bu yapılandırma, kullanıcının harita birleştirmesini yapmak için belleğe sığamayacak olan tabloların boyutunu belirtmesini sağlar. N-Way JOIN için n-1 değerinin toplam `tables/partitions` değeri yapılandırılan değerden azsa, eşleme birleşimi seçilir. Map JOIN 'e otomatik dönüştürme eşiğini hesaplamak için LLAP yürütücü bellek boyutu kullanılmalıdır.

D14 v2 için, bu değerin **2048 MB** olarak ayarlanması önerilir.

İş yükünüz için uygun olan bu değerin, bu değeri çok düşük olarak ayarlamak, oto Dönüştür özelliği kullanmayabilir. Bunun çok yüksek olması, GC duraklamaları oluşmasına neden olabilir ve bu da sorgu performansını olumsuz yönde etkileyebilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Ağ Geçidi yönergeleri](gateway-best-practices.md)
* [Önce açığa çıkar Apache Tez belleği ayarlama-adım adım](https://community.cloudera.com/t5/Community-Articles/Demystify-Apache-Tez-Memory-Tuning-Step-by-Step/ta-p/245279)
* [LLAP Için eşleme birleştirmesi bellek boyutlandırması](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)
* [LLAP-tek sayfalı mimariye genel bakış](https://community.cloudera.com/t5/Community-Articles/LLAP-a-one-page-architecture-overview/ta-p/247439)
* [Hive LLAP derin bakış](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)
