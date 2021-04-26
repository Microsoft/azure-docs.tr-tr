---
title: HDInsight etkileşimli sorgu kümesi (LLAP) boyutlandırma Kılavuzu
description: LLAP boyutlandırma Kılavuzu
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: ca3ba61de13e0e451b43dc9c8ea40db33fed859a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869676"
---
# <a name="azure-hdinsight-interactive-query-cluster-hive-llap-sizing-guide"></a>Azure HDInsight etkileşimli sorgu kümesi (Hive LLAP) boyutlandırma Kılavuzu

Bu belge, makul bir performansa ulaşmak için tipik bir iş yükü için HDInsight etkileşimli sorgu kümesi 'nin (Hive LLAP kümesi) boyutlandırılacağını açıklar. Bu belgede sunulan önerilerin genel yönergeler olduğunu ve belirli iş yüklerinin belirli bir ayarlamaya ihtiyaç duymadığını unutmayın.

### <a name="azure-default-vm-types-for-hdinsight-interactive-query-clusterllap"></a>**HDInsight etkileşimli sorgu kümesi (LLAP) için Azure varsayılan VM türleri**

| Düğüm türü      | Örnek | Boyut     |
| :---        |    :----:   | :---     |
| Head      | D13 v2       | 8 vCPU, 56 GB RAM, 400 GB SSD   |
| Indan   | **D14 v2**        | **16 vCPU, 112 GB RAM, 800 GB SSD**       |
| ZooKeeper   | A4 v2        | 4 vCPU, 8 GB RAM, 40 GB SSD       |

***Note: tüm önerilen yapılandırma değerleri D14 v2 türü çalışan düğümüne dayalıdır***  

### <a name="configuration"></a>**Yapılandırmada**    
| Yapılandırma Anahtarı      | Önerilen değer  | Description |
| :---        |    :----:   | :---     |
| Yarn. NodeManager. Resource. Memory-MB | 102400 (MB) | Bir düğümdeki tüm YARN kapsayıcıları için, MB olarak verilen toplam bellek | 
| Yarn. Scheduler. maksimum ayırma-MB | 102400 (MB) | RM 'de, MB cinsinden her kapsayıcı isteği için en yüksek ayırma. Bu değerden daha yüksek bellek istekleri uygulanmayacak |
| Yarn. Scheduler. maksimum ayırma-sanal çekirdek | 12 |Kaynak Yöneticisi her kapsayıcı isteği için en fazla CPU çekirdeği sayısı. Bu değerden daha yüksek istekler etkili olmayacaktır. |
| Yarn. NodeManager. Resource. CPU-vçekirdekler | 12 | Kapsayıcılar için ayrılabilecek, NodeManager başına düşen CPU çekirdeği sayısı. |
| Yarn. Scheduler. Capacity. root. LLAP. Capacity | 85 (%) | LLAP kuyruğu için YARN kapasite ayırma  |
| Tez. har. Resource. Memory. MB | 4096 (MB) | Tez AppMaster tarafından kullanılacak bellek miktarı (MB) |
| Hive. Sunucu2. tez. Sessions. per. default. Queue | <number_of_worker_nodes> |Hive. Sunucu2. tez. default. Queues adlı her bir kuyruğun oturum sayısı. Bu sayı, sorgu koordinatörleri sayısına karşılık gelir (Tez AMs) |
| Hive. tez. Container. size | 4096 (MB) | Belirtilen tez kapsayıcı boyutu MB olarak |
| hive.llap.daemon.num.execılar | 19 | LLAP Daemon başına yürütme sayısı | 
| Hive. LLAP. IO. ThreadPool. size | 19 | Yürüticileri için iş parçacığı havuzu boyutu |
| Hive. LLAP. Daemon. yarn. Container. MB | 81920 (MB) | Bireysel LLAP Daemon 'ları tarafından kullanılan MB cinsinden toplam bellek (daemon başına bellek)
| Hive. LLAP. IO. Memory. size | 242688 (MB) | LLAP arka plan programı başına MB cinsinden önbellek boyutu belirtilen SSD önbelleği etkin |
| Hive. Auto. Convert. JOIN. noconditionaltask. size | 2048 (MB) | Eşleme katılması için MB cinsinden bellek boyutu |

