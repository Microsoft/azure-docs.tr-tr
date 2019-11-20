---
title: Kapsayıcılar için Azure Izleyici durum izleyici yapılandırması | Microsoft Docs
description: Bu makale, kapsayıcılar için Azure Izleyici 'de sistem durumu izleyicilerinin ayrıntılı yapılandırmasını açıklayan içerik sağlar.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/12/2019
ms.author: magoedte
ms.openlocfilehash: b782581318751830ec47b9fecb056fecefb353eb
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134963"
---
# <a name="azure-monitor-for-containers-health-monitor-configuration-guide"></a>Kapsayıcılar için Azure Izleyici durum izleyici yapılandırma kılavuzu

İzleyiciler, Azure Izleyici 'de kapsayıcılar için sistem durumunu ölçmeye ve hataları algılamaya yönelik birincil öğedir. Bu makale, sağlık [özelliği](container-insights-health.md)Ile Kubernetes kümenizin sistem durumunu izlemek ve raporlamak için sistem durumunun ölçülme ve sistem durumu modelini oluşturan öğelerin kavramlarını anlamanıza yardımcı olur.

## <a name="monitors"></a>İzleyiciler

Bir izleyici, yönetilen bir nesnenin bazı yönlerinin sağlığını ölçer. İzleyicilerin her biri iki veya üç sistem durumuna sahiptir. Bir izleyici belirli bir zamanda potansiyel durumlarından birinde ve yalnızca birinde olacaktır. Kapsayıcılı aracı tarafından yüklenen bir izleyici, sağlıklı bir duruma başlatılır. Durum yalnızca, başka bir durum için belirtilen koşullar algılandığında değişir.

Belirli bir nesnenin genel sistem durumu, izleyicilerinin her birinin sistem durumundan belirlenir. Bu hiyerarşi, kapsayıcılar için Azure Izleyici 'de sistem durumu hiyerarşisi bölmesinde gösterilmiştir. Sistem durumunun alındığı ilke toplam izleyici yapılandırmasının bir parçasıdır.

## <a name="types-of-monitors"></a>İzleyici türleri

|İzleme | Açıklama | 
|--------|-------------|
| Birim izleyicisi |Birim izleyicisi bir kaynağın veya uygulamanın bazı yönlerini ölçer. Bu, kaynağın performansını veya kullanılabilirliğini belirlemede bir performans sayacı denetleniyor olabilir. |
|Toplam Izleyici | Toplama izleyicileri, tek bir sistem durumu toplu sistem durumu sağlamak için birden çok monitörü gruplar. Birim izleyicileri genellikle belirli bir toplam izleyici altında yapılandırılır. Örneğin, bir düğüm toplama izleyicisi, düğüm CPU kullanımı, bellek kullanımı ve düğüm durumunun durumunu toplar.
 |

### <a name="aggregate-monitor-health-rollup-policy"></a>Toplam izleme sistem durumu toplama ilkesi

Her toplam izleyici, altındaki izleyicilerin sistem durumunu temel alarak toplam izleyicinin durumunu belirlemede kullanılan mantık olan bir sistem durumu toplama ilkesi tanımlar. Bir toplam izleyici için olası durum toplama ilkeleri aşağıdaki gibidir:

#### <a name="worst-state-policy"></a>En kötü durum ilkesi

Toplam izleyicinin durumu, en kötü sistem durumu ile alt izleyicinin durumuyla eşleşir. Bu, toplam izleyiciler tarafından kullanılan en yaygın ilkedir.

![Toplu izleyici toplamasının en kötü durumu örneği](./media/container-insights-health-monitoring-cfg/aggregate-monitor-rollup-worstof.png)

### <a name="percentage-policy"></a>Yüzde ilkesi

Kaynak nesne, en iyi durumda, belirtilen hedef nesne yüzdesinin tek bir üyesinin en kötü durumuyla eşleşir. Bu ilke, hedef nesnenin sağlıklı olarak kabul edilmesi için belirli bir hedef nesne yüzdesi sağlıklı olması gereken durumlarda kullanılır. Yüzde ilkesi izleyicileri durum önem derecesine göre azalan sırada sıralar ve toplam izleyicinin durumu,% N ' nin en kötü durumu olarak hesaplanır (N, *Statethresholdpercentage*yapılandırma parametresi tarafından dikte edilir).

