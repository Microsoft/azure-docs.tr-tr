---
title: Service Fabric küme kapasitesi planlama konuları
description: Service Fabric kümenizi planlarken dikkate alınması gereken düğüm türleri, dayanıklılık, güvenilirlik ve diğer şeyler.
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: pepogors
ms.openlocfilehash: 9268dfef15d8302eb31cc1b649c7fd713aab6721
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732593"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Service Fabric küme kapasitesi planlama konuları

Küme kapasitesi planlaması her Service Fabric üretim ortamı için önemlidir. Önemli konular şunlardır:

* **Küme *düğümü türlerinin* ilk sayısı ve özellikleri**

* Azure altyapısında Service Fabric VM ayrıcalıklarını belirleyen **her düğüm türünün *dayanıklılık* düzeyi**

* Service Fabric sistem hizmetleri ve genel küme işlevinin kararlılığını belirleyen, **kümenin *güvenilirlik* düzeyi**

Bu makale, bu alanların her biri için önemli karar noktalarında size kılavuzluk eder.

## <a name="initial-number-and-properties-of-cluster-node-types"></a>Küme düğümü türlerinin ilk sayısı ve özellikleri

*Düğüm türü* , kümedeki bir dizi düğüm (sanal makine) için boyut, sayı ve özellikleri tanımlar. Bir Service Fabric kümesinde tanımlanan her düğüm türü, bir [sanal makine ölçek kümesi](../virtual-machine-scale-sets/overview.md)ile eşlenir.

Her düğüm türü ayrı bir ölçek kümesi olduğundan, bağımsız olarak yukarı veya aşağı ölçeklendirilebilir, farklı bağlantı noktası kümelerine açık olabilir ve farklı kapasite ölçümleri vardır. Düğüm türleri ve sanal makine ölçek kümeleri arasındaki ilişki hakkında daha fazla bilgi için bkz. [küme düğümü türleri Service Fabric](service-fabric-cluster-nodetypes.md).

Her küme, Service Fabric platform özellikleri sağlayan kritik sistem hizmetlerini çalıştıran bir **birincil düğüm türü** gerektirir. Uygulamalarınızı çalıştırmak için birincil düğüm türlerini de kullanmak mümkün olsa da, bunları yalnızca sistem hizmetlerini çalıştırmaya ayırmak önerilir.

**Birincil olmayan düğüm türleri** , uygulama rollerini ( *ön uç* ve *arka uç* hizmetleri gibi) tanımlamak ve bir küme içindeki hizmetleri fiziksel olarak yalıtmak için kullanılabilir. Service Fabric kümeler, birincil olmayan sıfır veya daha fazla düğüm türüne sahip olabilir.

Birincil düğüm türü, `isPrimary` Azure Resource Manager dağıtım şablonundaki düğüm türü tanımı altındaki özniteliği kullanılarak yapılandırılır. Düğüm türü özelliklerinin tam listesi için [Nodetypedescription nesnesine](/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object) bakın. Örneğin kullanım için, [Service Fabric küme örneklerindeki](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/) dosya *AzureDeploy.js* açın ve nesne arama *sayfasında bulun* `nodeTypes` .

### <a name="node-type-planning-considerations"></a>Düğüm türü planlama konuları

İlk düğüm türü sayısı, kümenizin amacına ve üzerinde çalışan uygulama ve hizmetlere bağlıdır. Aşağıdaki soruları göz önünde bulundurun:

* ***Uygulamanızda birden fazla hizmet var mı ve bunların herkese açık veya internet 'e yönelik olması gerekiyor mu?***

    Tipik uygulamalar, bir istemciden girdi alan bir ön uç ağ geçidi hizmeti ve ön uç ve arka uç hizmetleri arasında ayrı bir ağla, ön uç hizmetleriyle iletişim kuran bir veya daha fazla arka uç hizmet içerir. Bu durumlar genellikle üç düğüm türü gerektirir: bir birincil düğüm türü ve birincil olmayan iki düğüm türü (her biri ön ve arka uç hizmeti için).