### <a name="llap-architecturecomponents"></a>**LLAP mimarisi/bileşenleri:**  

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_architecture_sizing_guide.png " alt-text="' LLAP mimarisi/bileşenleri '" border="true":::

### <a name="llap-daemon-size-estimations"></a>**LLAP Daemon boyut tahminleri:** 

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1. bir düğümdeki tüm kapsayıcılar için toplam YARN bellek ayırmayı belirleme**    
Yapılandırma: ***yarn. NodeManager. Resource. Memory-MB***  

Bu değer, her düğümdeki YARN kapsayıcıları tarafından kullanılabilen, MB cinsinden maksimum bellek toplamını gösterir. Belirtilen değer, bu düğümdeki toplam fiziksel bellek miktarından daha az olmalıdır.   
Düğüm üzerindeki tüm YARN kapsayıcıları için toplam bellek = (Toplam fiziksel bellek – işletim sistemi + diğer hizmetler için bellek)  
Bu değeri kullanılabilir RAM boyutunun yaklaşık %90 ' i olarak ayarlayın.  
D14 v2 için önerilen değer **102400 MB**'dir. 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2. YARN kapsayıcı isteği başına en fazla bellek miktarını belirleme**  
Yapılandırma: ***yarn. Scheduler. maksimum ayırma-MB***

Bu değer, Kaynak Yöneticisi her kapsayıcı isteği için MB cinsinden maksimum ayırmayı gösterir. Belirtilen değerden daha yüksek bellek istekleri etkili olmayacak. Kaynak Yöneticisi, *yarn. Scheduler. minimum-Allocation-MB* artışlarına göre bellek sağlayabilir ve *yarn. Scheduler. maksimum ayırma-MB* tarafından belirtilen boyutu aşamaz. Belirtilen değer, *yarn. NodeManager. Resource. Memory-MB* tarafından belirtilen düğümdeki tüm kapsayıcılar için verilen toplam bellekten daha fazla olmamalıdır.    
D14 v2 çalışan düğümleri için önerilen değer **102400 MB** 'dir

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3. YARN kapsayıcı isteği başına maksimum sanal çekirdek miktarı belirleniyor**  
Yapılandırma: ***yarn. Scheduler. maksimum ayırma-sanal çekirdek***  

Bu değer, Kaynak Yöneticisi her kapsayıcı isteği için en fazla sanal CPU çekirdeği sayısını belirtir. Bu değerden daha yüksek sayıda sanal çekirdek istemek etkili olmayacaktır. YARN Scheduler 'ın genel bir özelliğidir. LLAP Daemon kapsayıcısı için, bu değer toplam kullanılabilir sanal çekirdekten %75 ' e ayarlanabilir. Kalan %25 ' i NodeManager, Davtanode ve çalışan düğümlerinde çalışan diğer hizmetler için ayrılmalıdır.  
D14 v2 VM 'lerinde 16 sanal çekirdek vardır ve toplam 16 sanal çekirdek %75, LLAP Daemon kapsayıcısı tarafından kullanılabilir.  
D14 v2 için önerilen değer **12**' dir.  

#### <a name="4-number-of-concurrent-queries"></a>**4. eşzamanlı sorguların sayısı**  
Yapılandırma: ***Hive. Sunucu2. tez. Sessions. per. default. Queue***

