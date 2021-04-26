---
title: Sanal makineler için Azure ayrılmış konaklarına genel bakış
description: Azure adanmış ana bilgisayarlarının sanal makineleri dağıtmak için nasıl kullanılabileceği hakkında daha fazla bilgi edinin.
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 46de182c7acfaf75b2e65fa318717348dd1c4b73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667356"
---
# <a name="azure-dedicated-hosts"></a>Azure ayrılmış Konakları

Azure adanmış ana bilgisayar, bir veya daha fazla sanal makineyi bir Azure aboneliğine ayrılmış olarak barındırabilecek fiziksel sunucular sağlayan bir hizmettir. Adanmış konaklar, kaynak olarak sağlanmış olan veri merkezlerimizde kullanılan fiziksel sunuculardır. Bir bölge, kullanılabilirlik alanı ve hata etki alanı içinde adanmış konaklar sağlayabilirsiniz. Daha sonra, gereksinimlerinizi en iyi şekilde karşılayan her yapılandırma için VM 'Leri doğrudan sağlanan konaklarınıza yerleştirebilirsiniz.


## <a name="benefits"></a>Avantajlar 

Tüm konağın rezerve etmek aşağıdaki avantajları sağlar:

-   Fiziksel sunucu düzeyinde donanım yalıtımı. Konaklarınıza başka VM 'Ler yerleştirilmeyecektir. Ayrılmış konaklar aynı veri merkezlerinde dağıtılır ve aynı ağı ve temel alınan depolama altyapısını diğer, yalıtılmış olmayan konaklarla paylaşır.
-   Azure platformu tarafından başlatılan bakım olayları üzerinde denetim. Bakım olaylarının çoğunluğu sanal makinelerinizde hiç etkilenmeyeceğinden, duraklamanın her saniyesi bir etkiye sahip olabileceği bazı hassas iş yükleri vardır. Adanmış konaklar sayesinde hizmetinize etkisini azaltmak için bir bakım penceresine geçebilirsiniz.
-   Azure hibrit avantajı ile, Windows ve SQL için kendi lisanslarınızı Azure 'a getirebilirsiniz. Hibrit avantajlarının kullanılması size ek avantajlar sağlar. Daha fazla bilgi için bkz. [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/).


## <a name="groups-hosts-and-vms"></a>Gruplar, konaklar ve VM 'Ler  

