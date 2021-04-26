---
title: Azure HDInsight kümelerini otomatik olarak ölçeklendirme
description: Azure HDInsight kümelerini bir zamanlamaya veya performans ölçümlerine göre otomatik olarak ölçeklendirmek için otomatik ölçeklendirme özelliğini kullanın.
ms.service: hdinsight
ms.topic: how-to
ms.custom: contperf-fy21q1, contperf-fy21q2
ms.date: 12/14/2020
ms.openlocfilehash: 5dabae76308f32da7968d8cfa89b95f1eb19c142
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863777"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Azure HDInsight kümelerini otomatik olarak ölçeklendirme

Azure HDInsight 'ın ücretsiz otomatik ölçeklendirme özelliği, daha önce ayarlanan ölçütlere göre kümenizdeki çalışan düğümlerinin sayısını otomatik olarak artırabilir veya azaltabilir. Otomatik Ölçeklendirme özelliği, performans ölçümlerine veya ölçek artırma ve ölçek azaltma işlemlerine göre önceden ayarlanmış limitlerin içindeki düğüm sayısını ölçeklendirerek çalışır.

## <a name="how-it-works"></a>Nasıl çalışır?

Otomatik Ölçeklendirme özelliği, ölçeklendirme olaylarını tetiklemek için iki tür koşul kullanır: çeşitli küme performansı ölçümleri ( *Yük tabanlı ölçeklendirme* olarak adlandırılır) ve zaman tabanlı tetikleyiciler ( *zamanlama tabanlı ölçeklendirme* olarak adlandırılır). Yük tabanlı ölçeklendirme, en iyi CPU kullanımını sağlamak ve çalışan maliyeti en aza indirmek için, kümenizdeki düğüm sayısını sizin ayarladığınız bir Aralık içinde değiştirir. Zamanlama tabanlı ölçeklendirme, ölçek büyütme ve azaltma işlemlerinin bir zamanlaması temelinde kümenizdeki düğüm sayısını değiştirir.

Aşağıdaki videoda, otomatik ölçeklendirmeyi çözen zorlukları ve HDInsight ile maliyetleri denetlemenize nasıl yardımcı olabilecek hakkında genel bir bakış sunulmaktadır.

