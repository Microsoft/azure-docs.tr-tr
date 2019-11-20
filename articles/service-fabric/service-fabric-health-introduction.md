---
title: Service Fabric 'de sistem durumu izleme | Microsoft Docs
description: Kümenin ve uygulama ve hizmetlerinin izlenmesini sağlayan Azure Service Fabric sistem durumu izleme modeline giriş.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: chackdan
editor: ''
ms.assetid: 1d979210-b1eb-4022-be24-799fd9d8e003
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: d0ef9f34d6b657a063e50b0f144197c41905e809
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2019
ms.locfileid: "60949190"
---
# <a name="introduction-to-service-fabric-health-monitoring"></a>Service Fabric sistem durumu izlemeye giriş
Azure Service Fabric, zengin, esnek ve Genişletilebilir sistem durumu değerlendirmesi ve raporlama sağlayan bir sistem durumu modeli sunar. Model, küme durumunun ve üzerinde çalışan hizmetlerin neredeyse gerçek zamanlı olarak izlenmesine olanak tanır. Kolayca sistem durumu bilgilerini alabilir ve olası sorunları basamaklandırmadan ve büyük kesintilere neden olacak şekilde düzeltebilirsiniz. Tipik modelde, hizmetler raporları yerel görünümlerine göre gönderir ve bu bilgiler genel bir küme düzeyi görünüm sağlamak için toplanır.

Service Fabric bileşenleri, geçerli durumlarını raporlamak için bu zengin sistem durumu modelini kullanır. Uygulamalarınızdan sistem durumunu raporlamak için aynı mekanizmayı kullanabilirsiniz. Özel koşullarınızı yakalayan yüksek kaliteli sistem durumu raporlaması yatırdığınızda, çalışan uygulamanızın sorunlarını daha kolay tespit edebilir ve giderebilirsiniz.

> [!NOTE]
> İzlenen yükseltmeler gereksinimini karşılamak için sistem durumu alt sistemini başladık. Service Fabric, tam kullanılabilirlik, kesinti olmaması ve Kullanıcı müdahalesi olmadan en düşük düzeyde emin olmanızı sağlayan izlenen uygulama ve küme yükseltmeleri sağlar. Bu hedeflere ulaşmak için yükseltme, yapılandırılan yükseltme ilkelerine göre sistem durumunu denetler. Bir yükseltme yalnızca sistem durumu istenen eşiklere sahip olduğunda devam edebilir. Aksi halde, yöneticilere sorunları çözme şansı vermek için yükseltme otomatik olarak geri alınır veya duraklatılır. Uygulama yükseltmeleri hakkında daha fazla bilgi edinmek için [Bu makaleye](service-fabric-application-upgrade.md)bakın.
> 
> 

## <a name="health-store"></a>Sistem durumu deposu
Sistem durumu deposu kolay alma ve değerlendirme için kümedeki varlıklar hakkında sistem durumuyla ilgili bilgileri tutar. Yüksek kullanılabilirlik ve ölçeklenebilirlik sağlamak için Service Fabric kalıcı durum bilgisi olan bir hizmet olarak uygulanır. Sistem durumu deposu **doku:/sistem** uygulamasının bir parçasıdır ve küme çalışır duruma geldiğinde kullanılabilir.

## <a name="health-entities-and-hierarchy"></a>Sistem durumu varlıkları ve hiyerarşisi
Sistem durumu varlıkları, farklı varlıklar arasında etkileşimleri ve bağımlılıkları yakalayan bir mantıksal hiyerarşide düzenlenir. Sistem durumu deposu, Service Fabric bileşenlerinden alınan raporlara göre sistem durumu varlıklarını ve hiyerarşisini otomatik olarak oluşturur.

Durum varlıkları Service Fabric varlıklarını yansıtır. (Örneğin, **sistem durumu uygulama varlığı** , kümede dağıtılan bir uygulama örneğiyle eşleşir, ancak **sistem durumu düğümü varlığı** bir Service Fabric küme düğümüyle eşleşir.) Sistem durumu hiyerarşisi Sistem varlıklarının etkileşimlerini yakalar ve gelişmiş sistem durumu değerlendirmesi için temeldir. Temel Service Fabric kavramlarıyla ilgili [Service Fabric teknik genel bakış](service-fabric-technical-overview.md)hakkında bilgi edinebilirsiniz. Uygulama hakkında daha fazla bilgi için bkz. [uygulama modeli Service Fabric](service-fabric-application-model.md).

Sistem durumu varlıkları ve hiyerarşisi kümenin ve uygulamaların etkili bir şekilde raporlanabilmesi, ayıklanayıklanması ve izlenmesi için izin verir. Sistem durumu modeli, kümedeki birçok hareketli parçanın durumunun doğru ve *ayrıntılı* bir gösterimini sağlar.

