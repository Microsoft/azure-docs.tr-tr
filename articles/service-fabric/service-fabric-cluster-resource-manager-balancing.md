---
title: Azure Service Fabric kümenizi dengelemek
description: Kümenizin Service Fabric kümesiyle dengelenmesi için bir giriş Kaynak Yöneticisi.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 192aca589c3b1e660667dbe8377afe7802b56f17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93146203"
---
# <a name="balancing-your-service-fabric-cluster"></a>Service Fabric kümenizi Dengeleme
Service Fabric kümesi Kaynak Yöneticisi, dinamik yükleme değişikliklerini destekler, düğümlerin veya hizmetlerin eklemeleri veya çıkartılması için yeniden davranır. Ayrıca, kısıtlama ihlallerini otomatik olarak düzeltir ve kümeyi yeniden dengeler. Ancak bu eylemler ne sıklıkla alınır ve ne sıklıkta tetiklenir?

Küme Kaynak Yöneticisi gerçekleştirdiği üç farklı çalışma kategorisi vardır. Bunlar:

1. Yerleştirme – bu aşamada, durum bilgisiz çoğaltmalar veya eksik durum bilgisi olmayan örnekler yerleştirilerek ilgilenir. Yerleştirme, hem yeni hizmetleri hem de durum bilgisi olan çoğaltmaları veya başarısız olmayan örnekleri işlemeyi içerir. Çoğaltmaları veya örnekleri silme ve bırakma burada işlenir.
2. Kısıtlama denetimleri – bu aşamada sistem içindeki farklı yerleştirme kısıtlamalarının (kuralların) ihlalleri denetlenir ve düzeltir. Kuralların örnekleri, düğümlerin kapasite üzerinde olmamasını ve bir hizmetin yerleştirme kısıtlamalarının karşılanmasını sağlamaya benzer.
3. Dengeleme – bu aşamada, farklı ölçümler için yapılandırılmış istenen bakiye düzeyine bağlı olarak yeniden dengelemenin gerekli olup olmadığını kontrol eder. Bu durumda, daha dengeli olan kümede bir düzenleme bulmaya çalışır.

## <a name="configuring-cluster-resource-manager-timers"></a>Küme Kaynak Yöneticisi zamanlayıcılarını yapılandırma
Dengelemenin çevresindeki ilk denetim kümesi bir Zamanlayıcı kümesidir. Bu zamanlayıcılar Küme Kaynak Yöneticisi kümeyi ne sıklıkta inceleyeceğini ve düzeltici eylemler gerçekleştirir.

Bu farklı düzeltme türlerinin her biri, Kaynak Yöneticisi kümesi, sıklığını yöneten farklı bir Zamanlayıcı tarafından kontrol edilir. Her süreölçer tetiklendiğinde, görev zamanlanır. Varsayılan olarak Kaynak Yöneticisi:

* durumunu tarar ve bir saniyede her 1/10 ' a kadar olan güncelleştirmeleri uygular.
* Yerleşim denetim bayrağını her saniye ayarlar
* kısıtlama onay bayrağını her saniye ayarlar
* her beş saniyede bir dengeleme bayrağını ayarlar

Bu zamanlayıcıları yöneten yapılandırmaya örnek olarak şunlar verilebilir:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