Örneğin, bir kapsayıcı görüntüsünün beş kapsayıcı örneği olduğunu ve bireysel durumlarının **kritik**, **Uyarı**, **sağlıklı**, **sağlıklı**, **sağlıklı**olduğunu varsayalım.  Kapsayıcıların %90 en kötü durumu azalan önem derecesine göre **sıralandığında, KAPSAYıCı** CPU kullanımı Izleyicisinin durumu **kritik**olur.

## <a name="understand-the-monitoring-configuration"></a>İzleme yapılandırmasını anlama

Kapsayıcılar için Azure Izleyici, aşağıdaki gibi yapılandırılmış bir dizi anahtar izleme senaryosu içerir.

### <a name="unit-monitors"></a>Birim izleyicileri

|**İzleyici adı** | İzleyici türü | **Açıklama** | **Parametre** | **Değer** |
|-----------------|--------------|-----------------|---------------|-----------|
|Düğüm bellek kullanımı |Birim izleyicisi |Bu izleyici, cadvizörü raporlanan verileri kullanarak bir düğümün bellek kullanımını dakikada bir değerlendirir. |ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|Düğüm CPU kullanımı |Birim Izleyicisi |Bu izleyici, cadvizörü tarafından bildirilen verileri kullanarak her dakikada düğümün CPU kullanımını denetler. | ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|Düğüm durumu |Birim izleyicisi |Bu izleyici, Kubernetes tarafından bildirilen düğüm koşullarını denetler.<br> Şu anda şu düğüm koşulları denetlenir: disk baskısı, bellek baskısı, PID baskısı, disk dışı, Ağ kullanılamıyor, düğüm için hazır durum.<br> Yukarıdaki koşulların dışında, *disk* veya *ağ kullanılamaz* **durumdaysa, izleyici** **kritik** duruma geçer.<br> Başka herhangi bir koşul varsa **, bu,** **Ready** durumu dışında, izleyici **Uyarı** durumuna geçer. | NodeConditionTypeForFailedState | outofdisk, networkunavailable ||
|Kapsayıcı bellek kullanımı |Birim izleyicisi |Bu izleyici, kapsayıcının örneklerinin bellek kullanımının (RSS) Birleşik sistem durumunu bildirir.<br> Her örneği tek bir eşikle karşılaştıran ve **ConsecutiveSamplesForStateTransition**yapılandırma parametresiyle belirtilen basit bir karşılaştırma gerçekleştirir.<br> Bu durum, kapsayıcının sistem durumunun önem derecesine (yani kritik, uyarı, sağlıklı) göre azalan düzende sıralanmış kapsayıcı (StateThresholdPercentage) örneklerinin %90 ' inin en kötü durumu olarak hesaplanır.<br> Bir kapsayıcı örneğinden bir kayıt alınmadığında, kapsayıcı örneğinin sistem durumu **bilinmiyor**olarak bildirilir ve sıralama düzeninde **kritik** durum üzerinde daha yüksek önceliğe sahiptir.<br> Her kapsayıcı örneğinin durumu, yapılandırmada belirtilen eşikler kullanılarak hesaplanır. Kullanım kritik eşiğin üzerindeyse (%90%), örnek kritik eşikten küçükse örnek **kritik** bir durumdaydı (%90) Ancak uyarı eşiğinin (%80%) daha büyük olması durumunda örnek bir **Uyarı** durumundadır. Aksi takdirde, **sağlıklı** durumda olur. |ConsecutiveSamplesForStateTransition<br> Failiflessıof yüzdesi<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|Kapsayıcı CPU kullanımı |Birim izleyicisi |Bu izleyici, kapsayıcının örneklerinin CPU kullanımının Birleşik sistem durumunu bildirir.<br> Her örneği tek bir eşikle karşılaştıran ve **ConsecutiveSamplesForStateTransition**yapılandırma parametresiyle belirtilen basit bir karşılaştırma gerçekleştirir.<br> Bu durum, kapsayıcının sistem durumunun önem derecesine (yani kritik, uyarı, sağlıklı) göre azalan düzende sıralanmış kapsayıcı (StateThresholdPercentage) örneklerinin %90 ' inin en kötü durumu olarak hesaplanır.<br> Bir kapsayıcı örneğinden bir kayıt alınmadığında, kapsayıcı örneğinin sistem durumu **bilinmiyor**olarak bildirilir ve sıralama düzeninde **kritik** durum üzerinde daha yüksek önceliğe sahiptir.<br> Her kapsayıcı örneğinin durumu, yapılandırmada belirtilen eşikler kullanılarak hesaplanır. Kullanım kritik eşiğin üzerindeyse (%90%), örnek kritik eşikten küçükse örnek **kritik** bir durumdaydı (%90) Ancak uyarı eşiğinin (%80%) daha büyük olması durumunda örnek bir **Uyarı** durumundadır. Aksi takdirde, **sağlıklı** durumda olur. |ConsecutiveSamplesForStateTransition<br> Failiflessıof yüzdesi<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|Sistem iş yükü Pod hazırlanıyor |Birim izleyicisi |Bu izleyici, belirli bir iş yükünde yer alan Pod yüzdesine göre durumu raporlar. Yük %100 ' den daha **az bir durumda** ise, durumu **kritik** olarak ayarlanır |ConsecutiveSamplesForStateTransition<br> Failiflessıof yüzdesi |2<br> 100 ||
|Kuto API durumu |Birim izleyicisi |Bu izleyici, kuin API hizmetinin durumunu raporlar. Kume API uç noktasının kullanılamadığı durumlarda İzleyici kritik bir durumda. Bu izleyici için, bu durum, kuin API sunucusu için ' düğümler ' uç noktasına bir sorgu yapılarak belirlenir. Tamam yanıt kodu dışında bir şey izleyiciyi **kritik** bir duruma geçirir. | Yapılandırma özelliği yok |||

