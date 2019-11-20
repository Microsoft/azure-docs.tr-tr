---
title: Service Fabric kümesi kapasitesini planlama | Microsoft Docs
description: Service Fabric küme kapasitesi planlama konuları. Nodetypes, Işlemler, dayanıklılık ve güvenilirlik katmanları
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: chackdan
editor: ''
ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/09/2019
ms.author: pepogors
ms.openlocfilehash: 1cbbc1fde22262d5841766978d40487f812e0963
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333117"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Service Fabric küme kapasitesi planlama konuları
Herhangi bir üretim dağıtımı için kapasite planlaması önemli bir adımdır. Bu işlemin bir parçası olarak göz önünde bulundurmanız gereken öğelerden bazıları aşağıda verilmiştir.

* Kümenizin başlamak için gereken düğüm türlerinin sayısı
* Her düğüm türünün özellikleri (boyut, birincil, internet 'e yönelik, VM sayısı vb.)
* Kümenin güvenilirlik ve dayanıklılık özellikleri

> [!NOTE]
> Planlama sırasında **Izin verilmeyen** tüm yükseltme ilkesi değerlerini en düşük düzeyde gözden geçirmeniz gerekir. Bu, değerleri uygun şekilde ayarlamış ve değiştirilemeyen sistem yapılandırma ayarları nedeniyle daha sonra kümenizin üzerine yazmayı azalttığınızdan emin olmak için kullanılır. 
> 

Bu öğelerin her birini kısaca gözden geçirmemize izin verin.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Kümenizin başlamak için gereken düğüm türlerinin sayısı
İlk olarak, oluşturduğunuz kümenin ne için kullanılacağını belirlemeniz gerekir.  Bu kümeye dağıtmayı planladığınız uygulama türleri nelerdir? Kümenin amacına açık değilseniz, büyük olasılıkla kapasite planlama işlemini girmeye henüz hazırlamadınız.

Kümenizin başlamak için gereken düğüm türü sayısını oluşturun.  Her düğüm türü bir sanal makine ölçek kümesi ile eşleştirilir. Daha sonra, her düğüm türünün ölçeği birbirinden bağımsız olarak artırılabilir veya azaltılabilir, her düğüm türünde farklı bağlantı noktası kümeleri açık olabilir ve farklı kapasite ölçümleri yapılabilir. Bu nedenle, düğüm türlerinin sayısı temel olarak aşağıdaki noktalara gelir:

* Uygulamanızda birden fazla hizmet var mı ve bunların herkese açık veya internet 'e yönelik olması gerekiyor mu? Tipik uygulamalar, bir istemciden giriş ve ön uç hizmetleriyle iletişim kuran bir veya daha fazla arka uç hizmeti içeren bir ön uç ağ geçidi hizmeti içerir. Bu durumda, en az iki düğüm türüne sahip olursunuz.
* Hizmetlerinizin (uygulamanızı oluşturan) daha büyük RAM veya daha yüksek CPU döngüleri gibi farklı altyapı gereksinimlerine sahip olması gerekir mi? Örneğin, dağıtmak istediğiniz uygulamanın bir ön uç hizmeti ve arka uç hizmeti içerdiğini varsayalım. Ön uç hizmeti, internet 'e açık bağlantı noktaları olan daha küçük VM 'lerde (D2 gibi VM boyutları) çalıştırılabilir.  Ancak arka uç hizmeti, hesaplama için yoğun ve internet 'e yönelik olmayan daha büyük VM 'lerde (D4, D6, D15 gibi VM boyutları ile) çalıştırılması gerekir.
  
  Bu örnekte, tüm hizmetleri tek bir düğüm türüne koymaya karar verseniz de, bunları iki düğüm türüne sahip bir kümeye yerleştirmeniz önerilir.  Bu, her düğüm türünün internet bağlantısı veya VM boyutu gibi farklı özelliklere sahip olmasına olanak sağlar. VM 'lerin sayısı bağımsız olarak ölçeklendirilebilir.  
* Gelecekte tahmin edemediğinizde, bildiğiniz olgularla ilerleyin ve uygulamalarınızın başlaması gereken düğüm türü sayısını seçin. Düğüm türlerini dilediğiniz zaman daha sonra ekleyebilir veya kaldırabilirsiniz. Service Fabric kümesi en az bir düğüm türüne sahip olmalıdır.

## <a name="the-properties-of-each-node-type"></a>Her düğüm türünün özellikleri
**Düğüm türü** Cloud Services rollerle eşdeğer olarak görülebilir. Düğüm türleri, VM boyutlarını, VM sayısını ve bunların özelliklerini tanımlar. Bir Service Fabric kümesinde tanımlanan her düğüm türü, bir [sanal makine ölçek kümesi](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)ile eşlenir.  
Her düğüm türü ayrı bir ölçek kümesidir ve bağımsız olarak yukarı veya aşağı ölçeklendirilebilir, farklı bağlantı noktası kümelerine sahip olabilir ve farklı kapasite ölçümlerine sahip olabilir. Düğüm türleri ve sanal makine ölçek kümeleri arasındaki ilişkiler hakkında daha fazla bilgi için, örneklerden birine RDP ekleyin, yeni bağlantı noktalarını açma ve benzeri, bkz. [küme düğümü türleri Service Fabric](service-fabric-cluster-nodetypes.md).

Service Fabric kümesi, birden fazla düğüm türünden oluşabilir. Bu olayda, küme bir birincil düğüm türünden ve bir veya daha fazla birincil olmayan düğüm türünden oluşur.

Tek düğümlü bir tür, SF uygulamaları için sanal makine ölçek kümesi başına 100 düğümden daha güvenli bir şekilde ölçeklendirilemez; 100 düğümden daha güvenilir bir şekilde elde etmek, ek sanal makine ölçek kümeleri eklemenizi gerektirir.

### <a name="primary-node-type"></a>Birincil düğüm türü

Service Fabric sistem hizmetleri (örneğin, Küme Yöneticisi hizmeti veya Görüntü Deposu hizmeti), birincil düğüm türüne yerleştirilir. 

![İki düğüm türüne sahip bir kümenin ekran görüntüsü][SystemServices]

* Birincil düğüm türü için **En düşük sanal makine boyutu** , seçtiğiniz **dayanıklılık katmanına** göre belirlenir. Varsayılan dayanıklılık katmanı bronz. Daha fazla ayrıntı için [kümenin dayanıklılık özelliklerine](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) bakın.  
* Birincil düğüm türü için **en az sanal makine sayısı** , seçtiğiniz **güvenilirlik katmanına** göre belirlenir. Varsayılan güvenilirlik katmanı gümüş olur. Daha fazla ayrıntı için [kümenin güvenilirlik özelliklerine](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) bakın.  

Azure Resource Manager şablondan, birincil düğüm türü, [düğüm türü tanımı](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object)altında `isPrimary` özniteliğiyle yapılandırılır.

### <a name="non-primary-node-type"></a>Birincil olmayan düğüm türü

Birden çok düğüm türüne sahip bir kümede, bir birincil düğüm türü vardır ve geri kalan birincil olmayan bir kümedir.

* Birincil olmayan düğüm türleri için **En düşük VM boyutu** , seçtiğiniz **dayanıklılık katmanına** göre belirlenir. Varsayılan dayanıklılık katmanı bronz. Daha fazla bilgi için, [kümenin dayanıklılık özelliklerine](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)bakın.  
* Birincil olmayan düğüm türleri için **En düşük VM sayısı** bir. Ancak bu numarayı, bu düğüm türünde çalıştırmak istediğiniz uygulama/hizmetlerin çoğaltmaları sayısına göre seçmeniz gerekir. Bir düğüm türündeki sanal makine sayısı, kümeyi dağıttıktan sonra artırılabilir.

## <a name="the-durability-characteristics-of-the-cluster"></a>Kümenin dayanıklılık özellikleri
Dayanıklılık katmanı, sistem sanal makinelerinizin temel alınan Azure altyapısıyla sahip olduğu ayrıcalıkları göstermek için kullanılır. Bu ayrıcalık, birincil düğüm türünde, sistem hizmetleri ve durum bilgisi olan hizmetleriniz için çekirdek gereksinimlerini etkileyen tüm VM düzeyi altyapı isteklerini (VM yeniden başlatma, VM yeniden görüntüsü veya VM geçişi gibi) duraklatmaya olanak Service Fabric tanır. Birincil olmayan düğüm türlerinde, bu ayrıcalık, durum bilgisi olan hizmetlerinizin çekirdek gereksinimlerini etkileyen tüm VM düzeyi altyapı isteklerini (VM yeniden başlatma, VM yeniden görüntüsü ve VM geçişi gibi) duraklatmaya Service Fabric olanak tanır.

| Dayanıklılık katmanı  | Gerekli en az sayıda VM | Desteklenen VM SKU 'Ları                                                                  | Sanal makine ölçek kümesinde yaptığınız güncelleştirmeler                               | Azure tarafından başlatılan güncelleştirmeler ve bakım                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Gold             | 5                              | Tek bir müşteriye adanmış tam düğümlü SKU 'Lar (örneğin, L32s, GS5, G5, DS15_v2, D15_v2) | Service Fabric kümesi tarafından onaylanana kadar ertelenebilir | Çoğaltmaların daha önceki hatalardan kurtarılmasına imkan tanımak için UD başına 2 saat boyunca duraklatılabilir |
| Silver           | 5                              | En az 50 GB yerel SSD ile tek çekirdekli veya yukarıdaki VM 'Ler                      | Service Fabric kümesi tarafından onaylanana kadar ertelenebilir | Herhangi bir önemli süre geciktirelemez                                                    |
| Bronz           | 1                              | En az 50 GB yerel SSD içeren VM 'Ler                                              | Service Fabric kümesi tarafından gecikmeyecek           | Herhangi bir önemli süre geciktirelemez                                                    |

> [!WARNING]
> Bronz dayanıklılık ile çalışan düğüm türleri _hiçbir ayrıcalık_alamadı. Bu, durum bilgisi olan iş yüklerinizi etkileyen altyapı işlerinin durdurulmayacağı veya gecikmeyeceği anlamına gelir ve bu da iş yüklerinizi etkileyebilir. Yalnızca durum bilgisiz iş yüklerini çalıştıran düğüm türleri için bronz kullanın. Üretim iş yükleri için gümüş veya üzeri çalıştırmanın kullanılması önerilir. 
> 
> Dayanıklılık düzeyinden bağımsız olarak, VM Ölçek kümesindeki [ayırmayı kaldırma](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/deallocate) işlemi kümeyi yok eder

**Gümüş veya altın dayanıklılık düzeylerini kullanmanın avantajları**
 
- Bir ölçeklendirme işleminde gerekli adımların sayısını azaltır (yani, Node devre dışı bırakma ve Remove-ServiceFabricNodeState otomatik olarak çağrılır).
- Müşteri tarafından başlatılan yerinde VM SKU değişiklik işlemi veya Azure altyapı işlemleri nedeniyle veri kaybı riskini azaltır.

**Gümüş veya altın dayanıklılık düzeylerini kullanmanın dezavantajları**
 
- Sanal makine ölçek kümenize ve diğer ilgili Azure kaynaklarına yapılan dağıtımlar geciktirilebilir, zaman aşımına uğrar veya kümenizde veya altyapı düzeyinde tamamen engellenebilir. 
- Azure altyapı işlemleri sırasında otomatik düğüm etkinleştirmeleri nedeniyle [çoğaltma yaşam döngüsü olaylarının](service-fabric-reliable-services-lifecycle.md) (örneğin, birincil takas) sayısını artırır.
- Azure platformu yazılım güncelleştirmeleri veya donanım bakım etkinlikleri gerçekleşirken, düğümleri hizmet dışı bir süre alır. Bu etkinlikler sırasında durumu devre dışı bırakma/devre dışı bırakılmış düğümleri görebilirsiniz. Bu, kümenizin kapasitesini geçici olarak azaltır, ancak kümenizin veya uygulamalarınızın kullanılabilirliğini etkilememelidir.

### <a name="recommendations-for-when-to-use-silver-or-gold-durability-levels"></a>Gümüş veya altın dayanıklılık düzeylerinin ne zaman kullanılacağı ile ilgili öneriler

Düzenli olmayan hizmetleri barındıran tüm düğüm türleri için gümüş veya altın dayanıklılık kullanın (VM örneği sayısını azaltır), sık sık ve bu ölçek basitleştirilmesi için bu dağıtım işlemlerinin gecikilmesi ve kapasitesinin azaltılmasını tercih etmeniz önerilir operasyonları. Genişleme senaryoları (VM örnekleri ekleme), dayanıklılık katmanı seçiminize değil, yalnızca ölçeği Ölçeklendir.

### <a name="changing-durability-levels"></a>Dayanıklılık düzeylerini değiştirme
- Gümüş veya altın dayanıklılık düzeylerine sahip düğüm türleri bronz olarak düşürülemez.
- Bronz veya altın güze 'a yükseltme birkaç saat sürebilir.
- Dayanıklılık düzeyini değiştirirken, sanal makine ölçek kümesi kaynağınızın Service Fabric uzantısı yapılandırmasında ve Service Fabric küme kaynağınızın düğüm türü tanımında güncelleştirdiğinizden emin olun. Bu değerlerin eşleşmesi gerekir.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Gümüş veya altın dayanıklılık düzeyine ayarladığınız düğüm türü için işletimsel öneriler.

- Kümenizi ve uygulamalarınızı her zaman sağlıklı tutun ve uygulamaların tüm [hizmet çoğaltması yaşam döngüsü olaylarına](service-fabric-reliable-services-lifecycle.md) (derleme içindeki çoğaltma gibi) zamanında yanıt vermesini sağlayın.
- VM SKU değişikliği yapmak için daha güvenli yollar benimseyin (ölçeği artırma/azaltma): bir sanal makine ölçek kümesinin VM SKU 'sunu değiştirmek için birkaç adım ve dikkat edilmesi gerekir. Yaygın sorunlardan kaçınmak için izleyebileceğiniz süreç aşağıda verilmiştir.
    - **Birincil olmayan düğüm türleri için:** Yeni sanal makine ölçek kümesi oluşturmanız önerilir, hizmet yerleştirme kısıtlamasını yeni sanal makine ölçek kümesi/düğüm türünü içerecek şekilde değiştirin ve ardından eski sanal makine ölçek kümesi örnek sayısını sıfıra, her seferinde bir düğüm olacak şekilde azaltabilirsiniz (Bunun yapılması gerekir düğümlerin kaldırılması kümenin güvenilirliğini etkilemediğinden emin olun).
    - **Birincil düğüm türü için:** Seçtiğiniz VM SKU 'SU kapasitese ve daha büyük bir VM SKU 'SU olarak değiştirmek istiyorsanız, [birincil düğüm türü için dikey ölçeklendirmeyle](https://docs.microsoft.com/azure/service-fabric/service-fabric-scale-up-node-type)ilgili kılavuzumuzu izleyin. 

- Dayanıklılık düzeyi Gold veya gümüş etkin olan herhangi bir sanal makine ölçek kümesi için en az beş düğüm sayısını koruyun.
- Dayanıklılık düzeyi gümüş veya altın sahip her sanal makine ölçek kümesi, Service Fabric kümesinde kendi düğüm türüne eşlenmelidir. Birden çok sanal makine ölçek kümesini tek bir düğüm türüyle eşlemek, Service Fabric kümesi ile Azure altyapısının düzgün çalışmasını engeller.
- Rastgele VM örneklerini silmeyin, her zaman sanal makine ölçek kümesi ölçek azaltma özelliğini kullanın. Rastgele sanal makine örneklerinin silinmesi, sanal makine örneğinde UD ve FD içinde yer aldığı bir dengesbakiyinin oluşturulmasına bir potansiyeline sahiptir. Bu dengesizliği, sistem hizmet örnekleri/hizmet çoğaltmaları arasında doğru şekilde yük dengelemesi yapabilme yeteneğini olumsuz etkileyebilir.
- Otomatik ölçeklendirme kullanıyorsanız, içindeki ölçek (sanal makine örneklerinin kaldırılması) gibi kuralları tek seferde yalnızca bir düğüm olarak ayarlayın. Aynı anda birden fazla örneğin ölçeğini azaltma güvenli değildir.
- Birincil düğüm türünde VM 'Leri silmek veya ayırmayı geri alıyorsa, güvenilirlik katmanının gerektirdiği ayrılmış VM 'lerin sayısını asla azaltmalısınız. Bu işlemler, bir ölçek kümesinde sonsuz veya altın bir dayanıklılık düzeyine kadar süresiz olarak engellenir.

## <a name="the-reliability-characteristics-of-the-cluster"></a>Kümenin güvenilirlik özellikleri
Güvenilirlik katmanı, birincil düğüm türünde bu kümede çalıştırmak istediğiniz sistem hizmetlerinin çoğaltmaları sayısını ayarlamak için kullanılır. Kopyaların ne kadar fazla olması durumunda, sistem hizmetleri kümenizde daha güvenilir.  

Güvenilirlik katmanı aşağıdaki değerleri alabilir:

* Platin bir hedef çoğaltma kümesi sayısı dokuz olan sistem hizmetlerini çalıştırın
* Gold-sistem hizmetlerini bir hedef çoğaltma kümesi sayısı yedi ile çalıştırın
* Gümüş-sistem hizmetlerini, hedef çoğaltma kümesi sayısı beş ile çalıştırın 
* Bronz-sistem hizmetlerini bir hedef çoğaltma kümesi sayısını üç ile çalıştırın

> [!NOTE]
> Seçtiğiniz güvenilirlik katmanı, birincil düğüm türünde olması gereken en az düğüm sayısını belirler. 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>Güvenilirlik katmanı için öneriler

Kümenizin boyutunu (tüm düğüm türlerindeki sanal makine örneklerinin toplamı) artırdığınızda veya azaltırsanız, kümenizin bir katmandan diğerine güvenilirliğini güncelleştirmeniz gerekir. Bunun yapılması, Sistem Hizmetleri çoğaltma kümesi sayısını değiştirmek için gereken küme yükseltmelerini tetikler. Kümede düğüm ekleme gibi başka herhangi bir değişiklik yapmadan önce yükseltme işleminin tamamlanmasını bekleyin.  Service Fabric Explorer veya [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps) komutunu çalıştırarak yükseltmenin ilerlemesini izleyebilirsiniz

Güvenilirlik katmanını seçme önerisi aşağıda verilmiştir.  Çekirdek düğümlerin sayısı, bir güvenilirlik katmanı için en düşük düğüm sayısına de ayarlanır.  Örneğin, altın güvenilirliğe sahip bir küme için 7 çekirdek düğümü vardır.

| **Küme düğümlerinin sayısı** | **Güvenilirlik katmanı** |
| --- | --- |
| 1 |Güvenilirlik katmanı parametresini belirtmeyin, sistem bunu hesaplar |
| 3 |Bronz |
| 5 veya 6|Silver |
| 7 veya 8 |Gold |
| 9 ve yukarı |PLA |

## <a name="primary-node-type---capacity-guidance"></a>Birincil düğüm türü-kapasite Kılavuzu

Birincil düğüm türü kapasitesini planlamaya yönelik rehberlik aşağıda verilmiştir:

- **Azure 'da herhangi bir üretim iş yükünü çalıştırmak için sanal makine örneği sayısı:** Minimum birincil düğüm türü boyutu olarak 5 ve bir gümüş güvenilirlik katmanı belirtmeniz gerekir.  
- **Azure 'da test iş yüklerini çalıştırmak için sanal makine örneği sayısı** En az 1 veya 3 olan bir birincil düğüm türü boyutu belirtebilirsiniz. Tek düğümlü küme, özel bir yapılandırmayla çalışır ve bu nedenle, bu kümeden ölçek genişletme desteklenmez. Bir düğüm kümesinin güvenilirliği yoktur ve bu nedenle Kaynak Yöneticisi şablonunuzda, bu yapılandırmanın (yapılandırma değerini Ayarlamasız) kaldırılması/belirtmemesi gerekir. Portal üzerinden ayarlanmış bir düğüm kümesi ayarlarsanız, yapılandırma otomatik olarak ele alınır. Üretim iş yüklerini çalıştırmak için bir ve üç düğüm kümesi desteklenmez. 
- **VM SKU 'su:** Birincil düğüm türü, sistem hizmetlerinin çalıştığı yerdir. bu nedenle, seçtiğiniz VM SKU 'SU kümeye yerleştirmeyi planladığınız genel yükün dikkate alınmalıdır. Burada, "Dug 'ler" olarak birincil düğüm türünü düşündüklerini göstermek için bir benzerleme vurguladı, betiğinizi oxtagen sağlar ve bu nedenle, beciciizin yeterli sayıda oxyıg yoksa gövdeleriniz yeterlidir. 

Kümenin kapasite ihtiyaçları kümede çalıştırmayı planladığınız iş yüküne göre belirlendiğinden, size özel iş yükünüz için size nitel Kılavuzu sağlayamıyoruz, ancak size başlamanıza yardımcı olacak geniş bir kılavuz aşağıda verilmiştir

Üretim iş yükleri için: 

- Kümelerinizi birincil NodeType sistem hizmetlerine ayırmanız önerilir ve uygulamanızı ikincil NodeTypes 'a dağıtmak için yerleştirme kısıtlamalarını kullanın.
- Önerilen VM SKU 'SU standart D2_V2 veya en az 50 GB yerel SSD ile eşdeğerdir.
- Desteklenen minimum Use VM SKU 'SU Standard_D2_V3 veya standart D1_V2 ya da en az 50 GB yerel SSD ile eşdeğerdir. 
- Önerimiz en az 50 GB 'dir. İş yükleriniz için özellikle Windows kapsayıcıları çalıştırırken daha büyük diskler gerekir. 
- Standart a0 gibi kısmi çekirdek VM SKU 'Ları üretim iş yükleri için desteklenmez.
- Bir seri VM SKU 'Ları, performans nedenleriyle üretim iş yükleri için desteklenmez.
- Düşük öncelikli VM 'Ler desteklenmez.

> [!WARNING]
> Çalışan bir kümede birincil düğüm VM SKU 'SU boyutunu değiştirme, bir ölçeklendirme işlemidir ve [sanal makine ölçek kümesi ölçek](virtual-machine-scale-set-scale-node-type-scale-out.md) genişletme belgelerinde belgelenmiştir.

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Durum bilgisi olan iş yükleri için birincil olmayan düğüm türü-kapasite Kılavuzu

Bu kılavuz, Service Fabric güvenilir koleksiyonlarını veya birincil olmayan düğüm türünde çalıştırdığınız [güvenilir aktörleri](service-fabric-choose-framework.md) kullanan durum bilgisi olan iş yükleri içindir.

**Sanal makine örneği sayısı:** Durum bilgisi olan üretim iş yükleri için, en az ve hedef çoğaltma sayısı olan 5 ' i çalıştırmanız önerilir. Bu, düzenli bir durumda her hata etki alanındaki bir çoğaltma (bir çoğaltma kümesinden) ve etki alanı yükseltme ile sona erdirmek anlamına gelir. Birincil düğüm türü için tüm güvenilirlik katmanı kavramı, sistem hizmetleri için bu ayarı belirtmenin bir yoludur. Bu nedenle, durum bilgisi olan hizmetleriniz için de aynı dikkat geçerlidir.

Bu nedenle, üretim iş yükleri için, üzerinde durum bilgisi olan iş yükleri çalıştırıyorsanız, en az önerilen birincil olmayan düğüm türü boyutu 5 ' tir.

**VM SKU 'su:** Bu, uygulama hizmetlerinizin çalıştığı düğüm türüdür, bu nedenle, seçtiğiniz VM SKU 'SU her bir düğüme yerleştirmeyi planladığınız en yüksek yükü hesaba almalıdır. Düğüm türünün kapasite ihtiyaçları, kümede çalıştırmayı planladığınız iş yüküne göre belirlenir, bu nedenle size özel iş yükünüz için size nitel Kılavuzu sağlamamız, ancak size başlamanıza yardımcı olacak geniş bir kılavuz aşağıda verilmiştir

Üretim iş yükleri için 

- Önerilen VM SKU 'SU standart D2_V2 veya en az 50 GB yerel SSD ile eşdeğerdir.
- Desteklenen minimum Use VM SKU 'SU Standard_D2_V3 veya standart D1_V2 ya da en az 50 GB yerel SSD ile eşdeğerdir. 
- Standart a0 gibi kısmi çekirdek VM SKU 'Ları üretim iş yükleri için desteklenmez.
- Bir seri VM SKU 'Ları, performans nedenleriyle üretim iş yükleri için desteklenmez.

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Birincil olmayan düğüm türü-durum bilgisiz iş yükleri için kapasite Kılavuzu

Birincil olmayan düğüm türünde çalıştırdığınız, durum bilgisiz Iş yüklerinin bu kılavuzu.

**Sanal makine örneği sayısı:** Durum bilgisi olmayan üretim iş yükleri için, en düşük desteklenen birincil düğüm türü boyutu 2 ' dir. Bu, uygulamanızın iki durum bilgisi olmayan örneğini çalıştırmanızı ve hizmetinizin bir VM örneği kaybını sürdürmesine olanak tanır. 

**VM SKU 'su:** Bu, uygulama hizmetlerinizin çalıştığı düğüm türüdür, bu nedenle, seçtiğiniz VM SKU 'SU her bir düğüme yerleştirmeyi planladığınız en yüksek yükü hesaba almalıdır. Düğüm türünün kapasite ihtiyaçları, kümede çalıştırmayı planladığınız iş yüküne göre belirlenir. Size özel iş yükünüz için size nitel Kılavuzu sağlayamıyoruz.  Ancak, başlamanıza yardımcı olacak kapsamlı bir kılavuz aşağıda verilmiştir.

Üretim iş yükleri için 

- Önerilen VM SKU 'SU standart D2_V2 veya eşdeğerdir. 
- Desteklenen minimum Use VM SKU 'SU standart D1 veya standart D1_V2 veya eşdeğerdir. 
- Standart a0 gibi kısmi çekirdek VM SKU 'Ları üretim iş yükleri için desteklenmez.
- Bir seri VM SKU 'Ları, performans nedenleriyle üretim iş yükleri için desteklenmez.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Sonraki adımlar
Kapasite planlamayı tamamladıktan ve bir küme ayarladıktan sonra aşağıdakileri okuyun:

* [Service Fabric küme güvenliği](service-fabric-cluster-security.md)
* [Service Fabric kümesi ölçeklendirme](service-fabric-cluster-scaling.md)
* [Olağanüstü durum kurtarma planlaması](service-fabric-disaster-recovery.md)
* [Nodetypes ile sanal makine ölçek kümesi arasındaki ilişki](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