![Sistem durumu varlıkları.][1]
Üst-alt ilişkileri temel alan bir hiyerarşide düzenlenmiş sistem durumu varlıkları.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Durum varlıkları şunlardır:

* **Küme**. Bir Service Fabric kümesinin sistem durumunu temsil eder. Küme durumu raporları tüm kümeyi etkileyen koşulları anlatmaktadır. Bu koşullar, kümedeki veya kümedeki birden çok varlığı etkiler. Koşula bağlı olarak, Raporlayıcı sorunu bir veya daha fazla sağlıksız alt öğeye karşı daraltamıyorum. Bu örnek, ağ bölümleme veya iletişim sorunları nedeniyle küme bölme işleminin beyde bulunur.
* **Düğüm**. Bir Service Fabric düğümünün sistem durumunu temsil eder. Düğüm durumu raporları, düğüm işlevselliğini etkileyen koşulları anlatmaktadır. Genellikle üzerinde çalışan tüm dağıtılan varlıkları etkiler. Örnekler arasında düğüm kalmadı (veya bellek, bağlantılar gibi diğer makine genelindeki Özellikler) ve bir düğüm çalışmıyor. Düğüm varlığı, düğüm adı (dize) tarafından tanımlanır.
* **Uygulama**. Kümede çalışan bir uygulama örneğinin sistem durumunu temsil eder. Uygulama durumu raporları, uygulamanın genel sistem durumunu etkileyen koşulları anlatmaktadır. Tek tek alt öğelere (hizmetler veya dağıtılan uygulamalar) göre daraltılamazlar. Örnek olarak, uygulamadaki farklı hizmetler arasında uçtan uca etkileşim vardır. Uygulama varlığı, uygulama adı (URI) tarafından tanımlanır.
* **Hizmet**. Kümede çalışan bir hizmetin sistem durumunu temsil eder. Hizmet durumu raporları hizmetin genel durumunu etkileyen koşulları anlatmaktadır. Raporlayıcı sorunu sağlıksız bir bölüm veya çoğaltmada daraltamıyorum. Örnekler, tüm bölümler için sorunlara neden olan bir hizmet yapılandırması (örneğin, bağlantı noktası veya harici dosya paylaşma) içerir. Hizmet varlığı, hizmet adı (URI) tarafından tanımlanır.
* **Bölüm**. Bir hizmet bölümünün sistem durumunu temsil eder. Bölüm durumu raporları, tüm çoğaltma kümesini etkileyen koşulları anlatmaktadır. Örnek sayısı, hedef sayısı ' nın altında ve bir bölüm de çekirdek kaybolduğunda verilebilir. Bölüm varlığı, bölüm KIMLIĞI (GUID) tarafından tanımlanır.
* **Çoğaltma**. Durum bilgisi olan bir hizmet çoğaltmasının veya durum bilgisi olmayan hizmet örneğinin sistem durumunu temsil eder. Çoğaltma, Watchdogs ve sistem bileşenlerinin bir uygulama için raporlayabilir en küçük birimdir. Durum bilgisi olan hizmetler için, örnekleri ikincil çoğaltma ve yavaş çoğaltma işlemlerine çoğaltamaz. Ayrıca, durum bilgisi olmayan bir örnek, kaynakların tükenme durumunda veya bağlantı sorunlarıyla karşılaşarak rapor verebilir. Çoğaltma varlığı, bölüm KIMLIĞI (GUID) ve çoğaltma ya da örnek KIMLIĞI (Long) tarafından tanımlanır.
* **Dağıtıcı**. *Düğüm üzerinde çalışan bir uygulamanın*sistem durumunu temsil eder. Dağıtılan uygulama durumu raporları, düğümdeki uygulamaya özgü koşulları, aynı düğümde dağıtılan hizmet paketlerine karşı daha dar olmayan koşullara göre anlatmaktadır. Örnek olarak, uygulama paketinin bu düğümde indirileceği ve düğümde uygulama güvenliği sorumlularını ayarlamayla ilgili sorunlar yer alır. Dağıtılan uygulama, uygulama adı (URI) ve düğüm adı (dize) tarafından tanımlanır.
* **Deployedservicepackage**. Kümedeki bir düğümde çalışan bir hizmet paketinin sistem durumunu temsil eder. Aynı uygulama için aynı düğümdeki diğer hizmet paketlerini etkilemeyen bir hizmet paketine özgü koşulları açıklar. Örnek olarak, hizmet paketinde başlatılamayacağını ve okunamayan bir yapılandırma paketini içeren bir kod paketi bulunur. Dağıtılan hizmet paketi, uygulama adı (URI), düğüm adı (dize), hizmet bildirimi adı (dize) ve hizmet paketi etkinleştirme KIMLIĞI (dize) ile tanımlanır.