### <a name="aggregate-monitors"></a>Toplam izleyiciler

|**İzleyici adı** | **Açıklama** | **Algoritma** |
|-----------------|-----------------|---------------|
|Düğüm |Bu izleyici, tüm düğüm izlemelerinin bir toplamasından oluşur. En kötü sistem durumu ile alt izleyicinin durumuyla eşleşir:<br> Düğüm CPU kullanımı<br> Düğüm bellek kullanımı<br> Düğüm durumu | En kötü|
|Düğüm havuzu |Bu izleyici, düğüm havuzu *agentpool*içindeki tüm düğümlerin Birleşik sistem durumunu bildirir. Bu üç durum izleyicisine sahiptir ve bu durum, düğüm havuzundaki düğümlerin %80 ' üne ait olan, düğüm durumlarının önem derecesine göre azalan düzende (yani, kritik, uyarı, sağlıklı) sıralanmıştır.|Yüzde |
|Düğümler (düğüm havuzunun üst öğesi) |Bu, tüm düğüm havuzlarının bir toplam izleyicisine sahiptir. Durumu, alt izlemelerinin (yani kümede bulunan düğüm havuzlarının) en kötü durumuna bağlıdır. |En kötü |
|Küme (düğümlerin üst öğesi/<br> Kubernetes altyapısı) |Bu, Kubernetes altyapısı ve düğümleri olan en kötü sistem durumu ile alt izleyicinin durumuyla eşleşen ana izleyicidir. |En kötü |
|Kubernetes altyapısı |Bu izleyici, kümenin yönetilen altyapı bileşenlerinin Birleşik sistem durumunu bildirir. durumu, kendi alt izleme durumları olan ' en kötü ', kuas sistem iş yükleri ve API sunucusu durumu olarak hesaplanır. |En kötü|
|Sistem iş yükü |Bu izleyici, bir KUIN sistem iş yükünün sistem durumunu bildirir. Bu izleyici, en kötü sistem durumu ile alt izleyicinin durumuyla eşleşir, bu, **Ready durum** izleyicisinde ve iş yükünde yer alan kapsayıcılardır. |En kötü |
|Kapsayıcı |Bu izleyici, belirli bir iş yükünde bir kapsayıcının genel sistem durumunu bildirir. Bu izleyici, **CPU kullanımı** ve **bellek kullanımı** izleyicileri olan en kötü sistem durumu ile alt izleyicinin durumuyla eşleşir. |En kötü |

## <a name="next-steps"></a>Sonraki adımlar

Kubernetes kümenizin sistem durumunu görüntüleme hakkında bilgi edinmek için [izleyici küme durumunu](container-insights-health.md) görüntüleyin.