![Adanmış konaklar için yeni kaynakların görünümü.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

**Konak grubu** , adanmış Konakları koleksiyonunu temsil eden bir kaynaktır. Bir bölge ve kullanılabilirlik bölgesinde bir konak grubu oluşturur ve bu gruba ana bilgisayar ekleyebilirsiniz.

Bir **konak** , bir Azure veri merkezinde fiziksel sunucuyla eşlenmiş bir kaynaktır. Fiziksel sunucu, ana bilgisayar oluşturulduğunda ayrılır. Konak grubu içinde bir konak oluşturulur. Bir konakta hangi VM boyutlarının oluşturulabileceği bir SKU vardır. Her ana bilgisayar, aynı boyut serisinde oldukları sürece farklı boyutlarda birden çok VM barındırırlar.


## <a name="high-availability-considerations"></a>Yüksek kullanılabilirlik konuları 

Yüksek kullanılabilirlik için birden çok sanal makine dağıtmanız gerekir (en az 2). Azure ayrılmış Konakları sayesinde, hata yalıtımı sınırlarınızı şekillendirmek için altyapınızı sağlamak üzere çeşitli seçenekleriniz vardır.

### <a name="use-availability-zones-for-fault-isolation"></a>Hata yalıtımı için Kullanılabilirlik Alanları kullanma

Kullanılabilirlik alanları, bir Azure bölgesi içinde benzersiz fiziksel konumlardır. Her alan bağımsız güç, soğutma ve ağ bağlantısı ile donatılmış bir veya daha fazla veri merkezinden oluşur. Tek bir kullanılabilirlik bölgesinde bir konak grubu oluşturulur. Oluşturulduktan sonra tüm konaklar bu bölgeye yerleştirilir. Bölgeler arasında yüksek kullanılabilirlik elde etmek için birden çok konak grubu oluşturmanız (her bölge için bir tane) ve konaklarınızı uygun şekilde yaymanız gerekir.

Bir kullanılabilirlik bölgesine bir konak grubu atarsanız, bu konakta oluşturulan tüm VM 'Lerin aynı bölgede oluşturulması gerekir.

### <a name="use-fault-domains-for-fault-isolation"></a>Hata yalıtma için hata etki alanlarını kullanma

Bir konak, belirli bir hata etki alanında oluşturulabilir. Bir ölçek kümesi veya kullanılabilirlik kümesindeki VM gibi, farklı hata etki alanlarında bulunan konaklar da veri merkezindeki farklı fiziksel raflara yerleştirilir. Bir konak grubu oluşturduğunuzda, hata etki alanı sayısını belirtmeniz gerekir. Konak grubu içinde konaklar oluştururken, her konak için hata etki alanı atarsınız. VM 'Ler herhangi bir hata etki alanı ataması gerektirmez.

Hata etki alanları birlikte bulundurma ile aynı değildir. Aynı hata etki alanının iki ana bilgisayar için olması, birbirleriyle yakınlardır.

Hata etki alanları konak grubunun kapsamına alınır. İki konak grubu arasında benzeşim önleme (farklı kullanılabilirlik bölgelerinde olmadıkları durumlar dışında) konusunda herhangi bir varsayım yapmayın.

Farklı hata etki alanlarına sahip konaklara dağıtılan VM 'Ler, hata yalıtımı korumasını artırmak için, birden çok depolama Damgalarında temel alınan yönetilen diskler hizmetlerine sahip olur.

### <a name="using-availability-zones-and-fault-domains"></a>Kullanılabilirlik Alanları ve hata etki alanlarını kullanma

Daha da fazla hata yalıtımı elde etmek için her iki özelliği birlikte kullanabilirsiniz. Bu durumda, her konak grubu için ' de kullanılabilirlik alanı ve hata etki alanı sayısı ' nı belirtirsiniz, gruptaki konaklarınızın her birine bir hata etki alanı atayabilir ve VM 'lerinizin her birine bir kullanılabilirlik alanı atayabilirsiniz

[Burada](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) bulunan Kaynak Yöneticisi örnek şablonu, bir bölgedeki en yüksek dayanıklılık için Konakları yaymak üzere bölgeleri ve hata etki alanlarını kullanır.


## <a name="manual-vs-automatic-placement"></a>El ile ve otomatik yerleştirme 

Azure 'da bir VM oluştururken, kullanılacak adanmış ana bilgisayarı seçebilirsiniz. Ayrıca, sanal makinelerinizi bir konak grubu içindeki mevcut konaklara otomatik olarak yerleştirmek için seçeneğini de kullanabilirsiniz. 

Yeni bir konak grubu oluştururken, otomatik VM yerleştirme ayarının seçili olduğundan emin olun. VM 'nizi oluştururken, konak grubunu seçin ve Azure 'un VM 'niz için en iyi Konağı seçmesini sağlayın. 

Otomatik yerleştirme için etkinleştirilen konak grupları, tüm VM 'Lerin otomatik olarak yerleştirilmesini gerektirmez. Konak grubu için otomatik yerleştirme seçildiğinde bile, yine de bir konak seçebilirsiniz. 

### <a name="limitations"></a>Sınırlamalar

Otomatik VM yerleşimi kullanılırken bilinen sorunlar ve sınırlamalar:

- Adanmış konaklarınıza Azure hibrit avantajları uygulayacaksınız.
- VM 'nizi yeniden dağıtmanız mümkün olmayacaktır. 
- Adanmış konaklarla Lsv2, NVasv4, NVsv3, Msv2 veya d serisi VM 'Leri kullanamayacaksınız 


## <a name="virtual-machine-scale-set-support"></a>Sanal makine ölçek kümesi desteği

Sanal Makine Ölçek Kümeleri, bir grup sanal makineyi tek bir kaynak olarak değerlendirmenize ve kullanılabilirlik, yönetim, ölçeklendirme ve düzenleme ilkelerini bir grup olarak uygulamanıza olanak tanır. Ayrıca, var olan ayrılmış ana makinelerinizin sanal makine ölçek kümeleri için de kullanılabilir. 

Bir sanal makine ölçek kümesi oluştururken, var olan bir konak grubunu özel konaklarda oluşturulan tüm VM örneklerine sahip olacak şekilde belirtebilirsiniz.

Ayrılmış bir konak grubunda bir sanal makine ölçek kümesi oluşturulurken aşağıdaki gereksinimler geçerlidir:

- Otomatik VM yerleştirme özelliğinin etkinleştirilmesi gerekir.
- Konak grubunuzun kullanılabilirlik ayarı ölçek kümesiyle eşleşmelidir. 
    - Bölgesel ölçek kümeleri için bölgesel bir konak grubu (bir kullanılabilirlik bölgesi belirtilmeden oluşturulan) kullanılmalıdır.
    - Konak grubu ve ölçek kümesi aynı Kullanılabilirlik alanını kullanıyor olmalıdır. 
    - Konak grubu düzeyi için hata etki alanı sayısı, ölçek kümesi için hata etki alanı sayısıyla eşleşmelidir. Azure portal, 1 hata etki alanı sayısını ayarlayan ölçek kümesi için *Maksimum yayma* belirlemenizi sağlar.
- Ayrılmış konaklar önce, yeterli kapasiteye sahip ve ölçek kümesi bölgeleri ve hata etki alanları için aynı ayarlarla oluşturulmalıdır.
- Adanmış konaklarınız için desteklenen VM boyutları, ölçek kümesi için kullanılan bir ile eşleşmelidir.

Tüm ölçek ayarlama düzenleme ve iyileştirmeler ayarları adanmış konaklar tarafından desteklenmez. Ölçek kümesine aşağıdaki ayarları uygulayın: 
- Aşırı sağlama önerilmez ve varsayılan olarak devre dışıdır. Fazla sağlamayı etkinleştirebilirsiniz, ancak konak grubunun fazla sağlanmış örnekler de dahil olmak üzere tüm VM 'Ler için kapasiteye sahip olmaması durumunda ölçek kümesi ayırması başarısız olur. 
- ScaleSetVM düzenleme modunu kullanma 
- Ortak konum için yakınlık yerleştirme gruplarını kullanma



## <a name="maintenance-control"></a>Bakım denetimi

Sanal makinelerinizi destekleyen altyapı, güvenilirliği, performansı ve güvenliği artırmak ve yeni özellikleri başlatmak için zaman zaman güncelleştirilebileceğini. Azure platformu, mümkün olduğunda platform bakımının etkilerini en aza indirmeye çalışır, ancak *bakım duyarlı* iş yükleri olan müşteriler VM 'nin dondurulmuş olması veya bakım için kesilmesi gereken birkaç saniye daha fazla zaman sayalamaz.

**Bakım denetimi** , müşterilere kendi adanmış konaklarında zamanlanan normal platform güncelleştirmelerini atlama seçeneği sağlar ve ardından bunu, 35 günlük bir pencere penceresinde tercih ettikleri zaman uygular. Bakım penceresinde, bakım ' ı dilediğiniz sırada doğrudan ana bilgisayar düzeyinde uygulayabilirsiniz. Bakım penceresi bittikten sonra, Microsoft ileri taşınır ve bekleyen Bakımı ana bilgisayarlara, Kullanıcı tanımlı hata etki alanlarına izleyemeyen bir sırada uygular.

Daha fazla bilgi için bkz. [Platform güncelleştirmelerini bakım denetimiyle yönetme](./maintenance-control.md).

## <a name="capacity-considerations"></a>Kapasiteye ilişkin önemli noktalar

Adanmış bir ana bilgisayar sağlandıktan sonra Azure bu uygulamayı fiziksel sunucuya atar. Bu, sanal makinenizin sağlanması gerektiğinde kapasitenin kullanılabilirliğini garanti eder. Azure, ana bilgisayarınız için fiziksel bir sunucu seçmek üzere bölgedeki (veya bölgedeki) tüm kapasiteyi kullanır. Ayrıca, müşterilerin küme içindeki boş alan tükenmeden ayrılmış ana bilgisayar parmak izini büyümesi bekleneceği anlamına gelir.

## <a name="quotas"></a>Kotalar

Adanmış bir konak dağıtırken tüketilen iki tür kota vardır.

1. Adanmış konak vCPU kotası. Varsayılan kota sınırı, bölge başına 3000 vCPU olur.
1. VM boyutu aile kotası. Örneğin, **Kullandıkça Öde** aboneliğine yalnızca, Doğu ABD bölgesinde Dsv3 boyut serisi için kullanılabilen 10 vCPU kotası olabilir. Adanmış ana bilgisayarı dağıtabilmeniz için, Dsv3 adanmış bir konak dağıtmak üzere en az 64 vCPU 'ya bir kota artışı istemeniz gerekir. 

Kota artışı istemek için [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)bir destek isteği oluşturun.

Adanmış bir konağın sağlanması hem adanmış konak vCPU hem de VM ailesi vCPU kotasını kullanır, ancak bölgesel vCPU 'yu kullanmaz.


![Portalda kullanım ve Kotalar sayfasının ekran görüntüsü](./media/virtual-machines-common-dedicated-hosts/quotas.png)

Daha fazla bilgi için bkz. [sanal makine vCPU kotaları](./windows/quotas.md).

Ücretsiz deneme ve MSDN aboneliklerinin Azure ayrılmış konakları için kotası yoktur.

## <a name="pricing"></a>Fiyatlandırma

Kullanıcılar, kaç VM dağıtıldığını göz önüne alarak adanmış ana bilgisayar başına ücretlendirilir. Aylık deyiminizde, yeni bir faturalanabilir kaynak türü olan Konakları görürsünüz. Adanmış bir konaktaki VM 'Ler hala deyiminizde gösterilmeye devam eder, ancak 0 ' ın bir fiyatını taşır.

Konak fiyatı, VM ailesi, türü (donanım boyutu) ve bölge temel alınarak ayarlanır. Ana bilgisayar fiyatı, konakta desteklenen en büyük VM boyutuna bağlıdır.

Yazılım lisanslama, depolama ve ağ kullanımı, konaktan ve VM 'lerden ayrı olarak faturalandırılır. Bu faturalanabilir öğelerde değişiklik yapılmaz.

Daha fazla bilgi için bkz. [Azure ayrılmış ana bilgisayar fiyatlandırması](https://aka.ms/ADHPricing).

Ayrıca, [ayrılmış bir Azure ayrılmış ana bilgisayar örneğiyle](prepay-dedicated-hosts-reserved-instances.md)maliyetlerle tasarruf edebilirsiniz.
 
## <a name="sizes-and-hardware-generations"></a>Boyutlar ve donanım oluşturmaları

Bir konak için bir SKU tanımlanmıştır ve VM boyut serisini ve türünü temsil eder. Aynı boyut serisinde olduğu sürece, tek bir konak içinde farklı boyutlardaki birden fazla VM 'yi karıştırabilirsiniz. 

*Tür* , donanım oluşturma ' dır. Aynı VM serisine ait farklı donanım türleri farklı CPU satıcılarından ve farklı CPU nesilleri ve çekirdek sayısına sahip olacaktır. 

Boyutlar ve donanım türleri bölgeye göre farklılık gösterir. Daha fazla bilgi edinmek için konak [fiyatlandırma sayfasına](https://aka.ms/ADHPricing) bakın.

> [!NOTE]
> Adanmış bir ana bilgisayar yeniden kurulduktan sonra, boyutunu veya türünü değiştiremezsiniz. Farklı türde bir boyut gerekiyorsa, yeni bir konak oluşturmanız gerekir.  

## <a name="host-life-cycle"></a>Ana bilgisayar yaşam döngüsü


Azure, konaklarınızın sistem durumunu izler ve yönetir. Ana bilgisayarınızı sorguladığınızda aşağıdaki durumlar döndürülür:

| Sistem Durumu   | Description       |
|----------|----------------|
| Ana bilgisayar kullanılabilir     | Konağınız ile ilgili bilinen bir sorun yoktur.   |
| Araştırma altında ana bilgisayar  | Aradığım ana bilgisayarla ilgili bazı sorunlar yaşıyoruz. Bu, Azure 'un, tanımlanan sorunun kapsamını ve kök nedenini belirlemek için gereken geçici bir durumdur. Konakta çalışan sanal makineler etkilenebilir. |
| Konağı serbest bırakma bekleniyor   | Azure, Konağı sağlıklı bir duruma geri yükleyemiyor ve sanal makinelerinizi bu konaktan yeniden dağıtmanıza neden olacak. `autoReplaceOnFailure`Etkinleştirilirse, sanal makineleriniz sağlıklı donanıma karşı *hizmet* olarak çalışır. Aksi halde, sanal makineniz başarısız olmak üzere bir konakta çalışıyor olabilir.|
| Konak serbest bırakıldı  | Tüm sanal makineler konaktan kaldırıldı. Bu ana bilgisayar için artık bu konak için ücret alınmaz çünkü donanım, döndürme dışında bırakıldı.   |


## <a name="next-steps"></a>Sonraki adımlar

- [Azure PowerShell](./windows/dedicated-hosts-powershell.md), [Portal](./dedicated-hosts-portal.md)ve [Azure CLI](./linux/dedicated-hosts-cli.md)kullanarak adanmış bir konak dağıtabilirsiniz.

- [Burada](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), bir bölgedeki maksimum dayanıklılık için hem bölge hem de hata etki alanı kullanan örnek şablon vardır.

- Ayrıca, [ayrılmış bir Azure ayrılmış ana bilgisayar örneğiyle](prepay-dedicated-hosts-reserved-instances.md)maliyetlerle tasarruf edebilirsiniz.