Sistem durumu modelinin ayrıntı düzeyi sorunları algılamayı ve düzeltmeyi kolaylaştırır. Örneğin, bir hizmet yanıt vermiyorsa, uygulama örneğinin sağlıksız olduğunu bildirmek mümkündür. Bununla birlikte, bu düzeydeki raporlama ideal değildir, çünkü sorun bu uygulamadaki tüm hizmetleri etkilemeyebilir. Bu bölüme daha fazla bilgi gösteriyorsa, raporun sağlıksız hizmete veya belirli bir alt bölüme uygulanması gerekir. Veriler hiyerarşide otomatik olarak yüzey sergiler ve sağlıksız bir bölüm hizmet ve uygulama düzeylerinde görünür hale getirilir. Bu toplama, sorunun kök nedenini daha hızlı bir şekilde belirlemenize ve çözmeye yardımcı olur.

Sistem durumu hiyerarşisi üst-alt ilişkilerinden oluşur. Bir küme, düğümler ve uygulamalardan oluşur. Uygulamalarda hizmetler ve dağıtılan uygulamalar vardır. Dağıtılan uygulamalar hizmet paketlerini dağıttı. Hizmetler bölümlere sahiptir ve her bölümde bir veya daha fazla çoğaltma bulunur. Düğümler ve dağıtılan varlıklar arasında özel bir ilişki vardır. Yetkili sistem bileşeni tarafından bildirilen, Yük Devretme Yöneticisi hizmeti, dağıtılmış uygulamaları, hizmet paketlerini ve üzerinde dağıtılan çoğaltmaları etkileyen, sağlıksız bir düğüm.

Sistem durumu hiyerarşisi, neredeyse gerçek zamanlı bilgiler olan en son sistem durumu raporlarına göre sistemin en son durumunu temsil eder.
İç ve dış Watchdogs, uygulamaya özgü mantık veya özel izlenen koşullara göre aynı varlıklarda rapor verebilir. Kullanıcı raporları sistem raporları ile birlikte bulunamaz.

Büyük bir bulut hizmeti tasarımı sırasında sistem durumunu raporlama ve yanıt verme konusunda yatırım yapın. Bu ön yatırım, hizmetin hata ayıklamayı, izlenmesini ve çalışmasını kolaylaştırır.

## <a name="health-states"></a>Sağlık durumları
Service Fabric, bir varlığın sağlıklı olup olmadığını betimleyen üç sistem durumu kullanır: Tamam, uyarı ve hata. Sistem durumu deposuna gönderilen herhangi bir rapor, bu durumlardan birini belirtmelidir. Sistem durumu değerlendirme sonucu, bu durumlardan biridir.

Olası [sistem durumları](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate) şunlardır:

* **TAMAM**. Varlık sağlıklı. Üzerinde veya alt öğelerinde (geçerli olduğunda) bildirilen bilinen bir sorun yoktur.
* **Uyarı**. Varlıkta bazı sorunlar vardır, ancak yine de düzgün bir şekilde çalışabilir. Örneğin, gecikme vardır ancak herhangi bir işlevsel soruna neden olmaz. Bazı durumlarda, uyarı koşulu dış müdahale olmadan kendi kendine düzelebilir. Bu durumlarda, sistem durumu raporları tanıma işlemi yaptığını ve neye devam ettiğinin görünürlüğünü sağlar. Diğer durumlarda, uyarı koşulu Kullanıcı müdahalesi olmadan ciddi bir sorunla azalabilir.
* **Hata**. Varlık sağlıksız. Doğru şekilde işleyemediği için varlığın durumunu onarmak üzere eylem gerçekleştirilmelidir.
* **Bilinmiyor**. Varlık sistem durumu deposunda yok. Bu sonuç, birden çok bileşenden sonuçları birleştirme dağıtılan sorgulardan elde edilebilir. Örneğin, düğüm listesini al sorgusu **failovermanager**, **Clustermanager**ve **healthmanager**' a gider; Uygulama listesi sorgusunun al, **clustermanager** ve **healthmanager**'a gider. Bu sorgular, birden çok sistem bileşeninden sonuçları birleştirir. Başka bir sistem bileşeni, sistem durumu deposunda mevcut olmayan bir varlık döndürürse, birleştirilmiş sonuç bilinmeyen sistem durumuna sahiptir. Sistem durumu raporları henüz işlenmediğinden veya silme işleminden sonra varlık temizlendiğinden varlık depoda değil.

