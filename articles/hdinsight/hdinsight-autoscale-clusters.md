---
title: Azure HDInsight kümelerini otomatik ölçeklendirme
description: Ölçek kümelerini otomatik olarak Apache Hadoop için Azure HDInsight otomatik ölçeklendirme özelliğini kullanın
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: fff5ad379aa11a0aae14b33f9f82f6da9c794517
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73643689"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Azure HDInsight kümelerini otomatik ölçeklendirme

> [!Important]
> Otomatik Ölçeklendirme özelliği, yalnızca 8 Mayıs 2019 ' den sonra oluşturulan Spark, Hive ve MapReduce kümeleri için geçerlidir. 

Azure HDInsight 'ın küme otomatik ölçeklendirme özelliği, bir kümedeki çalışan düğümlerinin sayısını otomatik olarak ölçeklendirir ve kapatır. Kümedeki diğer düğüm türleri şu anda ölçeklendirilemez.  Yeni bir HDInsight kümesi oluşturulurken, en düşük ve en fazla çalışan düğümü sayısı ayarlanabilir. Sonra otomatik ölçeklendirme, analiz yükünün kaynak gereksinimlerini izler ve çalışan düğümü sayısını yukarı veya aşağı ölçeklendirir. Bu özellik için ek ücret alınmaz.

## <a name="cluster-compatibility"></a>Küme uyumluluğu

Aşağıdaki tablo, otomatik ölçeklendirme özelliğiyle uyumlu küme türlerini ve sürümlerini açıklamaktadır.

| Sürüm | Spark | Hive | LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| ESP olmadan HDInsight 3,6 | Evet | Evet | Hayır | Hayır | Hayır | Hayır | Hayır |
| ESP olmadan HDInsight 4,0 | Evet | Evet | Hayır | Hayır | Hayır | Hayır | Hayır |
| HDInsight 3,6, ESP ile | Evet | Evet | Hayır | Hayır | Hayır | Hayır | Hayır |
| HDInsight 4,0, ESP ile | Evet | Evet | Hayır | Hayır | Hayır | Hayır | Hayır |

## <a name="how-it-works"></a>Nasıl çalışır?

HDInsight kümeniz için yük tabanlı ölçeklendirmeyi veya zamanlama tabanlı ölçeklendirmeyi seçebilirsiniz. Yük tabanlı ölçeklendirme, en iyi CPU kullanımını sağlamak ve çalışan maliyeti en aza indirmek amacıyla kümenizdeki düğüm sayısını, ayarladığınız bir Aralık içinde değiştirir.

Zamanlama tabanlı ölçeklendirme, belirli zamanlarda geçerli olan koşullara göre kümenizdeki düğüm sayısını değiştirir. Bu koşullar, kümeyi istenen sayıda düğüme ölçeklendirebilir.

### <a name="metrics-monitoring"></a>Ölçüm izleme

Otomatik ölçeklendirme, kümeyi sürekli izler ve aşağıdaki ölçümleri toplar:

* **Toplam bekleyen CPU**: bekleyen tüm kapsayıcıların yürütülmesini başlatmak için gereken toplam çekirdek sayısı.
* **Toplam bekleyen bellek**: bekleyen tüm kapsayıcıların yürütülmesini başlatmak için gereken toplam bellek (MB cinsinden).
* **Toplam boş CPU**: etkin çalışan düğümlerinde kullanılmayan tüm çekirdekler toplamı.
* **Toplam boş bellek**: etkin çalışan düğümlerinde kullanılmayan BELLEĞIN (MB cinsinden) toplamı.
* **Düğüm başına kullanılan bellek**: çalışan düğümündeki yük. 10 GB belleğin kullanıldığı bir çalışan düğümü, 2 GB kullanılan bellekle çalışan bir çalışandan daha fazla yük altında kabul edilir.
* **Düğüm başına uygulama ana sayısı**: bir çalışan düğümünde çalışan uygulama ana (Har) kapsayıcıları sayısı. İki har kapsayıcıyı barındıran bir çalışan düğümü, sıfır har kapsayıcıları barındıran bir çalışan düğümünden daha önemli kabul edilir.

