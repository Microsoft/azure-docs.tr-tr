---
title: SAP uygulamaları için Azure yakınlık yerleştirme grupları | Microsoft Docs
description: Azure yakınlık yerleştirme gruplarıyla SAP dağıtım senaryolarını açıklar
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/29/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 924fdef475c43023c69c3006db19cd9a5aa15349
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669662"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>SAP uygulamalarıyla en iyi ağ gecikmesi için Azure yakınlık yerleştirme grupları
SAP NetWeaver veya SAP S/4HANA mimarisine dayanan SAP uygulamaları, SAP uygulama katmanı ve SAP veritabanı katmanı arasındaki ağ gecikmesinden duyarlıdır. Bu duyarlılık, uygulama katmanında çalışan iş mantığının büyük bir sonucudur. SAP uygulama katmanı iş mantığını çalıştırdığı için, bir saniyede binlerce veya onlarca binlerce veya on binde bir hızda veritabanı katmanına sorgu verir. Çoğu durumda, bu sorguların doğası basittir. Bunlar, genellikle 500 mikrosaniye veya daha az bir veritabanı katmanında çalıştırılabilir.

Uygulama katmanından veritabanı katmanına bu tür bir sorgu göndermek için ağ üzerinde harcanan süre, iş süreçlerini çalıştırmak için gereken süre üzerinde büyük bir etkiye sahiptir. Ağ gecikmesi ile olan bu duyarlık, SAP dağıtım projelerinde en yüksek ağ gecikme süresini elde etmek isteyebilirsiniz. Bkz. [SAP Note #1100926-SSS:](https://launchpad.support.sap.com/#/notes/1100926/E) ağ gecikmesini sınıflandırmasına ilişkin yönergeler için ağ performansı.

Birçok Azure bölgesinde, veri merkezlerinin sayısı artmıştır. Aynı zamanda, özellikle yüksek kaliteli SAP sistemleri için müşteriler, M veya Mv2 ailesi veya HANA büyük örnekleri için daha özel VM SKU 'Ları kullanıyor. Bu Azure sanal makine türleri, Azure bölgesini tamamlayan tüm veri merkezlerinde her zaman kullanılabilir değildir. Bu olgular, SAP uygulama katmanı ve SAP DBMS katmanı arasındaki ağ gecikmesini iyileştirmek için fırsat oluşturabilir.

Ağ gecikmesini en uygun hale getirmenize olanak tanımak için Azure, [yakınlık yerleştirme grupları](../../co-location.md)sunar. Bu farklı VM türleri arasındaki ağ gecikmesini en iyi şekilde iyileştirmek için yakınlık yerleşimi grupları, farklı VM türlerini tek bir Azure veri merkezine gruplamayı zorlamak üzere kullanılabilir. İlk VM 'yi böyle bir yakınlık yerleşimi grubuna dağıtma işleminde, VM belirli bir veri merkezine bağlanır. Bu aday müşteri seslerinden dolayı, yapının kullanımı da bazı kısıtlamalar getirir:

- Tüm Azure sanal makine türlerinin her ve tüm Azure veri merkezlerinde kullanılabilir olduğunu varsayamaz. Sonuç olarak, bir yakınlık yerleşimi grubu içindeki farklı VM türlerinin birleşimi kısıtlanabilir. Bu kısıtlamalar, belirli bir VM türünü çalıştırmak için gereken konak donanımının, yerleştirme grubunun dağıtıldığı veri merkezinde mevcut olmaması nedeniyle oluşur
- Bir yakınlık yerleşimi grubundaki sanal makinelerin parçalarını yeniden boyutlandırdıkça, her durumda yeni sanal makine türünün, yakınlık yerleşimi grubunun parçası olan diğer VM 'Lerle aynı veri merkezinde kullanılabilir olduğunu otomatik olarak kabul edilemez.
- Azure, donanımı borçlandırmaya karşı bir yakınlık yerleşimi grubunun belirli sanal makinelerini başka bir Azure veri merkezine zorlayabilir. Bu durumu kapsayan Ayrıntılar için, [iyileştirilmiş gecikme süresi için belgeyi birlikte bulma kaynaklarını](../../co-location.md#planned-maintenance-and-proximity-placement-groups) okuyun  

> [!IMPORTANT]
> Olası kısıtlamaların bir sonucu olarak, yakınlık yerleşimi gruplarının kullanılması gerekir:
>
> - Yalnızca gerekli olduğunda
> - Yalnızca tek bir SAP sisteminin ayrıntı düzeyi üzerinde değil, tüm sistem yatay veya tam bir SAP yatay
> - Farklı VM türlerini ve bir yakınlık yerleşimi grubundaki VM sayısını en az bir olacak şekilde korumak için

Kullanılabilirlik Alanları belirterek VM 'Ler dağıtırsanız ve aynı Kullanılabilirlik Alanları seçtiğinizde, bu VM 'Ler arasındaki ağ gecikmesi SAP NetWeaver ve S/4HANA sistemlerini karşılayan performans ve verimlilik ile işletmek için yeterli olmalıdır. Bu varsayım, belirli bir bölgenin bir veri merkezinde veya birden çok veri merkezinden oluşturulup oluşturulmayacağı olgunun bağımsızdır. Bölgesel dağıtımlarında yakınlık yerleşimi gruplarının kullanılması için tek neden olan Azure kullanılabilirlik kümesi dağıtılan VM 'leri, bölgesel dağıtılan VM 'lerle birlikte ayırmak istediğiniz durumdur.


## <a name="what-are-proximity-placement-groups"></a>Yakınlık yerleşimi grupları nelerdir? 
Bir Azure yakınlık yerleşimi grubu, mantıksal bir yapıdır. Bir yakınlık yerleşimi grubu tanımlandığında, bu, bir Azure bölgesine ve bir Azure Kaynak grubuna bağlanır. VM 'Ler dağıtıldığında, bir yakınlık yerleşimi grubuna şunun tarafından başvuruluyor:

- Veri merkezinde dağıtılan ilk Azure VM. Son olarak, belirli bir kullanılabilirlik bölgesi için Kullanıcı tanımlarıyla birleştirilmiş Azure ayırma algoritmalarına dayalı olarak, ilk sanal makineyi bir veri merkezinde dağıtılan bir "kapsam VM" olarak düşünebilirsiniz.
- Daha sonra dağıtılan tüm VM 'Leri ilk sanal makineyle aynı veri merkezine yerleştirmek için yakınlık yerleşimi grubuna başvuran sonraki tüm VM 'Ler.

> [!NOTE]
> İlk VM 'nin yerleştirildiği veri merkezinde belirli bir VM türü çalıştıran bir konak donanımı dağıtılırsa, istenen VM türünün dağıtımı başarılı olmayacaktır. Bir hata iletisi alırsınız.

Tek bir [Azure Kaynak grubuna](../../../azure-resource-manager/management/manage-resources-portal.md) atanmış birden fazla yakınlık yerleştirme grubu olabilir. Ancak, bir yakınlık yerleşimi grubu yalnızca bir Azure Kaynak grubuna atanabilir.


## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Yalnızca Azure VM 'Leri kullanan SAP sistemleri olan yakınlık yerleştirme grupları
Azure 'da SAP NetWeaver ve S/4HANA sistem dağıtımları, [Hana büyük örnekleri](./hana-overview-architecture.md)kullanmaz. HANA büyük örnekleri kullanmayan dağıtımlar için SAP uygulama katmanı ve DBMS katmanı arasında en iyi performansı sağlamak önemlidir. Bunu yapmak için, yalnızca sistem için bir Azure yakınlık yerleşimi grubu tanımlayın.

Çoğu müşteri dağıtımında, müşteriler SAP sistemleri için tek bir [Azure Kaynak grubu](../../../azure-resource-manager/management/manage-resources-portal.md) oluşturur. Bu durumda, örneğin, üretim ERP sistem kaynak grubu ve yakınlık yerleşimi grubu arasında bire bir ilişki vardır. Diğer durumlarda, müşteriler kaynak gruplarını yatay olarak düzenler ve tüm üretim sistemlerini tek bir kaynak grubunda toplar. Bu durumda, üretim SAP sistemleri için kaynak grubunuz ve üretim SAP ERP, SAP BW gibi çeşitli yakınlık yerleştirme grupları arasında bire çok bir ilişkiye sahip olursunuz.

Tek bir yakınlık yerleşimi grubunda birkaç SAP üretimi veya üretim dışı sistemler paketlemeyi önleyin. Az sayıda SAP sistemi veya SAP sistemi ve çevresindeki uygulamaların düşük gecikmeli ağ iletişimine ihtiyacı olduğunda, bu sistemleri tek bir yakınlık yerleşimi grubuna taşımayı düşünebilirsiniz. Bir yakınlık yerleştirme grubunda daha fazla sistem gruplandırdığı için sistem paketlerinin önüne kaçının, bu da daha yüksek bir şansınız olur:

- Bu, yakınlık yerleşimi grubunun kapsamında olduğu belirli bir veri merkezinde çalıştırılamaz bir VM türüne ihtiyacınız olduğunu unutmayın.
- M serisi VM 'Ler gibi temel olmayan VM 'lerin kaynakları, zaman içinde bir yakınlık yerleşimi grubuna yazılım eklerken daha fazla ihtiyacınız olduğunda yerine getirilir.

Aşağıda açıklandığı gibi ideal yapılandırma şöyle görünür:

![Yalnızca Azure VM 'Leri olan yakınlık yerleştirme grupları](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

Bu durumda, tek SAP sistemleri her biri tek bir kaynak grubunda gruplandırılır ve her biri bir yakınlık yerleşimi grubuyla birlikte gruplandırılır. HANA genişleme veya DBMS genişleme yapılandırması kullanmanıza bakılmaksızın bir bağımlılık yoktur.

## <a name="proximity-placement-groups-and-hana-large-instances"></a>Yakınlık yerleştirme grupları ve HANA büyük örnekleri
SAP sistemlerinizden bazıları uygulama katmanı için [Hana büyük örnekleri](./hana-overview-architecture.md) kullanıyorsa, [Düzeltme 4 satırları veya damgaları](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)' nda dağıtılan Hana büyük örnekler birimleri kullanırken, Hana büyük örnekler birimi ile Azure VM 'ler arasındaki ağ gecikmesi üzerinde önemli geliştirmeler yaşayabilirsiniz. Bir iyileştirme, dağıtılan büyük örnek birimlerinin, dağıtıldığı gibi bir yakınlık yerleşimi grubuyla dağıtılması. Uygulama katmanı sanal makinelerinizi dağıtmak için bu yakınlık yerleşimi grubunu kullanabilirsiniz. Sonuç olarak, bu VM 'Ler HANA büyük örnek biriminizi barındıran veri merkezinde dağıtılır.

HANA büyük örnek biriminizdeki bir düzeltme 4 damgasında veya satırında dağıtılıp dağıtılmadığını öğrenmek için [Azure Portal aracılığıyla Azure Hana büyük örnekler denetimi](./hana-li-portal.md#look-at-attributes-of-single-hli-unit)makalesini kontrol edin. HANA büyük örnek biriminizdeki özniteliklere genel bakış bölümünde, HANA büyük örnekler biriminiz dağıtıldığında oluşturulduğu için yakınlık yerleşimi grubunun adını da belirleyebilirsiniz. Özniteliklerde genel bakışta görüntülenen ad, uygulama katmanı VM 'lerinizi dağıtmanız gereken yakınlık yerleşimi grubunun adıdır.

Yalnızca Azure sanal makinelerini kullanan SAP sistemleri ile karşılaştırıldığında, HANA büyük örnekleri kullandığınızda, kaç [Azure Kaynak grubunun](../../../azure-resource-manager/management/manage-resources-portal.md) kullanılacağına karar verirken daha az esneklik elde edersiniz. Bir [Hana büyük örnek kiracının](./hana-know-terms.md) tüm Hana büyük örnek birimleri, [Bu makalede](./hana-li-portal.md#display-of-hana-large-instance-units-in-the-azure-portal)açıklandığı gibi tek bir kaynak grubunda gruplandırılır. Örneğin, üretim ve üretim dışı sistemler veya diğer sistemler gibi ayrı kiracılara dağıtmadığınız takdirde, tüm HANA büyük örnek birimleriniz tek bir HANA büyük örnek kiracısında dağıtılır. Bu kiracının bir kaynak grubuyla bire bir ilişkisi vardır. Ancak tek bir birimin her biri için ayrı bir yakınlık yerleşimi grubu tanımlanacaktır.

Sonuç olarak, Azure Kaynak grupları ve tek bir kiracının yakınlık yerleşimi grupları arasındaki ilişkiler burada gösterildiği gibi olacaktır:

![Yakınlık yerleştirme grupları ve HANA büyük örnekleri](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>Yakınlık yerleştirme gruplarıyla dağıtım örneği
Aşağıda, Azure yakınlık yerleştirme gruplarıyla sanal makinelerinizi dağıtmak için kullanabileceğiniz bazı PowerShell komutları verilmiştir.

İlk adım, [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)oturum açtıktan sonra, dağıtım için kullanmak istediğiniz Azure aboneliğinde olup olmadığınızı denetliyoruz:

<pre><code>
Get-AzureRmContext
</code></pre>

Farklı bir aboneliğe değiştirmeniz gerekiyorsa, bu komutu çalıştırarak bunu yapabilirsiniz:

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

Şu komutu çalıştırarak yeni bir Azure Kaynak grubu oluşturun:

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

Şu komutu çalıştırarak yeni yakınlık yerleşimi grubunu oluşturun:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

Aşağıdaki gibi bir komut kullanarak ilk sanal makineyi yakınlık yerleşimi grubuna dağıtın:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Yukarıdaki komut, Windows tabanlı bir VM dağıtır. Bu VM dağıtımı başarılı olduktan sonra, yakınlık yerleşimi grubunun veri merkezi kapsamı Azure bölgesi içinde tanımlanır. Önceki komutta gösterildiği gibi, yakınlık yerleşimi grubuna başvuran sonraki tüm VM dağıtımları aynı Azure veri merkezinde dağıtılır ve bu da VM türü bu veri merkezine yerleştirilmiş donanımda barındırılıyorsa ve bu sanal makine türü için kapasite kullanılabilir.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Kullanılabilirlik kümelerini ve Kullanılabilirlik Alanları yakınlık yerleştirme gruplarıyla birleştirme
SAP sistem dağıtımları için Kullanılabilirlik Alanları kullanmanın dezavantajlarından biri, SAP uygulama katmanını belirli bir bölgedeki kullanılabilirlik kümelerini kullanarak dağıtamıyoruz. SAP uygulama katmanının, DBMS katmanıyla aynı bölgelerde dağıtılmasını istiyorsunuz. Tek bir VM dağıtımında bir kullanılabilirlik bölgesine ve kullanılabilirlik kümesine başvurulması desteklenmez. Bu nedenle, daha önce bir bölgeye başvurarak uygulama katmanınızı dağıtmaya zorlanmıştır. Uygulama katmanı VM 'lerinin farklı güncelleştirme ve hata etki alanlarına yayılmasını sağlama imkanını kaybettiniz.

Yakınlık yerleşimi gruplarını kullanarak bu kısıtlamayı atlayabilirsiniz. Dağıtım sırası aşağıda verilmiştir:

- Bir yakınlık yerleşimi grubu oluşturun.
- Bir kullanılabilirlik alanına başvurarak, genellikle DBMS sunucusundan bağlayıcı sanal makinenizin dağıtımını yapın.
- Azure yakınlık grubuna başvuran bir kullanılabilirlik kümesi oluşturun. (Bu makalenin devamındaki komutuna bakın.)
- Kullanılabilirlik kümesine ve yakınlık yerleşimi grubuna başvurarak uygulama katmanı VM 'lerini dağıtın.

Önceki bölümde gösterildiği gibi ilk VM 'yi dağıtmak yerine, VM 'yi dağıtırken bir kullanılabilirlik bölgesine ve yakınlık yerleşimi grubuna başvurun:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

Bu sanal makinenin başarılı bir dağıtımı, SAP sisteminin veritabanı örneğini tek bir kullanılabilirlik alanında barındırır. Yakınlık yerleşimi grubunun kapsamı, tanımladığınız kullanılabilirlik bölgesini temsil eden veri merkezlerinden birine sabitlenmiştir.

Aynı bölgeye veya bölgelere ve aynı yakınlık yerleşimi gruplarına başvurarak, Yönetim Hizmetleri VM 'lerini DBMS VM 'leriyle aynı şekilde dağıttığınızı varsayın. Bir sonraki adımda, SAP sisteminizin uygulama katmanı için kullanmak istediğiniz kullanılabilirlik kümelerini oluşturmanız gerekir.

Yakınlık yerleşimi grubunu tanımlayın ve oluşturun. Kullanılabilirlik kümesi oluşturma komutu, yakınlık yerleşimi grup KIMLIĞINE (adı değil) ek bir başvuru gerektirir. Şu komutu kullanarak yakınlık yerleşimi grubunun KIMLIĞINI alabilirsiniz:

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

Kullanılabilirlik kümesini oluştururken, yönetilen diskler (aksi belirtilmedikçe varsayılan) ve yakınlık yerleşimi grupları kullanırken ek parametreleri göz önünde bulundurmanız gerekir:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

İdeal olarak, üç hata etki alanı kullanmanız gerekir. Ancak, desteklenen hata etki alanlarının sayısı bölgeden bölgeye farklılık gösterebilir. Bu durumda, belirli bölgeler için mümkün olan en fazla hata etki alanı sayısı ikdir. Uygulama katmanı sanal makinelerinizi dağıtmak için, burada gösterildiği gibi, kullanılabilirlik kümesi adı ve yakınlık yerleşimi Grup adına bir başvuru eklemeniz gerekir:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Bu dağıtımın sonucu:
- Belirli bir kullanılabilirlik bölgesinde veya Kullanılabilirlik Alanları bulunan SAP sisteminiz için bir DBMS katmanı ve Merkezi Hizmetler.
- DBMS VM veya VM 'Lerle aynı Azure veri merkezlerinde kullanılabilirlik kümeleri aracılığıyla bulunan bir SAP uygulama katmanı.

> [!NOTE]
> Bir DBMS VM 'yi tek bir bölgeye ve ikinci DBMS sanal makinesine, yüksek oranda kullanılabilir bir yapılandırma oluşturmak için dağıttığınız için, bölgelerin her biri için farklı bir yakınlık yerleşimi grubuna ihtiyacınız olacaktır. Aynı değer, kullandığınız tüm kullanılabilirlik kümesi için de geçerlidir.

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>Mevcut bir sistemi yakınlık yerleşimi gruplarına taşı
Zaten dağıtılmış SAP sistemleri varsa, bazı kritik sistemlerinizden oluşan ağ gecikmesini iyileştirmek ve aynı veri merkezinde uygulama katmanını ve DBMS katmanını bulmak isteyebilirsiniz. Tüm Azure kullanılabilirlik kümesinin VM 'lerini zaten kapsamındaki mevcut bir yakınlık yerleşimi grubuna taşımak için, kullanılabilirlik kümesinin tüm VM 'lerini kapatmanız ve kullanılabilirlik kümesini Azure portal, PowerShell veya CLı aracılığıyla mevcut yakınlık yerleşimi grubuna atamanız gerekir. Kullanılabilirlik kümesinin parçası olmayan bir VM 'yi mevcut bir yakınlık yerleşimi grubuna taşımak istiyorsanız, yalnızca VM 'yi kapatmanız ve mevcut bir yakınlık yerleşimi grubuna atamanız gerekir. 


## <a name="next-steps"></a>Sonraki adımlar
Belgelere göz atın:

- [Azure 'da SAP iş yükleri: planlama ve dağıtım denetim listesi](./sap-deployment-checklist.md)
- [Önizleme: Azure CLı kullanarak yakınlık yerleşimi gruplarına VM 'Leri dağıtma](../../linux/proximity-placement-groups.md)
- [Önizleme: PowerShell kullanarak sanal makineleri yakınlık yerleştirme gruplarına dağıtma](../../windows/proximity-placement-groups.md)
- [SAP iş yükleri için Azure sanal makineler DBMS dağıtımına yönelik konular](./dbms_guide_general.md)