## <a name="health-policies"></a>Sistem durumu ilkeleri
Sistem durumu deposu, bir varlığın raporlarına ve alt öğelerine göre sağlıklı olup olmadığını anlamak için sistem durumu ilkeleri uygular.

> [!NOTE]
> Sistem durumu ilkeleri küme bildiriminde (küme ve düğüm durumu değerlendirmesi için) veya uygulama bildiriminde (uygulama değerlendirmesi ve alt öğelerinden herhangi biri için) belirtilebilir. Durum değerlendirme istekleri, yalnızca bu değerlendirme için kullanılan özel durum değerlendirme ilkeleri de geçirebilir.
> 
> 

Varsayılan olarak Service Fabric, üst-alt hiyerarşik ilişki için katı kurallar uygular (her şey sağlıklı olmalıdır). Alt öğelerinden birinin sağlıksız bir olayı varsa, üst öğe sağlıksız olarak değerlendirilir.

### <a name="cluster-health-policy"></a>Küme durumu ilkesi
Küme [sistem durumu ilkesi](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy) , küme sistem durumu ve düğüm durumu durumlarını değerlendirmek için kullanılır. İlke, küme bildiriminde tanımlanabilir. Mevcut değilse, varsayılan ilke (sıfır toleranslı başarısızlık) kullanılır.
Küme durumu ilkesi şunları içerir:

* [ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Sistem durumu değerlendirmesi sırasında uyarı durumu raporlarının hata olarak değerlendirilip değerlendirilmeyeceğini belirtir. Varsayılan: false.
* [Maxyüztunhealthyapplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications). Küme hatalı olarak değerlendirilmeden önce sağlıksız olabilecek uygulamaların en fazla toleranslı yüzdesini belirtir.
* [Maxyüztunhealthyınodes](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes). Küme hatalı olarak değerlendirilmeden önce sağlıksız olabilecek düğümlerin maksimum toleranslı yüzdesini belirtir. Büyük kümelerde, bazı düğümler her zaman tamir için veya çıkış olduğundan bu yüzdenin bu yüzdeyi kabul etmek üzere yapılandırılması gerekir.
* [Applicationtypehealthpolicymap](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap). Uygulama türü sistem durumu ilkesi eşlemesi, özel uygulama türlerini belirtmek için küme durumu değerlendirmesi sırasında kullanılabilir. Varsayılan olarak, tüm uygulamalar bir havuza konur ve Maxyüztunhealthuygulamaplications ile değerlendirilir. Bazı uygulama türleri farklı şekilde değerlendirilmelidir, bu, genel havuzdan alınmış olabilir. Bunun yerine, haritadaki uygulama türü adlarıyla ilişkili yüzdelerle değerlendirilir. Örneğin, bir kümede farklı türlerde binlerce uygulama vardır ve özel bir uygulama türünün birkaç denetim uygulaması örneği vardır. Denetim uygulamaları asla hata içermemelidir. Bazı hatalara sızmak için genel Maxyüztunhealthtoplications ' ı% 20 olarak belirtebilirsiniz ancak "ControlApplicationType" uygulama türü için Maxyüztunhealthtoplications değerini 0 olarak ayarlayın. Bu şekilde, birçok uygulamanın bir kısmı sağlıksız ise, ancak genel sağlıksız yüzdesinin altında, küme uyarı olarak değerlendirilir. Uyarı sistem durumu, küme yükseltmesini veya hata sistem durumu tarafından tetiklenen diğer izlemeyi etkilemez. Aynı olsa da, hata içindeki bir denetim uygulaması, yükseltme yapılandırmasına bağlı olarak küme yükseltmesini tetikleyen veya duraklayıp, kümeyi sağlıksız hale getirir.
  Haritada tanımlanan uygulama türleri için, tüm uygulama örnekleri genel uygulama havuzunda alınır. Bu uygulamalar, eşlemedeki belirli Maxyüztunhealthme plications kullanılarak uygulama türünün Toplam uygulama sayısına göre değerlendirilir. Tüm uygulamaların geri kalanı genel havuzda kalır ve Maxyüztunhealthyapplications ile değerlendirilir.

Aşağıdaki örnek, bir küme bildiriminin alıntısıdır. Uygulama türü eşlemesinde girdileri tanımlamak için, parametre adının önüne "Applicationtypemaxyüztunhealthyapplications-" ve ardından uygulama türü adını ekleyin.

```xml
<FabricSettings>
  <Section Name="HealthManager/ClusterHealthPolicy">
    <Parameter Name="ConsiderWarningAsError" Value="False" />
    <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
    <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
    <Parameter Name="ApplicationTypeMaxPercentUnhealthyApplications-ControlApplicationType" Value="0" />
  </Section>
</FabricSettings>
```

### <a name="application-health-policy"></a>Uygulama sistem durumu ilkesi
[Uygulama sistem durumu ilkesi](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy) , uygulamalar ve bunların alt öğeleri için olay değerlendirmesinin ve alt durum toplama işlemlerinin nasıl yapıldığını açıklar. Uygulama paketindeki **ApplicationManifest. xml**uygulama bildiriminde tanımlanabilir. Hiçbir ilke belirtilmemişse Service Fabric, bir sistem durumu raporu veya uyarı ya da hata sistem durumunda alt öğe varsa varlığın sağlıksız olduğunu varsayar.
Yapılandırılabilir ilkeler şunlardır:

* [ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Sistem durumu değerlendirmesi sırasında uyarı durumu raporlarının hata olarak değerlendirilip değerlendirilmeyeceğini belirtir. Varsayılan: false.
* [Maxyüztunhealthyıdeployedapplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications). Uygulamanın hatalı olarak değerlendirilmeden önce sağlıksız olabilecek dağıtılan uygulamaların en fazla toleranslı yüzdesini belirtir. Bu yüzde, uygulamaların kümede halen dağıtıldığı düğüm sayısına göre sağlıksız dağıtılan uygulama sayısı bölünerek hesaplanır. Hesaplama, küçük sayıda düğümde bir hata olduğunu tolerans için yukarı yuvarlar. Varsayılan yüzde: sıfır.
* [Defaultservicetypehealthpolicy](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy). Uygulamadaki tüm hizmet türleri için varsayılan sistem durumu ilkesinin yerini alan varsayılan hizmet türü sistem durumu ilkesini belirtir.
* [Servicetypehealthpolicymap](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap). Hizmet türü başına hizmet sistem durumu ilkelerinin haritasını sağlar. Bu ilkeler, belirtilen her hizmet türü için varsayılan hizmet türü sistem durumu ilkelerinin yerini alır. Örneğin, bir uygulamanın durum bilgisiz ağ geçidi hizmet türü ve durum bilgisi olan bir altyapı hizmeti türü varsa, değerlendirmesi için sistem durumu ilkelerini farklı şekilde yapılandırabilirsiniz. Hizmet türü başına ilke belirttiğinizde, hizmetin sistem durumunun daha ayrıntılı bir denetimini elde edebilirsiniz.

### <a name="service-type-health-policy"></a>Hizmet türü sistem durumu ilkesi
[Hizmet türü sistem durumu ilkesi](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy) , hizmetlerin ve hizmetlerin alt öğelerinin nasıl değerlendirileceğini ve toplanacağını belirtir. İlke şunları içerir:

* [Maxyüztunhealthypartitionsperservice](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice). Hizmetin sağlıksız olduğu kabul edilmeden önce sağlıksız bölümlerin maksimum toleranslı yüzdesini belirtir. Varsayılan yüzde: sıfır.
* [Maxyüztunhealthyıreplicasperpartition](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition). Bir bölümün sağlıksız olduğu kabul edilmeden önce sağlıksız çoğaltmaların en yüksek toleranslı yüzdesini belirtir. Varsayılan yüzde: sıfır.
* [Maxyüztunhealthi Hizmetleri](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices). Uygulamanın sağlıksız olduğu kabul edilmeden önce sağlıksız hizmetlerin maksimum toleranslı yüzdesini belirtir. Varsayılan yüzde: sıfır.

Aşağıdaki örnek, bir uygulama bildiriminin alıntısıdır:

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## <a name="health-evaluation"></a>Sistem durumu değerlendirmesi
Kullanıcılar ve otomatik hizmetler, herhangi bir zamanda herhangi bir varlık için sistem durumunu değerlendirebilir. Bir varlığın sistem durumunu değerlendirmek için, sistem durumu deposu varlıktaki tüm sistem durumu raporlarını toplar ve tüm alt öğelerini (uygun olduğunda) değerlendirir. Sistem durumu toplama algoritması, sistem durumu raporlarının nasıl değerlendirileceğini ve alt sistem durumu durumlarının (varsa) nasıl toplanacağını belirten sistem durumu ilkelerini kullanır.

### <a name="health-report-aggregation"></a>Durum raporu toplama
Bir varlıkta farklı özelliklerle (sistem bileşenleri veya Watchdogs) farklı özelliklerle gönderilen birden fazla sistem durumu raporu olabilir. Toplama, özellikle uygulamanın veya küme durumu ilkesinin ConsiderWarningAsError üyesi olan ilişkili sistem durumu ilkelerini kullanır. ConsiderWarningAsError, uyarıların nasıl değerlendirileceğini belirtir.

Toplu sistem durumu, varlıktaki en *kötü* durum raporları tarafından tetiklenir. En az bir hata sistem durumu raporu varsa, toplanmış sistem durumu bir hatadır.

![Hata raporuyla sistem durumu raporu toplama.][2]

Bir veya daha fazla hata sistem durumu raporu içeren bir sistem durumu varlığı hata olarak değerlendirilir. Aynı durum, sistem durumu ne olursa olsun, zaman aşımına uğradı durum raporu için de geçerlidir.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Hata raporu ve bir veya daha fazla uyarı yoksa, ConsiderWarningAsError ilke bayrağına bağlı olarak, toplanmış sistem durumu uyarı veya hata olur.

![Uyarı raporu ve ConsiderWarningAsError false ile sistem durumu raporu toplama.][3]

Uyarı raporu ve ConsiderWarningAsError ile durum raporu toplama, false (varsayılan) olarak ayarlanır.

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Alt durum toplama
Bir varlığın toplu sistem durumu, alt sistem durumu durumlarını yansıtır (varsa). Alt durum durumlarını toplama algoritması, varlık türüne göre geçerli olan sistem durumu ilkelerini kullanır.

![Alt varlıklar durum toplama.][4]

Sistem durumu ilkelerine göre alt toplama.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Sistem durumu deposu tüm alt öğeleri değerlendirdikten sonra, sağlıksız alt öğelerin yapılandırılan maksimum yüzdesine göre sistem durumlarını toplar. Bu yüzde, varlığa ve alt türüne göre ilkeden alınır.

* Tüm alt öğelerin Tamam durumu varsa, alt toplanmış sistem durumu Tamam olur.
* Alt öğelerin hem Tamam hem de uyarı durumları varsa, alt toplanmış sistem durumu uyarı olur.
* Hatalı çalışmayan alt öğelerin izin verilen maksimum yüzdesine göre olmayan alt öğeler varsa, toplanan ana sistem durumu bir hatadır.
* Hata içeren alt öğeler, sağlıksız alt öğelerin izin verilen maksimum yüzdesine göre bildiriliyorsa, toplanan ana sistem durumu uyarı olur.

## <a name="health-reporting"></a>Sistem durumu raporlaması
Sistem bileşenleri, System Fabric uygulamaları ve iç/dış Watchdogs Service Fabric varlıklara göre rapor verebilir. Raporla, izlenmekte olan koşullara göre izlenen varlıkların sistem durumunu Yereller halinde yapar. Herhangi bir genel duruma veya toplama verilerine bakmaları gerekmez. İstenen davranış, hangi bilgilerin gönderileceğini belirlemek için birçok şeyi göz atabilmeniz gereken karmaşık organiler değildir.

Sistem durumu deposuna sistem durumu verileri göndermek için, bir Raporlayıcı etkilenen varlığı belirleyip bir sistem durumu raporu oluşturur. Raporu göndermek için, `Partition` veya `CodePackageActivationContext` nesneleri, PowerShell cmdlet 'leri veya REST üzerinde kullanıma sunulan [FabricClient. healthclient. reporthegizlilik](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API 'sini, rapor durumu API 'lerini kullanın.

### <a name="health-reports"></a>Sistem durumu raporları
Kümedeki varlıkların her biri için [sistem durumu raporları](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthreport) aşağıdaki bilgileri içerir:

* **SourceId**. Sistem durumu olayının Raporlayıcısı 'nı benzersiz bir şekilde tanımlayan bir dize.
* **Varlık tanımlayıcısı**. Raporun uygulandığı varlığı tanımlar. [Varlık türüne](service-fabric-health-introduction.md#health-entities-and-hierarchy)göre farklılık gösterir:
  
  * İçi. Yok.
  * Düğümüne. Düğüm adı (dize).
  * Uygulamanızı. Uygulama adı (URI). Kümede dağıtılan uygulama örneğinin adını temsil eder.
  * Hizmetle. Hizmet adı (URI). Kümede dağıtılan hizmet örneğinin adını temsil eder.
  * Bölümünüzün. Bölüm KIMLIĞI (GUID). Bölüm benzersiz tanımlayıcısını temsil eder.
  * Yinelemeden. Durum bilgisi olan hizmet çoğaltma KIMLIĞI veya durum bilgisi olmayan hizmet örneği KIMLIĞI (ıNT64).
  * DeployedApplication. Uygulama adı (URI) ve düğüm adı (dize).
  * DeployedServicePackage. Uygulama adı (URI), düğüm adı (dize) ve hizmet bildirim adı (dize).
* **Özelliği**. Raporlamasının, varlığın belirli bir özelliği için sistem durumu olayını sınıflandırmalarına olanak tanıyan bir *dize* (sabit bir sabit listesi değil). Örneğin, Raporlayıcı, Node01 "Storage" özelliğinin sistem durumunu bildirebilir ve Reporter B, Node01 "Connectivity" özelliğinin sistem durumunu bildirebilir. Sistem durumu deposunda, bu raporlar Node01 varlığı için ayrı sistem durumu olayları olarak kabul edilir.
* **Açıklama**. Bir Raporlayıcı sistem durumu olayı hakkında ayrıntılı bilgi sağlamasına izin veren bir dize. **SourceId**, **Property**ve **HealthState** raporu tam olarak tanımlıyor. Açıklama, raporla ilgili olarak okunabilir bilgiler ekler. Metin, yöneticilerin ve kullanıcıların sistem durumu raporunu anlamasına daha kolay hale getirir.
* **HealthState**. Raporun sistem durumunu açıklayan [sabit listesi](service-fabric-health-introduction.md#health-states) . Kabul edilen değerler Tamam, uyarı ve hatadır.
* **TimeToLive**. Sistem durumu raporunun geçerli olduğu süreyi belirten bir TimeSpan. **Removewhenererle**bağlı olarak, sistem durumu deposunun süre dolma olaylarının nasıl değerlendirileceğini bilmesini sağlar. Varsayılan olarak, değer sonsuz ' dur ve rapor süresiz olarak geçerli olur.
* **Removewhen, zaman aşımına uğradı**. Boole değeri. True olarak ayarlanırsa, süresi biten sistem durumu raporu otomatik olarak sistem durumu deposundan kaldırılır ve rapor, varlık durumu değerlendirmesini etkilemez. Rapor yalnızca belirli bir süre için geçerli olduğunda ve Raporlayıcı açık bir şekilde temizlenme gereği olmadığında kullanılır. Ayrıca sistem durumu deposundan raporları silmek için de kullanılır (örneğin, bir izleme değiştirilir ve önceki kaynak ve özellik ile rapor gönderilmesini durdurmaktadır). Sistem durumu deposundan önceki bir durumu temizlemek için, Removewhentımeerle birlikte kısa bir süre içinde bir rapor gönderebilir. Değer false olarak ayarlandıysa, süresi geçen rapor durum değerlendirmesinde hata olarak değerlendirilir. False değeri, kaynağın bu özellikte düzenli olarak rapor etmesi gereken durum deposuna bildirir. Değilse, izleme ile ilgili bir sorun olması gerekir. İzleme sistem durumu olay bir hata olarak düşünüldüğünde yakalanır.
* **SequenceNumber**. Her zaman artırılması gereken pozitif bir tamsayı, raporların sırasını temsil eder. Ağ gecikmeleri veya diğer sorunlar nedeniyle geç alınan eski raporları algılamak için sistem durumu deposu tarafından kullanılır. Aynı varlık, kaynak ve özellik için sıra numarası en son uygulanan sayıdan küçük veya ona eşitse bir rapor reddedilir. Belirtilmemişse, sıra numarası otomatik olarak oluşturulur. Sıra numarasına yalnızca durum geçişleri bildirilmeye göre koymak gereklidir. Bu durumda, kaynağın hangi raporların gönderildiğini anımsamasını ve yük devretmede bilgileri kurtarmaya devam etmesini sağlaması gerekir.

Bu dört bilgi parçası--SourceId, Entity Identifier, Property ve HealthState--her sistem durumu raporu için gereklidir. SourceID dizesinin, sistem raporları için ayrılmış olan "**System.** " önekiyle başlayamaz. Aynı varlık için aynı kaynak ve özellik için yalnızca bir rapor vardır. Aynı kaynak ve özellik için birden çok rapor, sistem durumu istemci tarafında (toplu olmaları durumunda) veya sistem durumu deposu tarafında geçersiz kılınır. Değişiklik sıra numaralarına dayalıdır; daha yeni raporlar (daha yüksek sıra numaralarıyla) eski raporların yerini alır.

### <a name="health-events"></a>Sistem durumu olayları
Dahili olarak, sistem durumu deposu raporlardan tüm bilgileri ve ek meta verileri içeren [sistem durumu olaylarını](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthevent)tutar. Meta veriler, raporun sistem durumu istemcisine verildiği süreyi ve sunucu tarafında değiştirilme süresini içerir. Sistem durumu olayları, [sistem durumu sorguları](service-fabric-view-entities-aggregated-health.md#health-queries)tarafından döndürülür.

Eklenen meta veriler şunları içerir:

* **Sourceutctimestamp**. Raporun sistem durumu istemcisine verildiği zaman (Eşgüdümlü Evrensel Saat).
* **Lastmodifiedutctimestamp**. Raporun sunucu tarafında son değiştirilme zamanı (Eşgüdümlü Evrensel Saat).
* **IsExpired**. Sorgu sistem durumu deposu tarafından yürütüldüğünde raporun zaman aşımına ermediğini belirten bayrak. Bir olay, yalnızca Removewhendolmuşsa false olduğunda zaman aşımına uğradı. Aksi takdirde, olay sorgu tarafından döndürülmez ve mağazadan kaldırılır.
* **LastOkTransitionAt**, **lastwarninggeçişli tionat**, **lasterrorgeçişli tionat**. Tamam/uyarı/hata geçişleri için son zaman. Bu alanlar, olay için sistem durumu geçişlerinin geçmişini verir.

Durum geçiş alanları, daha akıllı uyarılar veya "geçmiş" sistem durumu olay bilgileri için kullanılabilir. Bunlar gibi senaryolara olanak tanır:

* X dakikadan uzun bir süre için bir özellik uyarı/hata olduğunda uyarır. Bir süre için koşulun denetlenmesi geçici koşullarda uyarıları önler. Örneğin, sistem durumunun beş dakikadan uzun süre uyarı vermesi durumunda bir uyarı (HealthState = = uyarı ve şimdi-Lastwarninggeçişli Tiontime > 5 dakika) arasında çevrilebilir.
* Yalnızca son X dakika içinde değişen koşullara uyar. Bir rapor belirtilen süreden daha önce hatada zaten zaten olduğu için yok sayılır.
* Bir özellik uyarı ve hata arasında geçiş yaparken, ne kadar sağlıklı olduğunu (yani, tamam değil) saptayın. Örneğin, özelliğin beş dakikadan uzun süre sağlıklı olması durumunda bir uyarı (HealthState! = ok ve Now-LastOkTransitionTime > 5 dakika) arasında çevrilebilir.

## <a name="example-report-and-evaluate-application-health"></a>Örnek: Uygulama durumunu raporlama ve değerlendirme
Aşağıdaki örnek, uygulama dokusunda PowerShell aracılığıyla bir sistem durumu raporu gönderir **:/WordCount** from **myizleyici**. Sistem durumu raporu, bir hata sağlık durumunda "kullanılabilirlik" Sağlık özelliği ile sonsuz TimeToLive ile ilgili bilgiler içerir. Ardından, toplanan sistem durumu hatalarını ve sistem durumu olayları listesinde bildirilen sistem durumu olaylarını döndüren uygulama durumunu sorgular.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 131032204762818013
                                  SentAt                : 3/23/2016 3:27:56 PM
                                  ReceivedAt            : 3/23/2016 3:27:56 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Ok->Error = 3/23/2016 3:27:56 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="health-model-usage"></a>Sistem durumu modeli kullanımı
Durum modeli, bulut hizmetleri ve temel alınan Service Fabric platformunun, izleme ve sağlık belirlemeleri küme içindeki farklı izleyicilerin dağıtıldığı için ölçeklenebilmesini sağlar.
Diğer sistemler, hizmetler tarafından yayılan tüm *olası* yararlı bilgileri çözümleyen, küme düzeyinde tek ve merkezi bir hizmete sahiptir. Bu yaklaşım, ölçeklenebilirliğini de azaltabilir. Ayrıca, sorunların ve olası sorunların mümkün olduğunca asıl nedenle yakın olduğunu belirlemenize yardımcı olmak için belirli bilgiler toplamasına izin vermez.

Durum modeli, izleme ve Tanılama için, küme ve uygulama durumunu değerlendirmek ve izlenen yükseltmeler için çok yoğun bir şekilde kullanılır. Diğer hizmetler otomatik onarımlar yapmak, küme durumu geçmişini derlemek ve belirli koşullarda uyarı vermek için sistem durumu verilerini kullanır.

## <a name="next-steps"></a>Sonraki adımlar
[Service Fabric sistem durumu raporlarını görüntüleme](service-fabric-view-entities-aggregated-health.md)

[Sorun giderme için sistem durumu raporlarını kullanma](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Hizmet durumunu raporlama ve denetleme](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Özel Service Fabric sistem durumu raporları ekleme](service-fabric-report-health.md)

[Hizmetleri yerel olarak izleme ve tanılama](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Uygulama yükseltmesini Service Fabric](service-fabric-application-upgrade.md)

