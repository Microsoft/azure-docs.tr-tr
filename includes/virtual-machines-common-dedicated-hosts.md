---
title: include dosyası
description: include dosyası
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/26/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 31fdd85fdcc40b38738d33e2c0c13797db7b1d42
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390554"
---
## <a name="benefits"></a>Avantajlar 

Tüm konağın rezerve etmek aşağıdaki avantajları sağlar:

-   Fiziksel sunucu düzeyinde donanım yalıtımı. Konaklarınıza başka VM 'Ler yerleştirilmeyecektir. Ayrılmış konaklar aynı veri merkezlerinde dağıtılır ve aynı ağı ve temel alınan depolama altyapısını diğer, yalıtılmış olmayan konaklarla paylaşır.
-   Azure platformu tarafından başlatılan bakım olayları üzerinde denetim. Bakım olaylarının çoğunluğu sanal makinelerinizde hiç etkilenmeyeceğinden, duraklamanın her saniyesi bir etkiye sahip olabileceği bazı hassas iş yükleri vardır. Adanmış konaklar sayesinde hizmetinize etkisini azaltmak için bir bakım penceresine geçebilirsiniz.
-   Azure hibrit avantajı ile, Windows ve SQL için kendi lisanslarınızı Azure 'a getirebilirsiniz. Hibrit avantajlarının kullanılması size ek avantajlar sağlar. Daha fazla bilgi için bkz. [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/).



## <a name="groups-hosts-and-vms"></a>Gruplar, konaklar ve VM 'Ler  