Bu yapılandırma değeri, paralel olarak başlatılabilen tez oturumlarının sayısını belirler. Bu tez oturumları, "Hive. Sunucu2. tez. default. Queues" tarafından belirtilen sıraların her biri için başlatılır. Tez AMs (sorgu koordinatörleri) sayısına karşılık gelir. Çalışan düğümlerinin sayısıyla aynı olması önerilir. Tez AMs sayısı LLAP Daemon düğümlerinin sayısından daha yüksek olabilir. Tez 'nin birincil sorumluluğu, sorgu yürütmeyi koordine etmek ve sorgu planı parçalarını yürütme için karşılık gelen LLAP Daemon 'ları atamaya yöneliktir. Daha yüksek performans elde etmek için bu değeri bir dizi LLAP Daemon düğümünün katı olarak tutun.  

Varsayılan HDInsight kümesinde dört çalışan düğümünde çalışan dört LLAP Daemon 'ları bulunur, bu nedenle önerilen değer **4**' tür.  

**Hive yapılandırma değişkeni için ambarı UI kaydırıcısı `hive.server2.tez.sessions.per.default.queue` :**

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_sizing_guide_max_concurrent_queries.png " alt-text="' LLAP maksimum eşzamanlı sorgu '" border="true":::

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5. tez kapsayıcısı ve tez uygulama ana boyutu**    
Yapılandırma: ***tez. har. Resource. Memory. MB, Hive. tez. Container. size***  

*tez. har. Resource. Memory. MB* -tez uygulama ana boyutunu tanımlar.  
Önerilen değer **4096 MB**'dir.
   
*Hive. tez. Container. size* -tez kapsayıcısı için verilen bellek miktarını tanımlar. Bu değer, YARN en az kapsayıcı boyutu (*yarn. Scheduler. minimum ayırma-MB*) ve Yarn maksimum kapsayıcı boyutu (*yarn. Scheduler. maksimum ayırma-MB*) arasında ayarlanmalıdır. LLAP Daemon yürüticileri, yürütücü başına bellek kullanımını sınırlamak için bu değeri kullanır.  
Önerilen değer **4096 MB**'dir.  

#### <a name="6-llap-queue-capacity-allocation"></a>**6. LLAP kuyruk kapasitesi ayırma**   
Yapılandırma: ***yarn. Scheduler. Capacity. root. LLAP. Capacity***  

Bu değer, LLAP kuyruğuna verilen kapasitenin yüzdesini gösterir. YARN sıralarının nasıl yapılandırıldığına bağlı olarak, kapasite ayırmaları farklı iş yükleri için farklı değerlere sahip olabilir. İş yükünüz salt okunurdur, daha sonra kapasitenin %90 ' i kadar yüksek olarak ayarlanması gerekir. Ancak, iş yükünüz yönetilen tabloları kullanarak güncelleştirme/silme/birleştirme işlemlerinin karışımı ise, LLAP kuyruğu için kapasitenin %85 ' i sağlamanız önerilir. Kalan %15 kapasite, varsayılan sıradan kapsayıcı ayırmak için sıkıştırma vb. gibi diğer görevler tarafından kullanılabilir. Bu şekilde, varsayılan kuyruktaki görevler YARN kaynaklarının içeriğini kaldırır.    