Yukarıdaki ölçümler her 60 saniyede bir denetlenir. Otomatik ölçeklendirme, ölçek artırma ve ölçeği azaltma kararlarını bu ölçümlere göre yapar.

### <a name="load-based-cluster-scale-up"></a>Yük tabanlı küme ölçeği artırma

Aşağıdaki koşullar algılandığında otomatik ölçeklendirme, bir ölçek artırma isteği yayınacaktır:

* Toplam bekleyen CPU, 3 dakikadan uzun bir süre boyunca toplam boş CPU 'dan fazla.
* Toplam bekleyen bellek, 3 dakikadan uzun bir süre boyunca toplam boş bellekten fazla.

HDInsight hizmeti, geçerli CPU ve bellek gereksinimlerini karşılamak için kaç yeni çalışan düğümünün gerekli olduğunu hesaplar ve ardından gerekli sayıda düğüm eklemek için bir ölçek isteği yayınlar.

### <a name="load-based-cluster-scale-down"></a>Yük tabanlı küme ölçeği azaltma

Aşağıdaki koşullar algılandığında otomatik ölçeklendirme bir ölçek azaltma isteği ister:

* Toplam bekleyen CPU, 10 dakikadan uzun bir süre için toplam boş CPU 'dan daha az.
* Toplam bekleyen bellek, 10 dakikadan uzun bir süre boyunca toplam boş bellekten daha düşüktür.

Düğüm başına har kapsayıcı sayısına ve geçerli CPU ve bellek gereksinimlerine bağlı olarak, otomatik ölçeklendirme belirli sayıda düğümü kaldırma isteği verir. Hizmet ayrıca hangi düğümlerin geçerli iş yürütmeye göre kaldırılması gerektiğini de algılar. Ölçeği azaltma işlemi, önce düğümleri yeniden komisyonlar ve sonra kümeden kaldırır.

## <a name="get-started"></a>Başlarken

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Yük tabanlı otomatik ölçeklendirme ile küme oluşturma

Bir kümede otomatik ölçeklendirmeyi kullanmak için, küme oluşturulduğunda **Otomatik ölçeklendirmeyi etkinleştir** seçeneğinin etkinleştirilmiş olması gerekir. 

Otomatik ölçeklendirme özelliğini yük tabanlı ölçeklendirmeyle etkinleştirmek için, normal küme oluşturma sürecinin bir parçası olarak aşağıdaki adımları izleyin:

1. **Yapılandırma + fiyatlandırma** sekmesinde **Otomatik ölçeklendirmeyi etkinleştir** onay kutusunu işaretleyin.
1. **Otomatik ölçeklendirme türü**altında **Yük tabanlı** ' i seçin.
1. Aşağıdaki özellikler için istenen değerleri girin:  

    * **Çalışan düğümü**için Ilk **düğüm sayısı** .
    * **En az** çalışan düğüm sayısı.
    * Çalışan düğüm sayısı **üst sınırı** .

    ![Çalışan düğümü yük tabanlı otomatik ölçeklendirmeyi etkinleştir](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

Çalışan düğümlerinin ilk sayısı, dahil en düşük ve en yüksek arasında olmalıdır. Bu değer, oluşturulduğu sırada kümenin başlangıç boyutunu tanımlar. Çalışan düğümlerinin minimum sayısı üç veya daha fazla olarak ayarlanmalıdır. . Kümenizin üçten az düğüme ölçeklendirilmesi, yetersiz dosya çoğaltma nedeniyle güvenli modda takılmasına neden olabilir. Daha fazla bilgi için bkz. [güvenli moda alma]( https://docs.microsoft.com/ azure/hdinsight/hdinsight-scaling-best-practices#getting-stuck-in-safe-mode) .

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Zamanlama tabanlı otomatik ölçeklendirme ile küme oluşturma

Otomatik ölçeklendirme özelliğini zamanlama tabanlı ölçeklendirmeyle etkinleştirmek için, normal küme oluşturma sürecinin bir parçası olarak aşağıdaki adımları izleyin:

1. **Yapılandırma + fiyatlandırma** sekmesinde **Otomatik ölçeklendirmeyi etkinleştir** onay kutusunu işaretleyin.
1. **Çalışan düğümü**Için **düğüm sayısını** girin ve bu, kümenin ölçeğini ölçekleme sınırını denetler.
1. **Otomatik ölçeklendirme türü**altında **zamanlama tabanlı** seçeneğini belirleyin.
1. **Otomatik ölçeklendirme yapılandırma** penceresini açmak için **Yapılandır** ' a tıklayın.
1. Saat diliminizi seçin ve **+ Koşul Ekle** ' ye tıklayın.
1. Yeni koşulun uygulanacağı haftanın günlerini seçin.
1. Koşulun etkili olması gereken süreyi ve kümenin ölçeklendirilmesi gereken düğüm sayısını düzenleyin.
1. Gerekirse daha fazla koşul ekleyin.

    ![Çalışan düğümü zamanlama tabanlı oluşturmayı etkinleştir](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

Düğüm sayısı 3 ile koşul eklemeden önce girmiş olduğunuz çalışan düğümü sayısının üst sınırı olmalıdır.

### <a name="final-creation-steps"></a>Son oluşturma adımları

Hem yük tabanlı hem de zamanlama tabanlı ölçeklendirme için, **düğüm boyutu**altındaki açılan LISTEDEN bir VM seçerek çalışan DÜĞÜMLERI için VM türünü seçin. Her düğüm türü için VM türünü seçtikten sonra, tüm küme için tahmini maliyet aralığını görebilirsiniz. VM türlerini bütçenize uyacak şekilde ayarlayın.

![Çalışan düğümü zamanlama tabanlı otomatik ölçeklendirme düğümü boyutunu etkinleştir](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

Aboneliğinizin her bölge için bir kapasite kotası vardır. Baş düğümlerinizin en fazla sayıda çalışan düğümü ile Birleşik Toplam çekirdek sayısı kapasite kotasını aşamaz. Ancak, bu kota bir hafif limit; her zaman kolayca arttırılabilmeniz için bir destek bileti oluşturabilirsiniz.

> [!Note]  
> Toplam çekirdek kota sınırını aşarsanız, ' en fazla düğüm bu bölgedeki kullanılabilir çekirdekleri aştığından bir hata iletisi alırsınız. lütfen başka bir bölge seçin veya kotayı artırmak için desteğe başvurun. '

Azure portal kullanarak HDInsight kümesi oluşturma hakkında daha fazla bilgi için [Azure Portal kullanarak HDInsight 'Ta Linux tabanlı kümeler oluşturma](hdinsight-hadoop-create-linux-clusters-portal.md)konusuna bakın.  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Kaynak Yöneticisi şablonuyla küme oluşturma

#### <a name="load-based-autoscaling"></a>Yük tabanlı otomatik ölçeklendirme

Aşağıdaki JSON parçacığında gösterildiği gibi özellikler `workernode` ve `minInstanceCount` `computeProfile` > `maxInstanceCount` bölümüne `autoscale` bir düğüm ekleyerek bir Azure Resource Manager şablonu yük tabanlı otomatik ölçeklendirmeyle bir HDInsight kümesi oluşturabilirsiniz.

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

Kaynak Yöneticisi şablonlarıyla kümeler oluşturma hakkında daha fazla bilgi için, bkz. [Kaynak Yöneticisi şablonları kullanarak HDInsight 'ta Apache Hadoop kümeleri oluşturma](hdinsight-hadoop-create-linux-clusters-arm-templates.md).  

#### <a name="schedule-based-autoscaling"></a>Zamanlama tabanlı otomatik ölçeklendirme

`computeProfile` > `workernode` bölümüne `autoscale` bir düğüm ekleyerek bir Azure Resource Manager şablonu zamanlama tabanlı otomatik ölçeklendirmeyle bir HDInsight kümesi oluşturabilirsiniz. `autoscale` düğümü, değişikliğin ne zaman gerçekleştireceğinizi açıklayan bir `timezone` ve `schedule` sahip bir `recurrence` içerir.

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
        },
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4,
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Çalışan bir küme için otomatik ölçeklendirmeyi etkinleştirme ve devre dışı bırakma

#### <a name="using-the-azure-portal"></a>Azure portalını kullanma

Çalışan bir kümede otomatik ölçeklendirmeyi etkinleştirmek için **Ayarlar**altında **küme boyutu** ' nu seçin. Sonra **Otomatik ölçeklendirmeyi etkinleştir**' e tıklayın. İstediğiniz otomatik ölçeklendirme türünü seçin ve yük tabanlı veya zamanlama tabanlı ölçeklendirme seçeneklerini girin. Son olarak **Kaydet**' e tıklayın.

![Çalışan düğümü zamanlama tabanlı otomatik ölçeklendirmeyi etkinleştirme kümesi](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-enable-running-cluster.png)

#### <a name="using-the-rest-api"></a>REST API’sini kullanma

REST API kullanarak çalışan bir kümede otomatik ölçeklendirmeyi etkinleştirmek veya devre dışı bırakmak için, aşağıdaki kod parçacığında gösterildiği gibi otomatik ölçeklendirme uç noktasına bir POST isteği oluşturun:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

İstek yükünde uygun parametreleri kullanın. Aşağıdaki JSON yükü otomatik ölçeklendirmeyi etkinleştirmek için kullanılabilir. Otomatik ölçeklendirmeyi devre dışı bırakmak için yük `{autoscale: null}` kullanın.

```json
{ autoscale: { capacity: { minInstanceCount: 3, maxInstanceCount: 2 } } }
```

Tüm yük parametrelerinin tam açıklaması için [Yük tabanlı otomatik ölçeklendirmeyi etkinleştirme](#load-based-autoscaling) konusundaki önceki bölüme bakın.

## <a name="best-practices"></a>En iyi uygulamalar

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Yük tabanlı veya zamanlama tabanlı ölçeklendirmeyi seçme

Hangi mod üzerinde seçim yapmaya karar vermeden önce aşağıdaki faktörleri göz önünde bulundurun:

* Küme oluşturma sırasında otomatik ölçeklendirmeyi etkinleştirin.
* Düğüm sayısı alt sınırı en az üç olmalıdır.
* Yük sapması: küme yükü, belirli saatlerde, belirli günlerde tutarlı bir model izler. Aksi takdirde, yük tabanlı zamanlama daha iyi bir seçenektir.
* SLA gereksinimleri: otomatik ölçeklendirme ölçeği, tahmine dayalı yerine reaktif. Yükün ne zaman artmaya başladığı ve kümenin hedef boyutunda olması gereken süre arasında yeterli gecikme olur mu? Katı SLA gereksinimleri varsa ve yük sabit bir bilinen örüntü ise, ' zamanlama tabanlı ' daha iyi bir seçenektir.

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Ölçek artırma veya azaltma işlemlerinin gecikme süresini göz önünde bulundurun

Ölçeklendirme işleminin tamamlanması 10 ila 20 dakika sürebilir. Özelleştirilmiş bir zamanlama ayarlarken bu gecikmeyi planlayın. Örneğin, küme boyutunun 9:00 ' de 20 olması gerekiyorsa, ölçeklendirme işleminin 9:00 "ile tamamlanmasını sağlamak için zamanlama tetikleyicisini 8:30 olarak daha önceki bir zamana ayarlayın.

### <a name="preparation-for-scaling-down"></a>Ölçeği azaltma hazırlığı

Küme ölçeklendirme işlemi sırasında, otomatik ölçeklendirme, hedef boyutunu karşılamak için düğümlerin yetkisini alınır. Bu düğümlerde çalışan görevler varsa, otomatik ölçeklendirme görevler tamamlanana kadar bekler. Her çalışan düğüm da bir rol görevi görüyor olduğundan, geçici veriler kalan düğümlere kaydıralınacaktır. Bu nedenle, tüm geçici verileri barındırmak için kalan düğümlerde yeterli alan olduğundan emin olun.

Çalışan işler çalışmaya ve sonuna kadar devam edecektir. Bekleyen işler, daha az kullanılabilir çalışan düğümü ile normal olarak zamanlanmayı bekler.

### <a name="minimum-cluster-size"></a>En düşük küme boyutu

Kümenizi üçten az düğüme ölçeklendirmeyin. Kümenizin üçten az düğüme ölçeklendirilmesi, yetersiz dosya çoğaltma nedeniyle güvenli modda takılmasına neden olabilir. Daha fazla bilgi için bkz. [güvenli moda alma]( https://docs.microsoft.com/ azure/hdinsight/hdinsight-scaling-best-practices#getting-stuck-in-safe-mode) .

## <a name="monitoring"></a>İzleme

### <a name="cluster-status"></a>Küme durumu

Azure portal listelenen küme durumu, otomatik ölçeklendirme etkinliklerini izlemenize yardımcı olabilir.

![Çalışan düğümü yük tabanlı otomatik ölçeklendirme kümesi durumunu etkinleştir](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Görebileceğiniz tüm küme durumu iletileri aşağıdaki listede açıklanmıştır.

| Küme durumu | Açıklama |
|---|---|
| Çalışıyor | Küme normal şekilde çalışıyor. Önceki otomatik ölçeklendirme etkinliklerinin tümü başarıyla tamamlandı. |
| Bilen  | Küme otomatik ölçeklendirme yapılandırması güncelleştiriliyor.  |
| HDInsight yapılandırması  | Bir küme ölçeği artırma veya genişletme işlemi devam ediyor.  |
| Güncelleştirme hatası  | HDInsight, otomatik ölçeklendirme yapılandırma güncelleştirmesi sırasında sorunlarla karşılaştı. Müşteriler güncelleştirmeyi yeniden denemeyi seçebilir veya otomatik ölçeklendirmeyi devre dışı bırakabilir.  |
| Hata  | Kümede bir sorun var ve kullanılabilir değil. Bu kümeyi silip yeni bir tane oluşturun.  |

Kümenizdeki düğümlerin geçerli sayısını görüntülemek için, kümenizin **genel bakış** sayfasında **küme boyutu** grafiğine gidin veya **Ayarlar**altında **küme boyutu** ' na tıklayın.

### <a name="operation-history"></a>İşlem geçmişi

Küme ölçümlerinin bir parçası olarak küme ölçeği artırma ve ölçeği azaltma geçmişini görüntüleyebilirsiniz. Ayrıca, geçen gün, hafta veya başka bir süre içinde tüm ölçekleme eylemlerini de listeleyebilirsiniz.

**İzleme**altında **ölçümler** ' i seçin. Ardından **ölçüm** açılan kutusundan ölçüm ve **etkin çalışan sayısı** **Ekle** ' ye tıklayın. Zaman aralığını değiştirmek için sağ üstteki düğmeye tıklayın.

![Çalışan düğümü zamanlama tabanlı otomatik ölçeklendirme ölçümünü etkinleştir](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Ölçek en iyi uygulamalarında](hdinsight-scaling-best-practices.md) kümeleri el ile ölçeklendirmeye yönelik en iyi yöntemler hakkında bilgi edinin