Tek başına dağıtımlar veya Azure 'da barındırılan kümeler için Template.jsClusterConfig.jsaracılığıyla:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PLBRefreshGap",
          "value": "0.10"
      },
      {
          "name": "MinPlacementInterval",
          "value": "1.0"
      },
      {
          "name": "MinConstraintCheckInterval",
          "value": "1.0"
      },
      {
          "name": "MinLoadBalancingInterval",
          "value": "5.0"
      }
    ]
  }
]
```

Küme Kaynak Yöneticisi, her zaman sırayla bu eylemlerden birini gerçekleştirir. Bu nedenle bu zamanlayıcılar "minimum aralıklar" olarak ve zamanlayıcılar "ayar bayrakları" olarak kapatıldığında gerçekleştirilecek eylemler "olarak adlandırıyoruz. Örneğin Küme Kaynak Yöneticisi, kümeyi dengelemeden önce hizmetleri oluşturmak için bekleyen isteklerden yararlanır. Belirtilen varsayılan zaman aralıklarıyla görebileceğiniz gibi Küme Kaynak Yöneticisi, sık yapılması gereken her şeyi tarar. Normalde bu, her adım için yapılan değişiklik kümesinin küçük olduğu anlamına gelir. Küçük değişiklikler yapmak genellikle küme Kaynak Yöneticisi kümede meydana geldiğinde yanıt vermesini sağlar. Aynı türde olayların birçoğu aynı anda gerçekleşdiğinden, varsayılan zamanlayıcılar bazı toplu işlem sağlar. 

Örneğin, düğümler başarısız olduğunda, her seferinde hata etki alanlarının tamamını yapabilir. Bu hataların hepsi, *Plbrefreshgap* sonrasında bir sonraki durum güncelleştirmesi sırasında yakalanır. Düzeltmeler, aşağıdaki yerleştirme, kısıtlama denetimi ve dengeleme çalıştırmaları sırasında belirlenir. Varsayılan olarak, Küme Kaynak Yöneticisi kümedeki değişikliklere göre tarama değildir ve tüm değişiklikleri tek seferde ele almaya çalışıyor. Bunun yapılması, karmaşıklığın dalgalanmasına neden olur.

Küme Kaynak Yöneticisi, kümenin dengeskümi olup olmadığını tespit etmek için bazı ek bilgiler de gerektirir. Bunun için iki farklı yapılandırma parçası vardır: *BalancingThresholds* ve *activityeşikleri*.

## <a name="balancing-thresholds"></a>Dengeleme eşikleri
Dengeleme eşiği, yeniden dengelemeyi tetiklemenin ana denetimidir. Bir ölçümün Dengeleme eşiği bir _orandır_. En az yüklenen düğümdeki yük miktarına göre ayrılmış olan bir ölçüm için yük, bu ölçüm 'in *BalancingThreshold* değerini aşarsa, küme imlenebilir olur. Bir sonuç dengeleme olarak küme Kaynak Yöneticisi bir sonraki denetim sırasında tetiklenir. *MinLoadBalancingInterval* Zamanlayıcı, yeniden dengelemenin gerekli olup olmadığını Kaynak Yöneticisi kümenin ne sıklıkla denetlemesi gerektiğini tanımlar. Denetim, her şeyin meydana geldiğini ifade etmez. 

Dengeleme eşikleri, küme tanımının bir parçası olarak her ölçüm temelinde tanımlanır. Ölçümler hakkında daha fazla bilgi için [Bu makaleye](service-fabric-cluster-resource-manager-metrics.md)göz atın.

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

Tek başına dağıtımlar veya Azure 'da barındırılan kümeler için Template.jsClusterConfig.jsaracılığıyla:

```json
"fabricSettings": [
  {
    "name": "MetricBalancingThresholds",
    "parameters": [
      {
          "name": "MetricName1",
          "value": "2"
      },
      {
          "name": "MetricName2",
          "value": "3.5"
      }
    ]
  }
]
```

<center>

![Dengeleme eşiği örneği][Image1]
</center>

Bu örnekte, her hizmet bir ölçüm birimini tüketiyor. Üstteki örnekte, bir düğüm üzerindeki en fazla yük beştir ve en az iki olur. Bu ölçüm için Dengeleme eşiğinin üç olduğunu varsayalım. Kümedeki oran 5/2 = 2,5 ve belirtilen Dengeleme eşiğinden daha küçük olduğundan, küme dengelenir. Küme Kaynak Yöneticisi kontrol edildiğinde hiçbir Dengeleme tetiklenmez.

Alt örnekte, bir düğüm üzerindeki en fazla yük 10 ' dur, en az iki ise, beş oranına neden olur. Beş, bu ölçüm için üç belirtilen Dengeleme eşiğinden daha fazladır. Sonuç olarak, bir yeniden dengeleme çalıştırması, Dengeleme süreölçeri bir sonraki sefer tetiklendiğinde zamanlanır. Bu şekilde, bazı yükün genellikle Düğüm3 'e dağıtıldığı durumlar vardır. Service Fabric kümesi kaynak yöneticisi doyumsuz yaklaşımı kullanmadığından, bazı yük da Düğüm2 ' e dağıtılabilir. 

<center>

![Dengeleme eşiği örnek eylemleri][Image2]
</center>

> [!NOTE]
> "Dengeleme", kümenizde yükün yönetilmesi için iki farklı strateji yönetir. Kümenin Kaynak Yöneticisi kullandığı varsayılan strateji, yükü kümedeki düğümler arasında dağıtmaktır. Diğer strateji [birleştirme](service-fabric-cluster-resource-manager-defragmentation-metrics.md). Birleştirme, aynı Dengeleme çalıştırması sırasında gerçekleştirilir. Dengeleme ve birleştirme stratejileri aynı küme içindeki farklı ölçümler için kullanılabilir. Bir hizmette hem Dengeleme hem de birleştirme ölçümleri bulunabilir. Birleştirme ölçümleri için, küme içindeki yüklerin oranı, Dengeleme eşiğinin _altında_ olduğunda yeniden dengelenmesini tetikler. 
>

Aşağıdaki Dengeleme eşiğinin alınması açık bir hedef değildir. Dengeleme eşikleri yalnızca bir *tetikleyiciden* yapılır. Dengeleme çalıştırıldığında küme Kaynak Yöneticisi, varsa hangi geliştirmelerin yapabileceğini belirler. Yalnızca bir dengeleme araması devre dışı bırakıldığı için herhangi bir şeyin hiçbir şekilde taşınmadığı anlamına gelir. Bazen küme, bazı durumlarda de daha fazla kısıtlanmış olabilir. Alternatif olarak, iyileştirmeler çok [maliyetli](service-fabric-cluster-resource-manager-movement-cost.md)hareketler gerektirir.

## <a name="activity-thresholds"></a>Etkinlik eşikleri
Bazen düğümler görece olarak dengeli olsa da, kümedeki yükün *Toplam* miktarı düşüktür. Yük eksikliği geçici bir DIP olabilir veya küme yenidir ve yalnızca önyüklendi alınıyor. Her iki durumda da, kazanılması çok fazla olduğundan kümeyi dengelemeye zaman harcamayı tercih edebilirsiniz. Küme Dengeleme gerçekleştiriyorsa, büyük bir *mutlak* fark yapmadan ağ ve bilgi işlem kaynaklarını bir şeyler arasında hareket ettirmek için harcaymanız gerekir. Gereksiz taşımaları önlemek için, etkinlik eşikleri olarak bilinen başka bir denetim vardır. Etkinlik eşikleri, etkinlik için bazı mutlak alt sınır belirtmenize olanak tanır. Bu eşiğin üzerinde hiçbir düğüm yoksa Dengeleme, Dengeleme eşiğine ulaşılsa bile tetiklenmez.

Bu ölçüm için üç Dengeleme Eşiğimizi koruduğumuz söylüyoruz. Ayrıca, 1536 etkinlik eşiğine sahip olduğumuz bir bakalım. İlk durumda, küme Dengeleme eşiğine göre, bu etkinlik eşiğini karşılayan hiçbir düğüm olmadığı sürece hiçbir şey olmaz. Alt örnekte, Düğüm1 etkinlik eşiğinin üzerinde. Hem Dengeleme eşiği hem de ölçüm için etkinlik eşiği aşıldığından, Dengeleme zamanlanır. Örnek olarak, aşağıdaki diyagrama bakalım: 

<center>

![Etkinlik eşiği örneği][Image3]
</center>

Dengeleme eşiklerine benzer şekilde, etkinlik eşikleri küme tanımı aracılığıyla ölçüm başına tanımlanır:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

Tek başına dağıtımlar veya Azure 'da barındırılan kümeler için Template.jsClusterConfig.jsaracılığıyla:

```json
"fabricSettings": [
  {
    "name": "MetricActivityThresholds",
    "parameters": [
      {
          "name": "Memory",
          "value": "1536"
      }
    ]
  }
]
```

Dengeleme ve etkinlik eşikleri her ikisi de yalnızca aynı ölçüm için hem Dengeleme eşiği hem de etkinlik eşiği aşıldığında tetiklenir.

> [!NOTE]
> Belirtilmediğinde, bir ölçüm için Dengeleme eşiği 1 ve etkinlik eşiği 0 ' dır. Bu, Küme Kaynak Yöneticisi söz konusu bir yük için bu ölçümü kusursuz tutmaya çalışacağı anlamına gelir. Özel ölçümler kullanıyorsanız, ölçümleriniz için kendi dengeliğinizi ve etkinlik eşiklerinizi açıkça tanımlamanız önerilir. 
>

## <a name="balancing-services-together"></a>Hizmetleri birlikte Dengeleme
Kümenin açık olup olmadığı, küme genelinde bir karardır. Bununla birlikte, düzeltmeyi nasıl yaptığımız, tek tek hizmet çoğaltmalarını ve örnekleri etrafında taşımaktır. Bu anlamlı, doğru mu? Bellek bir düğümde yığılır ise, birden çok çoğaltma veya örnek buna katkıda bulunabilir. Dengesizliği düzeltilirken, durum bilgisiz kopyaların veya imdenlü ölçümü kullanan durum bilgisi olmayan örneklerin herhangi birinin taşınması gerekebilir.

Bazen, kendisi de kendi imdenmiş olmayan bir hizmet taşınabilir (daha önce yerel ve küresel ağırlıklarındaki tartışmayı unutmayın). Hizmetin tüm ölçümleri dengeli olduğunda neden bir hizmet taşınır? Bir örnek görelim:

- Dört hizmet, Service1, Service2, Service3 ve service4 olduğunu varsayalım. 
- Service1 Reports ölçümleri Metric1 ve Metric2. 
- Service2 Reports ölçümleri Metric2 ve Metric3. 
- Service3 Reports ölçümleri Metric3 ve Metric4.
- Service4 Reports ölçümü Metric99. 

Burada nereye gittiğimiz hakkında bilgi edinebilirsiniz: bir zincir var! Yalnızca dört bağımsız hizmete sahip olduğumuz ve kendi kendine açık olan üç hizmeti sunuyoruz.

<center>

![Hizmetlerin birlikte nasıl dengelenmesi gerektiğini gösteren diyagram.][Image4]
</center>

Bu zincir nedeniyle, ölçümler 1-4 ' deki bir dengesizliği, hizmetler 1-3 ' e ait çoğaltmalara veya örneklere yol açabilir. Ayrıca, ölçüm 1, 2 veya 3 ' teki bir dengesizin service4 içinde hareketlerine neden olmadığını da biliyoruz. Service4 ' ye ait çoğaltmaları veya örnekleri taşımaya yönelik bir nokta yoktur, ölçüm 1-3 ' inin bakiyesini etkilemek için kesinlikle hiçbir şey yapmaz.

Küme Kaynak Yöneticisi hangi hizmetlerin ilişkili olduğunu otomatik olarak belirler. Hizmetler için ölçümleri ekleme, kaldırma veya değiştirme, ilişkilerini etkileyebilir. Örneğin, Service2 'in iki çalıştırma arasında Metric2 kaldırmak için güncelleştirilmiş olabilir. Bu, Service1 ile Service2 arasındaki zinciri keser. Artık iki ilgili hizmet grubu yerine üç grup vardır:

<center>

![Bu küme Kaynak Yöneticisi hangi hizmetlerin ilgili olduğunu belirten diyagram.][Image5]
</center>

## <a name="next-steps"></a>Sonraki adımlar
* Ölçümler, Service Fabric küme kaynağı Yöneticisi 'nin kümedeki tüketimi ve kapasiteyi nasıl yönettiğini açıklamaktadır. Ölçümler ve bunların nasıl yapılandırılacağı hakkında daha fazla bilgi edinmek için [Bu makaleye](service-fabric-cluster-resource-manager-metrics.md) göz atın
* Taşıma maliyeti, belirli hizmetlerin diğerlerine göre daha pahalı olduğunu Kaynak Yöneticisi küme sinyalinden bir yoldur. Taşıma maliyeti hakkında daha fazla bilgi için [Bu makaleye](service-fabric-cluster-resource-manager-movement-cost.md) bakın
* Küme Kaynak Yöneticisi, kümedeki karmaşıklığı yavaşlatacak şekilde yapılandırabileceğiniz çeşitli kısıtları vardır. Bunlar normalde gerekli değildir, [ancak bunlarla ilgili](service-fabric-cluster-resource-manager-advanced-throttling.md) daha fazla bilgi edinebilirsiniz
* Küme Kaynak Yöneticisi, alt kümelendirmeyi tanıyabilir ve işleyebilir (bazı durumlarda yerleştirme kısıtlamalarını ve dengelemeyi kullandığınızda ortaya çıkar). Alt kümelemenin dengelemeyi nasıl etkileyebileceğini ve nasıl işleyebileceğini öğrenmek için [buraya](cluster-resource-manager-subclustering.md) bakın

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