![Adanmış konaklar için yeni kaynakların görünümü.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

**Konak grubu** , adanmış Konakları koleksiyonunu temsil eden bir kaynaktır. Bir bölge ve kullanılabilirlik bölgesinde bir konak grubu oluşturur ve bu gruba ana bilgisayar ekleyebilirsiniz.

Bir **konak** , bir Azure veri merkezinde fiziksel sunucuyla eşlenmiş bir kaynaktır. Fiziksel sunucu, ana bilgisayar oluşturulduğunda ayrılır. Konak grubu içinde bir konak oluşturulur. Bir konakta hangi VM boyutlarının oluşturulabileceği bir SKU vardır. Her ana bilgisayar, aynı boyut serisinde oldukları sürece farklı boyutlarda birden çok VM barındırırlar.

Azure 'da bir VM oluştururken, VM 'niz için hangi adanmış konağın kullanılacağını seçebilirsiniz. Konaklarınıza hangi VM 'Lerin yerleştirildiğini tam denetime sahip olursunuz.


## <a name="high-availability-considerations"></a>Yüksek kullanılabilirlik konuları 

Yüksek kullanılabilirlik için birden çok sanal makine dağıtmanız gerekir (en az 2). Azure ayrılmış Konakları sayesinde, hata yalıtımı sınırlarınızı şekillendirmek için altyapınızı sağlamak üzere çeşitli seçenekleriniz vardır.

### <a name="use-availability-zones-for-fault-isolation"></a>Hata yalıtımı için Kullanılabilirlik Alanları kullanma

Kullanılabilirlik alanları, bir Azure bölgesi içinde benzersiz fiziksel konumlardır. Her alan bağımsız güç, soğutma ve ağ bağlantısı ile donatılmış bir veya daha fazla veri merkezinden oluşur. Tek bir kullanılabilirlik bölgesinde bir konak grubu oluşturulur. Oluşturulduktan sonra tüm konaklar bu bölgeye yerleştirilir. Bölgeler arasında yüksek kullanılabilirlik elde etmek için birden çok konak grubu oluşturmanız (her bölge için bir tane) ve konaklarınızı uygun şekilde yaymanız gerekir.

Bir kullanılabilirlik bölgesine bir konak grubu atarsanız, bu konakta oluşturulan tüm VM 'Lerin aynı bölgede oluşturulması gerekir.

### <a name="use-fault-domains-for-fault-isolation"></a>Hata yalıtma için hata etki alanlarını kullanma

Bir konak, belirli bir hata etki alanında oluşturulabilir. Bir ölçek kümesi veya kullanılabilirlik kümesindeki VM gibi, farklı hata etki alanlarında bulunan konaklar da veri merkezindeki farklı fiziksel raflara yerleştirilir. Bir konak grubu oluşturduğunuzda, hata etki alanı sayısını belirtmeniz gerekir. Konak grubu içinde konaklar oluştururken, her konak için hata etki alanı atarsınız. VM 'Ler herhangi bir hata etki alanı ataması gerektirmez.

Hata etki alanları collocation ile aynı değildir. Aynı hata etki alanının iki ana bilgisayar için olması, birbirleriyle yakınlardır.

Hata etki alanları konak grubunun kapsamına alınır. İki konak grubu arasında benzeşim önleme (farklı kullanılabilirlik bölgelerinde olmadıkları durumlar dışında) konusunda herhangi bir varsayım yapmayın.

Farklı hata etki alanlarına sahip konaklara dağıtılan VM 'Ler, hata yalıtımı korumasını artırmak için, birden çok depolama Damgalarında temel alınan yönetilen diskler hizmetlerine sahip olur.

### <a name="using-availability-zones-and-fault-domains"></a>Kullanılabilirlik Alanları ve hata etki alanlarını kullanma

Daha da fazla hata yalıtımı elde etmek için her iki özelliği birlikte kullanabilirsiniz. Bu durumda, her konak grubu için ' de kullanılabilirlik alanı ve hata etki alanı sayısı ' nı belirtirsiniz, gruptaki konaklarınızın her birine bir hata etki alanı atayabilir ve VM 'lerinizin her birine bir kullanılabilirlik alanı atayabilirsiniz

[Burada](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) bulunan Kaynak Yöneticisi örnek şablonu, bir bölgedeki en yüksek dayanıklılık için Konakları yaymak üzere bölgeleri ve hata etki alanlarını kullanır.

## <a name="maintenance-control"></a>Bakım denetimi

Sanal makinelerinizi destekleyen altyapı, güvenilirliği, performansı ve güvenliği artırmak ve yeni özellikleri başlatmak için zaman zaman güncelleştirilebileceğini. Azure platformu, mümkün olduğunda platform bakımının etkilerini en aza indirmeye çalışır, ancak *bakım duyarlı* iş yükleri olan müşteriler VM 'nin dondurulmuş olması veya bakım için kesilmesi gereken birkaç saniye daha fazla zaman sayalamaz.

**Bakım denetimi** , müşterilere kendi adanmış konaklarında zamanlanan normal platform güncelleştirmelerini atlama seçeneği sağlar ve ardından bunu, 35 günlük bir pencere penceresinde tercih ettikleri zaman uygular.

> [!NOTE]
>  Bakım denetimi şu anda sınırlı bir önizleme aşamasındadır ve bir ekleme işlemi gerektirir. Bir [aday anketi](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6lJf7DwiQxNmz51ksQvxV9UNUM3UllWUjBMTFZQUFhHUDI0VTBPQlJFNS4u)göndererek bu önizleme için geçerlidir.

## <a name="capacity-considerations"></a>Kapasite Konuları

Adanmış bir ana bilgisayar sağlandıktan sonra Azure bu uygulamayı fiziksel sunucuya atar. Bu, sanal makinenizin sağlanması gerektiğinde kapasitenin kullanılabilirliğini garanti eder. Azure, ana bilgisayarınız için fiziksel bir sunucu seçmek üzere bölgedeki (veya bölgedeki) tüm kapasiteyi kullanır. Ayrıca, müşterilerin küme içindeki boş alan tükenmeden ayrılmış ana bilgisayar parmak izini büyümesi bekleneceği anlamına gelir.

## <a name="quotas"></a>Kotalar

Bölge başına adanmış konaklar için 3000 vCPU varsayılan kota sınırı vardır. Ancak dağıtabileceğiniz ana bilgisayar sayısı ayrıca konak için kullanılan VM boyutu ailesinin kotasıyla sınırlıdır. Örneğin, **Kullandıkça Öde** aboneliğine yalnızca, Doğu ABD bölgesinde Dsv3 boyut serisi için kullanılabilen 10 vCPU kotası olabilir. Bu durumda, adanmış bir konak dağıtabilmeniz için en az 64 vCPU 'ya bir kota artışı istemeniz gerekir. Gerekirse bir istek vermek için sağ üst köşedeki **artış iste** düğmesini seçin.

![Portalda kullanım ve Kotalar sayfasının ekran görüntüsü](./media/virtual-machines-common-dedicated-hosts/quotas.png)

Daha fazla bilgi için bkz. [sanal makine vCPU kotaları](/azure/virtual-machines/windows/quotas).

Ücretsiz deneme ve MSDN aboneliklerinin Azure ayrılmış konakları için kotası yoktur.

## <a name="pricing"></a>Fiyatlandırma

Kullanıcılar, kaç VM dağıtıldığını göz önüne alarak adanmış ana bilgisayar başına ücretlendirilir. Aylık deyiminizde, yeni bir faturalanabilir kaynak türü olan Konakları görürsünüz. Adanmış bir konaktaki VM 'Ler hala deyiminizde gösterilmeye devam eder, ancak 0 ' ın bir fiyatını taşır.

Konak fiyatı, VM ailesi, türü (donanım boyutu) ve bölge temel alınarak ayarlanır. Ana bilgisayar fiyatı, konakta desteklenen en büyük VM boyutuna bağlıdır.

Yazılım lisanslama, depolama ve ağ kullanımı, konaktan ve VM 'lerden ayrı olarak faturalandırılır. Bu faturalanabilir öğelerde değişiklik yapılmaz.

Daha fazla bilgi için bkz. [Azure ayrılmış ana bilgisayar fiyatlandırması](https://aka.ms/ADHPricing).
 
## <a name="vm-families-and-hardware-generations"></a>VM aileleri ve donanım nesilleri

Bir konak için bir SKU tanımlanmıştır ve VM boyut serisini ve türünü temsil eder. Aynı boyut serisinde olduğu sürece, tek bir konak içinde farklı boyutlardaki birden fazla VM 'yi karıştırabilirsiniz. Bu tür, bölgede şu anda kullanılabilir donanım oluşturma türüdür.

Aynı VM Serisi için farklı `types` farklı CPU satıcılarından olacaktır ve farklı CPU nesilleri ve çekirdek sayısına sahip olur.

Daha fazla bilgi edinmek için konak [fiyatlandırma sayfasına](https://aka.ms/ADHPricing) bakın.

Önizleme sırasında şu ana bilgisayar SKU\types destekliyoruz: DSv3_Type1 ve ESv3_Type1

 
## <a name="host-life-cycle"></a>Ana bilgisayar yaşam döngüsü


Azure, konaklarınızın sistem durumunu izler ve yönetir. Ana bilgisayarınızı sorguladığınızda aşağıdaki durumlar döndürülür:

| Sistem durumu   | Açıklama       |
|----------|----------------|
| Ana bilgisayar kullanılabilir     | Konağınız ile ilgili bilinen bir sorun yoktur.   |
| Araştırma altında ana bilgisayar  | Aradığım ana bilgisayarla ilgili bazı sorunlar yaşıyoruz. Bu, Azure 'un, tanımlanan sorunun kapsamını ve kök nedenini belirlemek için gereken geçici bir durumdur. Konakta çalışan sanal makineler etkilenebilir. |
| Konağı serbest bırakma bekleniyor   | Azure, Konağı sağlıklı bir duruma geri yükleyemiyor ve sanal makinelerinizi bu konaktan yeniden dağıtmanıza neden olacak. @No__t-0 etkinse sanal makineleriniz, sağlıklı donanıma karşı *hizmet* olarak çalışır. Aksi halde, sanal makineniz başarısız olmak üzere bir konakta çalışıyor olabilir.|
| Konak serbest bırakıldı  | Tüm sanal makineler konaktan kaldırıldı. Bu ana bilgisayar için artık bu konak için ücret alınmaz çünkü donanım, döndürme dışında bırakıldı.   |