* ***Uygulamanızı oluşturan hizmetler, daha fazla RAM veya daha yüksek CPU döngüleri gibi farklı altyapı ihtiyaçlarına sahip mi?***

    Genellikle, ön uç hizmeti, internet 'e açık bağlantı noktaları olan daha küçük VM 'lerde (D2 gibi VM boyutları) çalıştırılabilir.  Yoğun şekilde yoğun arka uç hizmetlerinin, internet 'e yönelik olmayan daha büyük VM 'lerde (D4, D6, D15 gibi) çalışması gerekebilir. Bu hizmetler için farklı düğüm türlerini tanımlamak, temel Service Fabric VM 'lerin daha verimli ve güvenli bir şekilde kullanılabilmesini sağlar ve bunları bağımsız olarak ölçeklendirmelerini sağlar. İhtiyaç duyacağınız kaynak miktarını tahmin etme hakkında daha fazla bilgi için bkz. [Service Fabric uygulamalar Için kapasite planlama](service-fabric-capacity-planning.md)

* ***Uygulama hizmetlerinizin 100 düğümden daha fazla ölçeklendirme yapması gerekir mi?***

    Tek düğümlü bir tür, Service Fabric uygulamalar için sanal makine ölçek kümesi başına 100 düğümden daha fazla güvenilir bir şekilde ölçeklendirilemedi. 100 'den fazla düğüm çalıştırmak için ek sanal makine ölçek kümeleri (ve bu nedenle ek düğüm türleri) gerekir.