D14v2 Worker düğümleri için, LLAP kuyruğu için önerilen değer **85**' dir.     
(Salt okunur iş yükleri için, uygun şekilde 90 'e varan bir artış olabilir.)  

#### <a name="7-llap-daemon-container-size"></a>**7. LLAP Daemon kapsayıcı boyutu**    
Yapılandırma: ***Hive. LLAP. Daemon. yarn. Container. MB***  
   
LLAP Daemon, her çalışan düğümünde bir YARN kapsayıcısı olarak çalıştırılır. LLAP Daemon kapsayıcısı için toplam bellek boyutu aşağıdaki faktörlere bağlıdır,    
*  YARN kapsayıcı boyutu (yarn. Scheduler. minimum ayırma-MB, yarn. Scheduler. maksimum ayırma-MB, yarn. NodeManager. Resource. Memory-MB) konfigürasyonları
*  Bir düğümdeki tez AMs sayısı
*  Bir düğümdeki tüm kapsayıcılar ve LLAP kuyruğu kapasitesi için yapılandırılan toplam bellek  

Tez uygulama yöneticileri (Tez) için gereken bellek aşağıdaki gibi hesaplanabilir.  
Tez, bir sorgu Düzenleyicisi görevi görür ve sunulacak bir dizi eşzamanlı sorguya göre tez AMs sayısı yapılandırılmalıdır. Teorik olarak, çalışan düğümü başına bir tez har göz abiliriz. Bununla birlikte, bir çalışan düğümünde birden fazla tez ile karşılaşabilirsiniz. Hesaplama amacıyla, tüm LLAP Daemon düğümleri/çalışan düğümleri genelinde tez AMs 'nin tek başına dağıtımını varsaytık.
Her tez için 4 GB bellek olması önerilir.  

Hive config ***Hive. Sunucu2. tez. Sessions. per. default. Queue*** tarafından belirtilen tez AMS = değer sayısı.  
Ambarı Kullanıcı arabirimindeki ***num_llap_nodes_for_llap_daemons*** LLAP Daemon düğümlerinin sayısı = env değişkeni tarafından belirtilir.  
Tez NZ kapsayıcı boyutu = tez config ***tez.*** m.  

Düğüm başına tez belleği = **(** CEIL **(** **/** LLAP Daemon düğümü sayısı **)** **x** tez, kapsayıcı boyutu **)**  
D14 v2 için varsayılan yapılandırmanın dört adet tez AMs ve dört LLAP Daemon düğümü vardır.  
Düğüm başına tez belleği = (CEIL (4/4) x 4 GB) = 4 GB

Çalışan düğümü başına LLAP kuyruğu için kullanılabilen toplam bellek aşağıdaki gibi hesaplanabilir:  
Bu değer, bir düğümdeki tüm YARN kapsayıcıları için kullanılabilen toplam bellek miktarına (*yarn. NodeManager. Resource. Memory-MB*) ve LLAP kuyruğu için yapılandırılan kapasite yüzdesine (*yarn. Scheduler. Capacity. root. LLAP. Capacity*) bağlıdır.  
Çalışan düğümünde LLAP kuyruğu için toplam bellek = düğüm x, LLAP kuyruğu için Kapasite yüzdesi olan tüm YARN kapsayıcıları için kullanılabilir toplam bellek.  
D14 v2 için bu değer (100 GB x 0,85) = 85 GB 'dir.

LLAP Daemon kapsayıcı boyutu aşağıdaki gibi hesaplanır;

**LLAP Daemon kapsayıcı boyutu = (bir workernode üzerinde LLAP kuyruğu için toplam bellek) – (düğüm başına tez belleği)-(hizmet ana kapsayıcı boyutu)**  
Çalışan düğümlerinden birinde oluşturulan kümede yalnızca bir hizmet Yöneticisi (LLAP hizmeti için uygulama Yöneticisi) vardır. Hesaplama amacıyla, çalışan düğümü başına bir hizmet yöneticisini dikkate aldık.  
D14 v2 çalışan düğümü için, HDI 4,0-önerilen değer (85 GB-4 GB-1 GB)) = **80 GB**   
(HDI 3,6 için önerilen değer **79 GB** 'tır çünkü kaydırıcı için ek ~ 2 GB) ayırmanız gerekir.)  

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8. LLAP Daemon başına yürütme sayısını belirleme**  
Yapılandırma: ***hive.llap.daemon.num.executors** _, _ *_Hive. LLAP. IO. ThreadPool. size_**

***hive.llap.daemon.num.execucılar***:   
Bu yapılandırma, görevleri her LLAP arka plan programı başına paralel olarak yürütebilmesi gereken yürütme sayısını denetler. Bu değer, sanal çekirdek sayısına, yürütücü başına kullanılan bellek miktarına ve LLAP Daemon kapsayıcısı için kullanılabilir toplam bellek miktarına bağlıdır.    Yürüticilerinin sayısı, çalışan düğümü başına kullanılabilir sanal çekirdekler %120 ' e abone olabilir. Ancak, her yürütücü için gereken belleğe ve LLAP Daemon kapsayıcı boyutuna bağlı olarak bellek gereksinimlerini karşılamıyorsa, bu, ayarlanmalıdır.

Her yürütücü bir tez kapsayıcısına eşdeğerdir ve belleğin 4GB'A (Tez kapsayıcı boyutu) tüketebilir. LLAP Daemon 'daki tüm yürüticileri aynı yığın belleğini paylaşır. Tüm yürüticilere aynı anda bellek yoğun işlemler çalıştırılmadığından varsayımıyla, her yürütücü için tez kapsayıcı boyutunun (4 GB) %75 ' ını göz önüne alabilirsiniz. Bu şekilde, daha fazla paralellik için her bir yürütücü daha az bellek (örneğin 3 GB) vererek yürüticilerinin sayısını artırabilirsiniz. Ancak, bu ayarı hedef iş yükünüz için ayarlamanız önerilir.

D14 v2 VM 'lerinde 16 sanal çekirdek vardır.
D14 v2 için, yürütme sayısı için önerilen değer (16 sanal çekirdek x 120%) ~ = **19** ' u her çalışan düğümünde, yürütücü başına 3GB bir şekilde ele alır.

***Hive. LLAP. IO. ThreadPool. size***:   
Bu değer, yürüticileri için iş parçacığı havuzu boyutunu belirtir. Yürüticileri belirtilen şekilde sabitlendiği için, LLAP Daemon başına yürüticilerinin sayısıyla aynı olacaktır.    
D14 v2 için önerilen değer **19**' dir.

#### <a name="9-determining-llap-daemon-cache-size"></a>**9. LLAP Daemon önbellek boyutunu belirleme**  
Yapılandırma: ***Hive. LLAP. IO. Memory. size***

LLAP Daemon kapsayıcı belleği aşağıdaki bileşenlerden oluşur;
*  Baş oda
*  Yürüticileri (XMX) tarafından kullanılan yığın belleği
*  Daemon önbelleği etkinken, Daemon başına bellek içi önbellek (yığın dışı bellek boyutu, kullanılamaz)
*  Bellek içi önbellek meta veri boyutu (yalnızca SSD önbelleği etkinleştirildiğinde geçerlidir)

**Yer alan boyutu**: Bu boyut, Java VM yükü (meta alan, iş parçacıkları yığını, GC veri yapıları vb.) için kullanılan yığın dışı belleğin bir kısmını gösterir. Genellikle bu ek yük, yığın boyutunun (XMX) yaklaşık %6 ' dır. Bu değer, daha güvenli bir tarafta olması için toplam LLAP Daemon bellek boyutunun %6 ' ı kadar hesaplanabilir.  
D14 v2 için önerilen değer CEIL 'dir (80 GB x 0,06) ~ = **4 GB**.  

**Yığın boyutu (XMX)**: tüm yürütcılar için kullanılabilir yığın belleği miktarıdır.
Toplam yığın boyutu = yürüticilerinin sayısı x 3 GB  
D14 v2 için bu değer 19 x 3 GB = **57 GB** 'tır  

`Ambari environment variable for LLAP heap size:`

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_sizing_guide_llap_heap_size.png " alt-text="' LLAP yığın boyutu '" border="true":::

SSD önbelleği devre dışı bırakıldığında, bellek içi önbellek, LLAP Daemon kapsayıcı boyutundan gelen yayın odası boyutu ve yığın boyutu alındıktan sonra bırakılan bellek miktarıdır.

SSD önbelleği etkinleştirildiğinde önbellek boyutu hesaplaması farklılık gösterir.
*Hive. LLAP. IO. ayırıcı. MMAP* = true ayarı SSD önbelleğe almayı etkinleştirir.
SSD önbelleği etkinleştirildiğinde, SSD önbelleğinin meta verilerini depolamak için belleğin bazı kısımları kullanılacaktır. Meta veriler bellekte depolanıyor ve SSD önbellek boyutunun yaklaşık %8 ' i olması beklenmektedir.   
SSD önbelleği bellek içi meta veri boyutu = LLAP Daemon kapsayıcı boyutu-(baş oda + yığın boyutu)  
D14 v2 için, HDI 4,0, SSD önbelleği bellek içi meta veri boyutu = 80 GB-(4 GB + 57 GB) = **19 GB**  
D14 v2 için, HDI 3,6, SSD önbelleği bellek içi meta veri boyutu = 79 GB-(4 GB + 57 GB) = **18 GB**

SSD Önbelleği meta verilerini depolamak için kullanılabilir belleğin boyutu verildiğinde, desteklenebilir SSD önbelleğinin boyutunu hesaplayabiliriz.  
SSD önbelleği için bellek içi meta verilerin boyutu = LLAP Daemon kapsayıcı boyutu-(baş oda + yığın boyutu) = 19 GB     
SSD Önbelleği boyutu = SSD önbelleği için bellek içi meta verilerin boyutu (19 GB)/0,08 (yüzde 8)  

D14 v2 ve HDI 4,0 için önerilen SSD önbellek boyutu = 19 GB/0,08 ~ = **237 GB**  
D14 v2 ve HDI 3,6 için önerilen SSD önbellek boyutu = 18 GB/0,08 ~ = **225 GB**

#### <a name="10-adjusting-map-join-memory"></a>**10. eşleme bellek birleştirmesini ayarlama**   
Yapılandırma: ***Hive. Auto. Convert. JOIN. noconditionaltask. size***

Bu parametre için *Hive. Auto. Convert. JOIN. noconditionaltask* özelliğinin etkin olduğundan emin olun.
Bu yapılandırma, diğer yürütmelerin daha fazla eşleme birleştirmesini sağlamak üzere bellek içi karma tablolar için daha fazla yere sahip olması için diğer yürütmelere göre daha fazla yer açmak üzere Hive iyileştiricilerine göre Mapjoın 'in eşiğini belirlenir. Yürütücü başına 3GB göz önünde bulundurarak, bu boyut 3GB 'e abone olabilir, ancak bazı yığın belleği sıralama arabellekleri, karışık arabellekler vb. diğer işlemler için de kullanılabilir.   
Bu nedenle, D14 v2 için, yürütücü başına 3 GB bellek ile bu değerin **2048 MB** olarak ayarlanması önerilir.  

(Unutmayın: Bu değer, iş yükünüz için uygun olan ayarlamalara gereksinim duyuyor olabilir. Bu değeri çok düşük olarak ayarlamak, oto Dönüştür özelliği kullanamaz. Ve bunu çok yüksek olarak ayarlamak, olumsuz performansla sonuçlanamayacak bellek özel durumları veya GC duraklamaları oluşmasına neden olabilir.)  

#### <a name="11-number-of-llap-daemons"></a>**11. LLAP Daemon 'ları sayısı**
Ambarı ortam değişkenleri: ***num_llap_nodes, num_llap_nodes_for_llap_daemons***  

**num_llap_nodes** -HIVE LLAP hizmeti tarafından kullanılan düğüm sayısını belirtir. Bu, LLAP Daemon, LLAP hizmet yöneticisi ve tez uygulama ana (Tez) çalıştıran düğümleri içerir.  

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes.png " alt-text="' LLAP hizmeti için düğüm sayısı '" border="true":::  

**num_llap_nodes_for_llap_daemons** -yalnızca LLAP Daemon 'ları için kullanılan düğüm sayısı. LLAP Daemon kapsayıcı boyutları maks. düğüme ayarlanır, bu nedenle her düğümde bir LLAP daemon ile sonuçlanır.

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes_for_llap_daemons.png " alt-text="' LLAP Daemon 'ları için düğüm sayısı '" border="true":::

Her iki değerin de etkileşimli sorgu kümesindeki çalışan düğümlerinin sayısıyla aynı tutulması önerilir.

### <a name="considerations-for-workload-management"></a>**Iş yükü yönetimine ilişkin konular**  
LLAP için iş yükü yönetimini etkinleştirmek istiyorsanız, iş yükü yönetiminin beklendiği gibi çalışması için yeterli kapasiteyi ayırdığınızdan emin olun. İş yükü yönetimi, sıranın yanı sıra özel bir YARN sırasının yapılandırılmasını gerektirir `llap` . İş yükü gereksinimlerinize uygun olarak LLAP kuyruğu ve iş yükü yönetim kuyruğu arasındaki toplam küme kaynak kapasitesini böltığınızdan emin olun.
Bir kaynak planı etkinleştirildiğinde iş yükü yönetimi (Tez AMs).
Lütfen unutmayın:

* Bir kaynak planı etkinleştirilerek oluşturulan tez 'Ler, tarafından belirtilen iş yükü yönetimi kuyruğundan kaynakları tüketir `hive.server2.tez.interactive.queue` .  
* Tez AMs sayısı, `QUERY_PARALLELISM` kaynak planında belirtilen değere bağlıdır.  
* İş yükü yönetimi etkin olduktan sonra, LLAP kuyruğu 'ndaki tez AMs kullanılmaz. Sorgu düzenlemesi için yalnızca iş yükü yönetim kuyruğundan tez AMs kullanılır. Kuyruktaki tez 'ler `llap` iş yükü yönetimi devre dışı bırakıldığında kullanılır.
 
Örneğin: toplam küme kapasitesi = 100 GB bellek, LLAP, Iş yükü yönetimi ve varsayılan kuyruklar arasına aşağıda gösterildiği gibi bölünür:
 - LLAP sıra kapasitesi = 70 GB
 - İş yükü yönetimi sıra kapasitesi = 20 GB
 - Varsayılan sıra kapasitesi = 10 GB

İş yükü yönetimi sıra kapasitesinde 20 GB ile bir kaynak planı `QUERY_PARALLELISM` beş olarak değer belirtebilir, bu da iş yükü yönetiminin her biri 4 GB kapsayıcı boyutuyla beş tez AMs başlatabileceği anlamına gelir. `QUERY_PARALLELISM`Kapasiteden yüksekse, bazı tez AMs durma yanıt verme `ACCEPTED` durumunu görebilirsiniz. Hiveserver2 Interactive, durum içinde olmayan tez AMs 'ye sorgu parçaları gönderemiyor `RUNNING` .


#### <a name="next-steps"></a>**Sonraki adımlar**
Bu değerleri ayarlamak sorununuzu gidermezse, aşağıdakilerden birini ziyaret edin...

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport)Azure Community 'yi doğru kaynaklara bağlayarak müşteri deneyimini iyileştirmeye yönelik resmi Microsoft Azure hesabı ile bağlanın: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için lütfen [Azure destek isteği oluşturma](../../azure-portal/supportability/how-to-create-azure-support-request.md)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.  

* ##### <a name="other-references"></a>**Diğer başvurular:**
  * [Diğer LLAP özelliklerini yapılandırma](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_setup_llap.html)  
  * [Hive sunucu yığın boyutunu yapılandırma](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_hiveserver_heap_sizing.html)  
  * [LLAP için eşleme birleştirmesi bellek boyutlandırması](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)  
  * [Tez yürütme altyapısı özellikleri](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_tez_engine_properties.html)  
  * [Hive LLAP derin bakış](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)