> [!VIDEO https://www.youtube.com/embed/UlZcDGGFlZ0?WT.mc_id=dataexposed-c9-niner]

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Yük tabanlı veya zamanlama tabanlı ölçeklendirmeyi seçme

Bir ölçeklendirme türü seçerken aşağıdaki faktörleri göz önünde bulundurun:

* Yük sapması: küme yükünün belirli saatlerde, belirli günlerde tutarlı bir düzende izlenmesi mi? Aksi takdirde, yük tabanlı zamanlama daha iyi bir seçenektir.
* SLA gereksinimleri: otomatik ölçeklendirme ölçeği, tahmine dayalı yerine reaktif. Yükün ne zaman artmaya başladığı ve kümenin hedef boyutunda olması gereken süre arasında yeterli gecikme olur mu? Katı SLA gereksinimleri varsa ve yük sabit bir bilinen örüntü ise, ' zamanlama tabanlı ' daha iyi bir seçenektir.

### <a name="cluster-metrics"></a>Küme ölçümleri

Otomatik ölçeklendirme, kümeyi sürekli izler ve aşağıdaki ölçümleri toplar:

|Metric|Açıklama|
|---|---|
|Toplam bekleyen CPU|Tüm bekleyen kapsayıcıları yürütmeye başlamak için gereken toplam çekirdek sayısı.|
|Toplam bekleyen bellek|Tüm bekleyen kapsayıcıların yürütülmesini başlatmak için gereken toplam bellek (MB cinsinden).|
|Toplam boş CPU|Etkin çalışan düğümlerinde kullanılmayan tüm çekirdekler toplamı.|
|Toplam boş bellek|Etkin çalışan düğümlerinde kullanılmayan belleğin (MB cinsinden) toplamı.|
|Düğüm başına kullanılan bellek|Çalışan düğümündeki yük. 10 GB belleğin kullanıldığı bir çalışan düğümü, 2 GB kullanılan bellekle çalışan bir çalışandan daha fazla yük altında kabul edilir.|
|Düğüm başına uygulama ana sayısı|Çalışan düğümünde çalışan uygulama ana (Har) kapsayıcıları sayısı. İki har kapsayıcıyı barındıran bir çalışan düğümü, sıfır har kapsayıcıları barındıran bir çalışan düğümünden daha önemli kabul edilir.|

Yukarıdaki ölçümler her 60 saniyede bir denetlenir. Bu ölçülerden herhangi birini kullanarak kümeniz için ölçeklendirme işlemleri ayarlayabilirsiniz.

### <a name="load-based-scale-conditions"></a>Yük tabanlı ölçek koşulları

Aşağıdaki koşullar algılandığında otomatik ölçeklendirme bir ölçek isteği ister:

|Ölçeği büyütme|Ölçeği azalt|
|---|---|
|Toplam bekleyen CPU, 3 dakikadan uzun bir süre boyunca toplam boş CPU 'dan fazla.|Toplam bekleyen CPU, 10 dakikadan uzun bir süre için toplam boş CPU 'dan daha az.|
|Toplam bekleyen bellek, 3 dakikadan uzun bir süre boyunca toplam boş bellekten fazla.|Toplam bekleyen bellek, 10 dakikadan uzun bir süre boyunca toplam boş bellekten daha düşüktür.|

Ölçek artırma için, otomatik ölçeklendirme gereken sayıda düğüm eklemek için bir ölçek isteği verir. Ölçek artırma, geçerli CPU ve bellek gereksinimlerini karşılamak için kaç yeni çalışan düğümünün gerekli olduğunu temel alır.

Ölçek azaltma için, otomatik ölçeklendirme belirli sayıda düğümü kaldırma isteği verir. Ölçek azaltma, düğüm başına düşen saat kapsayıcısı sayısına bağlıdır. Ve geçerli CPU ve bellek gereksinimleri. Hizmet ayrıca hangi düğümlerin geçerli iş yürütmeye göre kaldırılması gerektiğini de algılar. Ölçeği azaltma işlemi, önce düğümleri yeniden komisyonlar ve sonra kümeden kaldırır.

### <a name="cluster-compatibility"></a>Küme uyumluluğu

> [!Important]
> Azure HDInsight Otomatik Ölçeklendirme özelliği, Spark ve Hadoop kümeleri için 7 Kasım 2019’da genel kullanıma sunulmuştu ve özelliğin önizleme sürümünde sağlanmayan geliştirmeler içeriyordu. 7 Kasım 2019’dan önce Spark kümesi oluşturduysanız ve kümenizde Otomatik Ölçeklendirme özelliğini kullanmak istiyorsanız, yeni küme oluşturma ve yeni kümede Otomatik Ölçeklendirme’yi etkinleştirme yolunu izlemeniz önerilir.
>
> Etkileşimli sorgu (LLAP) için otomatik ölçeklendirme, 6 Ağustos 2020 tarihinde HDI 4,0 için genel kullanıma sunuluyordu. HBase kümeleri hala önizlemededir. Otomatik ölçeklendirme yalnızca Spark, Hadoop, Interactive Query ve HBase kümelerinde kullanılabilir.

Aşağıdaki tablo, otomatik ölçeklendirme özelliğiyle uyumlu küme türlerini ve sürümlerini açıklamaktadır.

| Sürüm | Spark | Hive | Interactive Query | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| ESP olmadan HDInsight 3,6 | Yes | Yes | Yes | Evet* | Hayır | Hayır | Hayır |
| ESP olmadan HDInsight 4,0 | Yes | Yes | Yes | Evet* | Hayır | Hayır | Hayır |
| HDInsight 3,6, ESP ile | Yes | Yes | Yes | Evet* | Hayır | Hayır | Hayır |
| HDInsight 4,0, ESP ile | Yes | Yes | Yes | Evet* | Hayır | Hayır | Hayır |

\* HBase kümeleri, yük tabanlı değil yalnızca zamanlama tabanlı ölçeklendirme için yapılandırılabilir.

## <a name="get-started"></a>başlarken

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Yük tabanlı otomatik ölçeklendirme ile küme oluşturma

Otomatik ölçeklendirme özelliğini yük tabanlı ölçeklendirmeyle etkinleştirmek için, normal küme oluşturma sürecinin bir parçası olarak aşağıdaki adımları izleyin:

1. **Yapılandırma + fiyatlandırma** sekmesinde **Otomatik ölçeklendirmeyi etkinleştir** onay kutusunu seçin.
1. **Otomatik ölçeklendirme türü** altında **Yük tabanlı** ' i seçin.
1. Aşağıdaki özellikler için amaçlanan değerleri girin:  

    * **Çalışan düğümü** için Ilk **düğüm sayısı** .
    * **En az** çalışan düğüm sayısı.
    * Çalışan düğüm sayısı **üst sınırı** .

    :::image type="content" source="./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png" alt-text="Çalışan düğümü yük tabanlı otomatik ölçeklendirmeyi etkinleştir":::

Çalışan düğümlerinin ilk sayısı, dahil en düşük ve en yüksek arasında olmalıdır. Bu değer, oluşturulduğu sırada kümenin başlangıç boyutunu tanımlar. Çalışan düğümlerinin minimum sayısı üç veya daha fazla olarak ayarlanmalıdır. Kümenizin üçten az düğüme ölçeklendirilmesi, yetersiz dosya çoğaltma nedeniyle güvenli modda takılmasına neden olabilir.  Daha fazla bilgi için bkz. [güvenli modda takılme](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Zamanlama tabanlı otomatik ölçeklendirme ile küme oluşturma

Otomatik ölçeklendirme özelliğini zamanlama tabanlı ölçeklendirmeyle etkinleştirmek için, normal küme oluşturma sürecinin bir parçası olarak aşağıdaki adımları izleyin:

1. **Yapılandırma + fiyatlandırma** sekmesinde **Otomatik ölçeklendirmeyi etkinleştir** onay kutusunu işaretleyin.
1. **Çalışan düğümü** Için **düğüm sayısını** girin ve bu, kümenin ölçeğini ölçekleme sınırını denetler.
1. **Otomatik ölçeklendirme türü** altında **zamanlama tabanlı** seçeneğini belirleyin.
1. **Otomatik ölçeklendirme yapılandırma** penceresini açmak için **Yapılandır** ' ı seçin.
1. Saat diliminizi seçin ve **+ Koşul Ekle** ' ye tıklayın.
1. Yeni koşulun uygulanacağı haftanın günlerini seçin.
1. Koşulun etkili olması gereken süreyi ve kümenin ölçeklendirilmesi gereken düğüm sayısını düzenleyin.
1. Gerekirse daha fazla koşul ekleyin.

    :::image type="content" source="./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png" alt-text="Çalışan düğümü zamanlama tabanlı oluşturmayı etkinleştir":::

Düğüm sayısı 3 ile koşul eklemeden önce girmiş olduğunuz çalışan düğümü sayısının üst sınırı olmalıdır.

### <a name="final-creation-steps"></a>Son oluşturma adımları

**Düğüm boyutu** altında aşağı açılan LISTEDEN bir VM seçerek çalışan DÜĞÜMLERI için VM türünü seçin. Her düğüm türü için VM türünü seçtikten sonra, tüm küme için tahmini maliyet aralığını görebilirsiniz. VM türlerini bütçenize uyacak şekilde ayarlayın.

:::image type="content" source="./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png" alt-text="Çalışan düğümü zamanlama tabanlı otomatik ölçeklendirme düğümü boyutunu etkinleştir":::

Aboneliğinizin her bölge için bir kapasite kotası vardır. Baş düğümlerinizin toplam çekirdek sayısı ve en yüksek çalışan düğümleri kapasite kotasını aşamaz. Ancak, bu kota bir hafif limit; her zaman kolayca arttırılabilmeniz için bir destek bileti oluşturabilirsiniz.

> [!Note]  
> Toplam çekirdek kota sınırını aşarsanız, ' en fazla düğüm bu bölgedeki kullanılabilir çekirdekleri aştığından bir hata iletisi alırsınız. lütfen başka bir bölge seçin veya kotayı artırmak için desteğe başvurun. '

Azure portal kullanarak HDInsight kümesi oluşturma hakkında daha fazla bilgi için [Azure Portal kullanarak HDInsight 'Ta Linux tabanlı kümeler oluşturma](hdinsight-hadoop-create-linux-clusters-portal.md)konusuna bakın.  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Kaynak Yöneticisi şablonuyla küme oluşturma

#### <a name="load-based-autoscaling"></a>Yük tabanlı otomatik ölçeklendirme

`autoscale` `computeProfile`  >  `workernode` Özellikler ile bölüme bir düğüm ekleyerek `minInstanceCount` ve `maxInstanceCount` aşağıdaki JSON kod parçacığında gösterildiği gibi, Azure Resource Manager şablonu yük tabanlı otomatik ölçeklendirmeyle bir HDInsight kümesi oluşturabilirsiniz. Tam bir Kaynak Yöneticisi Şablon için bkz. [hızlı başlangıç şablonu: yük tabanlı otomatik ölçeklendirme etkinken Spark kümesi dağıtma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-loadbased).

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 3,
          "maxInstanceCount": 10
      }
  },
  "hardwareProfile": {
      "vmSize": "Standard_D13_V2"
  },
  "osProfile": {
      "linuxOperatingSystemProfile": {
          "username": "[parameters('sshUserName')]",
          "password": "[parameters('sshPassword')]"
      }
  },
  "virtualNetworkProfile": null,
  "scriptActions": []
}
```

#### <a name="schedule-based-autoscaling"></a>Zamanlama tabanlı otomatik ölçeklendirme

Bölüme bir düğüm ekleyerek bir Azure Resource Manager şablonu zamanlama tabanlı otomatik ölçeklendirmeyle bir HDInsight kümesi oluşturabilirsiniz `autoscale` `computeProfile`  >  `workernode` . `autoscale`Düğüm, `recurrence` ' a sahip olan `timezone` ve `schedule` değişikliğin ne zaman gerçekleştireceğinizi açıklayan bir içerir. Tam bir Kaynak Yöneticisi şablonu için bkz. [zamanlama tabanlı otomatik ölçeklendirme etkinken Spark kümesini dağıtma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-schedulebased).

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        }
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Çalışan bir küme için otomatik ölçeklendirmeyi etkinleştirme ve devre dışı bırakma

#### <a name="using-the-azure-portal"></a>Azure portalını kullanma

Çalışan bir kümede otomatik ölçeklendirmeyi etkinleştirmek için **Ayarlar** altında **küme boyutu** ' nu seçin. Sonra **Otomatik ölçeklendirmeyi etkinleştir**' i seçin. İstediğiniz otomatik ölçeklendirme türünü seçin ve yük tabanlı veya zamanlama tabanlı ölçeklendirme seçeneklerini girin. Son olarak **Kaydet**' i seçin.

:::image type="content" source="./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png" alt-text="Çalışan düğümü zamanlama tabanlı otomatik ölçeklendirmeyi etkinleştirme kümesi":::

#### <a name="using-the-rest-api"></a>REST API’yi kullanma

REST API kullanarak çalışan bir kümede otomatik ölçeklendirmeyi etkinleştirmek veya devre dışı bırakmak için, otomatik ölçeklendirme uç noktasına bir POST isteği oluşturun:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

İstek yükünde uygun parametreleri kullanın. Aşağıdaki JSON yükü otomatik ölçeklendirmeyi etkinleştirmek için kullanılabilir. `{autoscale: null}`Otomatik ölçeklendirmeyi devre dışı bırakmak için yükü kullanın.

```json
{ "autoscale": { "capacity": { "minInstanceCount": 3, "maxInstanceCount": 5 } } }
```

Tüm yük parametrelerinin tam açıklaması için [Yük tabanlı otomatik ölçeklendirmeyi etkinleştirme](#load-based-autoscaling) konusundaki önceki bölüme bakın.

## <a name="monitoring-autoscale-activities"></a>Otomatik ölçeklendirme etkinliklerini izleme

### <a name="cluster-status"></a>Küme durumu

Azure portal listelenen küme durumu, otomatik ölçeklendirme etkinliklerini izlemenize yardımcı olabilir.

:::image type="content" source="./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png" alt-text="Çalışan düğümü yük tabanlı otomatik ölçeklendirme kümesi durumunu etkinleştir":::

Görebileceğiniz tüm küme durumu iletileri aşağıdaki listede açıklanmıştır.

| Küme durumu | Description |
|---|---|
| Çalışma | Küme normal şekilde çalışıyor. Önceki otomatik ölçeklendirme etkinliklerinin tümü başarıyla tamamlandı. |
| Güncelleştirme  | Küme otomatik ölçeklendirme yapılandırması güncelleştiriliyor.  |
| HDInsight yapılandırması  | Bir küme ölçeği artırma veya genişletme işlemi devam ediyor.  |
| Güncelleştirme hatası  | HDInsight, otomatik ölçeklendirme yapılandırma güncelleştirmesi sırasında sorunlar karşıladı. Müşteriler güncelleştirmeyi yeniden denemeyi seçebilir veya otomatik ölçeklendirmeyi devre dışı bırakabilir.  |
| Hata  | Kümede bir sorun var ve kullanılabilir değil. Bu kümeyi silip yeni bir tane oluşturun.  |

Kümenizdeki düğümlerin geçerli sayısını görüntülemek için, kümenizin **genel bakış** sayfasında **küme boyutu** grafiğine gidin. Veya **Ayarlar** altında **küme boyutu** ' nu seçin.

### <a name="operation-history"></a>İşlem geçmişi

Küme ölçümlerinin bir parçası olarak küme ölçeği artırma ve ölçeği azaltma geçmişini görüntüleyebilirsiniz. Ayrıca, geçen gün, hafta veya başka bir süre içinde tüm ölçekleme eylemlerini de listeleyebilirsiniz.

**İzleme** altında **ölçümler** ' i seçin. Ardından **ölçüm** açılan kutusundan ölçüm ve **etkin çalışan sayısı** **Ekle** ' yi seçin. Zaman aralığını değiştirmek için sağ üstteki düğmeyi seçin.

:::image type="content" source="./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png" alt-text="Çalışan düğümü zamanlama tabanlı otomatik ölçeklendirme ölçümünü etkinleştir":::

## <a name="best-practices"></a>En iyi uygulamalar

### <a name="consider-the-latency-of-scale-up-and-scale-down-operations"></a>Ölçek artırma ve ölçeği azaltma işlemlerinin gecikmesini göz önünde bulundurun

Ölçeklendirme işleminin tamamlanması 10 ila 20 dakika sürebilir. Özelleştirilmiş bir zamanlama ayarlarken bu gecikmeyi planlayın. Örneğin, küme boyutunun 9:00 ' de 20 olması gerekiyorsa, ölçeklendirme işleminin 9:00 "ile tamamlanmasını sağlamak için zamanlama tetikleyicisini 8:30 olarak daha önceki bir zamana ayarlayın.

### <a name="prepare-for-scaling-down"></a>Ölçeklendirme için hazırlanma

Küme ölçeklendirme işlemi sırasında, otomatik ölçeklendirme, düğümleri hedef boyutunu karşılayacak şekilde kaldırır. Görevler bu düğümlerde çalışıyorsa, otomatik ölçeklendirme, Spark ve Hadoop kümeleri için görevler tamamlanana kadar bekler. Her çalışan düğüm da bir rol görevi görüyor olduğundan, geçici veriler kalan düğümlere kaydırılacağı. Tüm geçici verileri barındırmak için kalan düğümlerde yeterli alan olduğundan emin olun.

Çalışan işler devam edecektir. Bekleyen işler, daha az kullanılabilir çalışan düğümü ile zamanlamaya göre bekleyecektir.

### <a name="be-aware-of-the-minimum-cluster-size"></a>En düşük küme boyutunu bilmelisiniz

Kümenizi üçten az düğüme ölçeklendirmeyin. Kümenizin üçten az düğüme ölçeklendirilmesi, yetersiz dosya çoğaltma nedeniyle güvenli modda takılmasına neden olabilir. Daha fazla bilgi için bkz. [güvenli modda takılme](hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

### <a name="increase-the-number-of-mappers-and-reducers"></a>Mapbir ve azaltıcının sayısını artırma

Hadoop kümelerinin otomatik ölçeklendirmeyi Ayrıca, bu işlemleri izler. Bu,, kümenin hala geçerli kaynaklara ihtiyacı olduğunu varsayar. Sorguda büyük miktarda veri olduğunda, paralelliği artırmak ve hacimsel işlemleri hızlandırmak için mapzm ve azaltıcının sayısını artırabilirsiniz. Bu şekilde, ek kaynak olduğunda doğru ölçeklendirme tetiklenecektir. 

### <a name="set-the-hive-configuration-maximum-total-concurrent-queries-for-the-peak-usage-scenario"></a>En yüksek kullanım senaryosu için Hive yapılandırması için maksimum eşzamanlı sorgu ayarlama

Otomatik ölçeklendirme olayları, ambarı 'nda bulunan *en fazla eşzamanlı sorgu* olan Hive yapılandırmasını değiştirmez. Yani, Hive Server 2 etkileşimli hizmeti, etkileşimli sorgu Daemon 'ları sayısı, yük ve zamanlamaya göre ölçeği yukarı ve aşağı ölçeklense bile, her zaman herhangi bir zamanda yalnızca verilen sayıda eşzamanlı sorgu işleyebilir. Genel öneri, bu yapılandırmayı el ile müdahale önlemek için en yoğun kullanım senaryosuna göre ayarlamadır.

Ancak, yalnızca az sayıda çalışan düğümü varsa ve en fazla toplam eşzamanlı sorgu sayısı çok yüksek olduğunda bir Hive sunucu 2 yeniden başlatma hatası yaşayabilirsiniz. En azından, belirtilen sayıda tez AMS (maksimum eşzamanlı sorgu yapılandırmasına eşit) barındırabilecek çalışan düğümü sayısının en az olması gerekir. 

## <a name="limitations"></a>Sınırlamalar

### <a name="node-label-file-missing"></a>Düğüm etiketi dosyası eksik

HDInsight otomatik ölçeklendirme, bir düğümün görevleri yürütmeye hazırlanma olup olmadığını anlamak için bir düğüm etiketi dosyası kullanır. Düğüm etiketi dosyası, üç çoğaltmaya göre ve üzerinde depolanır. Küme boyutu önemli ölçüde azaltıldığı ve büyük miktarda geçici veri varsa, bu üç çoğaltmanın de bırakılması küçük bir şansınız olur. Bu durumda, küme bir hata durumu girer.

### <a name="interactive-query-daemons-count"></a>Etkileşimli sorgu Daemon 'ları sayısı

Otomatik Ölçeklendirme özelliği etkinleştirilmiş etkileşimli sorgu kümelerinde bir otomatik ölçek artırma/azaltma olayı ayrıca etkileşimli sorgu Daemon 'ları sayısının etkin çalışan düğümlerinin sayısına göre ölçeğini/ölçeğini de ölçeklendirir. Daemon 'ları sayısında değişiklik, `num_llap_nodes` ambarı 'ndaki yapılandırmada kalıcı değildir. Hive hizmetleri el ile yeniden başlatılırsa, Daemon 'ları etkileşimli sorgu sayısı, ambarı 'nda yapılandırma uyarınca sıfırlanır.

Etkileşimli sorgu hizmeti el ile yeniden başlatılırsa, `num_llap_node` geçerli etkin çalışan düğüm sayısıyla eşleşecek şekilde yapılandırmayı (Hive etkileşimli sorgu cini 'nı çalıştırmak için gereken düğüm sayısı) *Gelişmiş Hive-Interactive-env* altında el ile değiştirmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

[Ölçek kılavuzlarından](hdinsight-scaling-best-practices.md) el ile ölçek ölçekleme yönergeleri hakkında bilgi edinin