* ***Kümeniz Kullanılabilirlik Alanları arasında yayılacaktır mi?***

    Service Fabric, belirli bölgelere sabitlenmiş düğüm türlerini dağıtarak, uygulamalarınızın yüksek kullanılabilirliğe sahip olduğundan [kullanılabilirlik alanları](../availability-zones/az-overview.md) yayılmış kümeleri destekler. Kullanılabilirlik Alanları ek düğüm türü planlaması ve minimum gereksinimleri gerektirir. Ayrıntılar için bkz. [kullanılabilirlik alanları arasında yayılan Service Fabric kümelerinin birincil düğüm türü Için önerilen topoloji](service-fabric-cross-availability-zones.md#recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones). 

Kümenizin ilk oluşturması için düğüm türlerinin sayısını ve özelliklerini belirlerken, kümeniz dağıtıldıktan sonra her zaman, birincil olmayan düğüm türlerini ekleyebileceğiniz, değiştirebileceğinizi veya kaldıracağınızı unutmayın. [Birincil düğüm türleri, çalışan kümelerde de değiştirilebilir](service-fabric-scale-up-primary-node-type.md) (ancak, bu işlemler üretim ortamlarında büyük bir planlama ve uyarı gerektirir).

Düğüm türü özelliklerinizi daha iyi bir şekilde dikkate almanız, düğüm türünün VM 'lerinin Azure altyapısında sahip olduğu ayrıcalıkları belirleyen dayanıklılık düzeyidir. Daha önce açıklandığı gibi, düğüm türlerinizin her biri için uygun dayanıklılık katmanını belirlemenize yardımcı olmak üzere kümeniz için seçtiğiniz VM 'lerin boyutunu ve tek tek düğüm türleri için atadığınız örnek sayısını kullanın.

## <a name="durability-characteristics-of-the-cluster"></a>Kümenin dayanıklılık özellikleri

*Dayanıklılık düzeyi* , Service Fabric sanal makinelerinizin temel alınan Azure altyapısına sahip olduğu ayrıcalıkları belirler. Bu ayrıcalık Service Fabric, Service Fabric sistemi hizmetleri ve durum bilgisi olan hizmetlerinizin çekirdek gereksinimlerini etkileyen tüm VM düzeyi altyapı isteklerini (yeniden başlatma, yeniden görüntü veya geçiş gibi) duraklatmasını sağlar.

> [!IMPORTANT]
> Dayanıklılık düzeyi, düğüm türü başına ayarlanır. Hiçbiri belirtilmemişse, *bronz* katman kullanılacaktır, ancak otomatik işletim sistemi yükseltmeleri sağlamaz. Üretim iş yükleri için *gümüş* veya *altın* dayanıklılık önerilir.

Aşağıdaki tabloda Service Fabric dayanıklılık katmanları, gereksinimleri ve uygun tehlikikler listelenmiştir.

| Dayanıklılık katmanı  | Gerekli en az sayıda VM | Desteklenen VM boyutları                                                                  | Sanal makine ölçek kümesinde yaptığınız güncelleştirmeler                               | Azure tarafından başlatılan güncelleştirmeler ve bakım                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Gold             | 5                              | Tek bir müşteriye adanmış tam düğümlü Boyutlar (örneğin, L32s, GS5, G5, DS15_v2, D15_v2) | Service Fabric kümesi tarafından onaylanana kadar ertelenebilir | Kopyaların daha önceki hatalardan kurtarılmasına olanak tanımak için yükseltme etki alanı başına 2 saat boyunca duraklatılabilir |
| Silver           | 5                              | En az 50 GB yerel SSD ile tek çekirdekli veya yukarıdaki VM 'Ler                      | Service Fabric kümesi tarafından onaylanana kadar ertelenebilir | Herhangi bir önemli süre geciktirelemez                                                    |
| Bronz          | 1                              | En az 50 GB yerel SSD içeren VM 'Ler                                              | Service Fabric kümesi tarafından gecikmeyecek           | Herhangi bir önemli süre geciktirelemez                                                    |

> [!NOTE]
> Yukarıda belirtilen en düşük VM sayısı, her dayanıklılık düzeyi için gerekli bir gereksinimdir. Yerinde, bu gereksinimleri karşılamayan var olan sanal makine ölçek kümelerinin oluşturulmasını veya değiştirilmesini önleyen doğrulamalar sunuyoruz.

> [!WARNING]
> Bronz dayanıklılık sayesinde, otomatik işletim sistemi görüntüsü yükseltmesi kullanılamaz. [Düzeltme Eki düzenleme uygulaması](service-fabric-patch-orchestration-application.md) (yalnızca Azure 'da barındırılan kümeler için tasarlanan) gümüş veya daha fazla dayanıklılık düzeyi için *önerilmemekle* birlikte, Windows güncelleştirmelerini Service Fabric yükseltme etki alanlarına göre otomatik hale getirmek için tek seçeneğiniz vardır.

> [!IMPORTANT]
> Dayanıklılık düzeyinden bağımsız olarak, bir sanal makine ölçek kümesi üzerinde [ayırmayı kaldırma](/rest/api/compute/virtualmachinescalesets/deallocate) işlemi çalıştırıldığında küme yok edilir.

### <a name="bronze"></a>Bronz

Bronz dayanıklılık ile çalışan düğüm türleri hiçbir ayrıcalık alamadı. Bu, durum bilgisi olan iş yüklerinizi etkileyen altyapı işlerinin durdurulmayacak veya gecikmeyeceği anlamına gelir. Yalnızca durum bilgisi olmayan iş yüklerini çalıştıran düğüm türleri için bronz dayanıklılık kullanın. Üretim iş yükleri için gümüş veya üzeri çalıştırmanın kullanılması önerilir.

### <a name="silver-and-gold"></a>Gümüş ve altın

Sıklıkla ölçeklendirmeyi düşündüğünüz durum bilgisi olan hizmetleri barındıran tüm düğüm türleri için gümüş veya altın dayanıklılık kullanın ve dağıtım işlemlerinin, işlemi basitleştirmek için ne kadar gecikmesini ve kapasitesini azaltmak istediğinizi unutmayın. Ölçek Genişletme senaryoları, dayanıklılık katmanı seçiminizi etkilememelidir.

#### <a name="advantages"></a>Avantajlar

* Ölçek Genişletme işlemleri için gerekli adımların sayısını azaltır (düğüm devre dışı bırakma ve Remove-ServiceFabricNodeState otomatik olarak çağrılır).
* Yerinde VM boyutu değiştirme işlemleri ve Azure altyapı işlemleri nedeniyle veri kaybı riskini azaltır.

#### <a name="disadvantages"></a>Dezavantajlar

* Sanal makine ölçek kümelerine ve diğer ilgili Azure kaynaklarına yapılan dağıtımlar zaman aşımına uğrar, geciktirilebilir veya kümenizde ya da altyapı düzeyinde sorunları tamamen engellemiş olabilir.
* Azure altyapı işlemleri sırasında otomatik düğüm etkinleştirmeleri nedeniyle [çoğaltma yaşam döngüsü olaylarının](service-fabric-reliable-services-lifecycle.md) (örneğin, birincil takas) sayısını artırır.
* Azure platformu yazılım güncelleştirmeleri veya donanım bakım etkinlikleri gerçekleşirken, düğümleri hizmet dışı bir süre alır. Bu etkinlikler sırasında durumu devre dışı bırakma/devre dışı bırakılmış düğümleri görebilirsiniz. Bu, kümenizin kapasitesini geçici olarak azaltır, ancak kümenizin veya uygulamalarınızın kullanılabilirliğini etkilememelidir.

#### <a name="best-practices-for-silver-and-gold-durability-node-types"></a>Gümüş ve altın dayanıklılık düğümü türleri için en iyi yöntemler

Gümüş veya altın dayanıklılık ile düğüm türlerini yönetmek için bu önerileri izleyin:

* Kümenizi ve uygulamalarınızı her zaman sağlıklı tutun ve uygulamaların tüm [hizmet çoğaltması yaşam döngüsü olaylarına](service-fabric-reliable-services-lifecycle.md) (derleme içindeki çoğaltma gibi) zamanında yanıt vermesini sağlayın.
* VM Boyut değişikliği yapmak için daha güvenli yollar benimseyin (ölçeği artırma/azaltma). Bir sanal makine ölçek kümesinin VM boyutunun değiştirilmesi dikkatli bir planlama ve uyarı gerektirir. Ayrıntılar için bkz. [Service Fabric bir düğüm türünü ölçekleme](service-fabric-scale-up-primary-node-type.md)
* Dayanıklılık düzeyi Gold veya gümüş etkin olan herhangi bir sanal makine ölçek kümesi için en az beş düğüm sayısını koruyun. Bu eşiğin altına ölçeklendirirseniz kümeniz hata durumuna girer ve kaldırılan düğümler için durumu () el ile temizlemeniz gerekir `Remove-ServiceFabricNodeState` .
* Dayanıklılık düzeyi gümüş veya altın sahip her sanal makine ölçek kümesi, Service Fabric kümesinde kendi düğüm türüne eşlenmelidir. Birden çok sanal makine ölçek kümesini tek bir düğüm türüyle eşlemek, Service Fabric kümesi ile Azure altyapısının düzgün çalışmasını engeller.
* Rastgele VM örneklerini silmeyin, her zaman özelliğindeki sanal makine ölçek kümesi ölçeğini kullanın. Rastgele sanal makine örneklerinin silinmesi, sanal makine örneğinde [yükseltme etki alanları](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) ve [hata etki alanları](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains)arasında yer aldığı bir dengesbakiyinin oluşturulmasına neden vardır. Bu dengesizliği, sistem hizmet örnekleri/hizmet çoğaltmaları arasında doğru şekilde yük dengelemesi yapabilme yeteneğini olumsuz etkileyebilir.
* Otomatik ölçeklendirme kullanıyorsanız, (sanal makine örneklerinin kaldırılması) işlemleri aynı anda yalnızca bir düğüm olarak ayarlanır. Tek seferde birden fazla örneğin ölçeklendirilmesi güvenli değildir.
* Birincil düğüm türünde VM 'Leri silmek veya ayırmayı geri alıyorsa, güvenilirlik katmanının gerektirdiği ayrılmış VM 'lerin sayısını hiçbir şekilde azalmayın. Bu işlemler, bir ölçek kümesinde sonsuz veya altın bir dayanıklılık düzeyine kadar süresiz olarak engellenir.

### <a name="changing-durability-levels"></a>Dayanıklılık düzeylerini değiştirme

Belirli kısıtlamalar dahilinde düğüm türü dayanıklılığı düzeyi ayarlanabilir:

* Gümüş veya altın dayanıklılık düzeylerine sahip düğüm türleri bronz olarak düşürülemez.
* Bronz veya altın güze 'a yükseltme birkaç saat sürebilir.
* Dayanıklılık düzeyini değiştirirken, sanal makine ölçek kümesi kaynağınızın Service Fabric uzantısı yapılandırmasında ve Service Fabric küme kaynağınızın düğüm türü tanımında güncelleştirdiğinizden emin olun. Bu değerlerin eşleşmesi gerekir.

Daha sonra, sistem hizmetlerinin ve genel kümenizin, sonraki bölümde açıklandığı gibi kararlılığını belirleyen, kümenizin güvenilirlik düzeyi olan bir başka noktadır.

## <a name="reliability-characteristics-of-the-cluster"></a>Kümenin güvenilirlik özellikleri

Küme *güvenilirlik düzeyi* , kümenin birincil düğüm türünde çalışan sistem hizmetleri çoğaltmalarının sayısını belirler. Daha fazla çoğaltma, sistem hizmetlerinden daha güvenilirdir (ve dolayısıyla küme bir bütün olarak).

> [!IMPORTANT]
> Güvenilirlik düzeyi küme düzeyinde ayarlanır ve birincil düğüm türünün en az düğüm sayısını belirler. Üretim iş yükleri, bir güvenilirlik düzeyi (beş düğümden daha büyük veya eşit) veya daha yukarıya gerek duyar.  

Güvenilirlik katmanı aşağıdaki değerleri alabilir:

* **Platin** sistem hizmetleri, hedef çoğaltma kümesi dokuz sayısıyla çalışır
* **Altın** -sistem hizmetleri, hedef çoğaltma kümesi sayısı yedi ile çalışır
* **Gümüş** -sistem hizmetleri, hedef çoğaltma kümesi sayısı beş ile çalışır
* **Bronz** -sistem hizmetleri, hedef çoğaltma kümesi sayısı üç ile çalışır

Güvenilirlik katmanını seçme önerisi aşağıda verilmiştir. Çekirdek düğümlerin sayısı, bir güvenilirlik katmanı için en düşük düğüm sayısına de ayarlanır.


| **Düğüm sayısı** | **Güvenilirlik katmanı** |
| --- | --- |
| 1 | *`reliabilityLevel`Parametre belirtmeyin: sistem onu hesaplar.* |
| 3 | Bronz |
| 5 veya 6| Silver |
| 7 veya 8 | Gold |
| 9 ve yukarı | PLA |

Kümenizin boyutunu (tüm düğüm türlerindeki sanal makine örneklerinin toplamı) artırdığınızda veya azaltırsanız, kümenizin bir katmandan diğerine güvenilirliğini güncelleştirmeyi göz önünde bulundurun. Bunun yapılması, Sistem Hizmetleri çoğaltma kümesi sayısını değiştirmek için gereken küme yükseltmelerini tetikler. Kümede düğüm ekleme gibi başka herhangi bir değişiklik yapmadan önce yükseltme işleminin tamamlanmasını bekleyin.  Service Fabric Explorer veya [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade) komutunu çalıştırarak yükseltmenin ilerlemesini izleyebilirsiniz

### <a name="capacity-planning-for-reliability"></a>Güvenilirlik için kapasite planlaması

Kümenizin kapasite gereksinimleri, belirli iş yükünüz ve güvenilirlik gereksinimlerinize göre belirlenir. Bu bölüm, kapasite planlamasına başlamanıza yardımcı olmak için genel rehberlik sağlar.

#### <a name="virtual-machine-sizing"></a>Sanal makine boyutlandırma

**Üretim iş yükleri için önerilen VM boyutu (SKU), en az 50 GB yerel SSD, 2 çekirdek ve 4 GiB bellek içeren [standart D2_V2](../virtual-machines/dv2-dsv2-series.md) (veya eşdeğerdir).** En az 50 GB yerel SSD önerilir, ancak bazı iş yükleri (örneğin, Windows kapsayıcıları çalıştıran) daha büyük diskler gerektirir. Üretim iş yükleri için diğer [VM boyutlarını](../virtual-machines/sizes-general.md) seçerken, aşağıdaki kısıtlamaları göz önünde bulundurun:

- Standart a0 gibi kısmi çekirdekli VM boyutları desteklenmez.
- *A serisi* Performans nedenleriyle VM boyutları desteklenmez.
- Düşük öncelikli VM 'Ler desteklenmez.

#### <a name="primary-node-type"></a>Birincil düğüm türü

Azure üzerindeki **üretim iş yükleri** en az beş birincil düğüm (VM örneği) ve gümüş güvenilirlik katmanını gerektirir. Küme birincil düğüm türünü sistem hizmetlerine ayırmanız ve uygulamanızı ikincil düğüm türlerine dağıtmak için yerleştirme kısıtlamalarını kullanmanız önerilir.

Azure 'daki **test iş yükleri** , en az bir veya üç birincil düğüm çalıştırabilir. Tek düğümlü bir küme yapılandırmak için, `reliabilityLevel` ayarın kaynak yöneticisi şablonunuzda tamamen atlandığından emin olun (için boş dize değeri belirtilmesi `reliabilityLevel` yeterli değildir). Azure portal ile ayarlanmış bir düğüm kümesi ayarlarsanız, bu yapılandırma otomatik olarak yapılır.

> [!WARNING]
> Tek düğümlü kümeler güvenilirlik olmadan özel bir yapılandırmayla çalışır ve ölçeği genişletme desteklenmez.

#### <a name="non-primary-node-types"></a>Birincil olmayan düğüm türleri

Birincil olmayan düğüm türü için düğüm sayısı alt sınırı, düğüm türünün belirli [dayanıklılık düzeyine](#durability-characteristics-of-the-cluster) bağlıdır. Düğüm türü için çalıştırmak istediğiniz uygulama veya hizmet çoğaltmaları sayısına ve iş yükünün durum bilgisiz veya durumsuz olmasına bağlı olarak düğüm sayısını (ve dayanıklılık düzeyi) planlamanız gerekir. Kümeyi dağıttıktan sonra bir düğüm türündeki VM sayısını artırabilir veya azaltabilirsiniz.

##### <a name="stateful-workloads"></a>Durum bilgisi olan iş yükleri

Service Fabric [güvenilir koleksiyonlar veya güvenilir aktörler](service-fabric-choose-framework.md)kullanan, durum bilgisi olan üretim iş yükleri için en düşük ve hedef çoğaltma sayısı beş önerilir. Bu şekilde, düzenli bir durumda, her hata etki alanı ve yükseltme etki alanında bir çoğaltma (bir çoğaltma kümesinden) biter. Genel olarak, sistem hizmetleri için ayarladığınız güvenilirlik düzeyini, durum bilgisi olan hizmetleriniz için kullandığınız çoğaltma sayısı için bir kılavuz olarak kullanın.

##### <a name="stateless-workloads"></a>Durum bilgisiz iş yükleri

Durum bilgisi olmayan üretim iş yükleri için, en düşük desteklenen birincil düğüm türü boyutu, çekirdeği sürdürmek için üç ' dür, ancak beş düğüm türü boyutu önerilir.

## <a name="next-steps"></a>Sonraki adımlar

Kümenizi yapılandırmadan önce, `Not Allowed` Aksi takdirde değiştirilemeyen sistem yapılandırma ayarları nedeniyle kümenizin daha sonra yeniden oluşturulması gereğini azaltmak için [küme yükseltme ilkelerini](service-fabric-cluster-fabric-settings.md) gözden geçirin.

Küme planlaması hakkında daha fazla bilgi için bkz.

* [İşlem planlama ve ölçeklendirme](service-fabric-best-practices-capacity-scaling.md)
* [Service Fabric uygulamalar için kapasite planlaması](service-fabric-capacity-planning.md)
* [Olağanüstü durum kurtarma planlaması](service-fabric-disaster-recovery.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
