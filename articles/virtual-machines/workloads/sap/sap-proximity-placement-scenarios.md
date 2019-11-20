---
title: SAP uygulamalarıyla en iyi ağ gecikmesi için Azure yakınlık yerleştirme grupları | Microsoft Docs
description: Azure yakınlık yerleştirme gruplarıyla SAP dağıtım senaryolarını açıklar
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: deffcb81a4f66783fedc89c3e21ea46b15ad1c64
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720006"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>SAP uygulamalarıyla en iyi ağ gecikmesi için Azure yakınlık yerleştirme grupları
SAP NetWeaver veya SAP S/4HANA mimarisine dayanan SAP uygulamaları, SAP uygulama katmanı ve SAP veritabanı katmanı arasındaki ağ gecikmesinden duyarlıdır. Bu duyarlılık, uygulama katmanında çalışan iş mantığının büyük bir sonucudur. SAP uygulama katmanı iş mantığını çalıştırdığı için, bir saniyede binlerce veya onlarca binlerce veya on binde bir hızda veritabanı katmanına sorgu verir. Çoğu durumda, bu sorguların doğası basittir. Bunlar, genellikle 500 mikrosaniye veya daha az bir veritabanı katmanında çalıştırılabilir.

Uygulama katmanından veritabanı katmanına bu tür bir sorgu göndermek için ağ üzerinde harcanan süre, iş süreçlerini çalıştırmak için gereken süre üzerinde büyük bir etkiye sahiptir. Ağ gecikmesi ile olan bu duyarlık, SAP dağıtım projelerinde en iyi ağ gecikmesini elde etmeniz gerekir. Bkz. [SAP Note #1100926-SSS:](https://launchpad.support.sap.com/#/notes/1100926/E) ağ gecikmesini sınıflandırmasına ilişkin yönergeler için ağ performansı.

Birçok Azure bölgesinde, veri merkezlerinin sayısı artmıştır. Bu büyüme Ayrıca Kullanılabilirlik Alanları tanıtımı tarafından tetiklenir. Aynı zamanda, özellikle yüksek kaliteli SAP sistemleri için müşteriler, M serisi ailesinde veya HANA büyük örneklerde daha özel VM SKU 'Ları kullanmaktır. Bu Azure sanal makine türleri, belirli bir Azure bölgesindeki tüm veri merkezlerinde kullanılamaz. Bu iki temel nedenle, müşteriler en uygun aralıkta olmayan ağ gecikme süresi yaşadı. Bazı durumlarda, bu gecikme süresi SAP sistemlerinin en iyi performans performansını elde etmez.

Bu sorunları engellemek için Azure, [yakınlık yerleştirme grupları](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)sunmaktadır. Bu yeni işlevsellik, çeşitli SAP sistemlerini dağıtmak için zaten kullanıldı. Yakınlık yerleştirme gruplarındaki kısıtlamalar için, bu paragrafın başlangıcında başvurulan makaleye bakın. Bu makalede Azure yakınlık yerleşimi gruplarının kullanılabileceği veya kullanılması gereken SAP senaryoları ele alınmaktadır.

## <a name="what-are-proximity-placement-groups"></a>Yakınlık yerleşimi grupları nelerdir? 
Bir Azure yakınlık yerleşimi grubu, mantıksal bir yapıdır. Biri tanımlandığında bir Azure bölgesine ve bir Azure Kaynak grubuna bağlanır. VM 'Ler dağıtıldığında, bir yakınlık yerleşimi grubuna şunun tarafından başvuruluyor:

- Veri merkezinde dağıtılan ilk Azure VM. Son olarak, belirli bir kullanılabilirlik bölgesi için Kullanıcı tanımlarıyla birleştirilmiş Azure ayırma algoritmalarına dayalı olarak, ilk sanal makineyi bir veri merkezinde dağıtılan bir "kapsam VM" olarak düşünebilirsiniz.
- Daha sonra dağıtılan tüm VM 'Leri ilk sanal makineyle aynı veri merkezine yerleştirmek için yakınlık yerleşimi grubuna başvuran sonraki tüm VM 'Ler.

> [!NOTE]
> İlk VM 'nin yerleştirildiği veri merkezinde belirli bir VM türü çalıştıran bir konak donanımı dağıtılırsa, istenen VM türünün dağıtımı başarılı olmayacaktır. Bir hata iletisi alırsınız.

Tek bir [Azure Kaynak grubuna](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) atanmış birden fazla yakınlık yerleştirme grubu olabilir. Ancak, bir yakınlık yerleşimi grubu yalnızca bir Azure Kaynak grubuna atanabilir.

Yakınlık yerleştirme gruplarını kullanırken, bu noktaları göz önünde bulundurun:

- SAP sisteminiz için en iyi performansı hedefleyerek ve kendinizi, yakınlık yerleşimi gruplarını kullanarak sistem için tek bir Azure veri merkezi ile sınırlandırdığınızda, yerleştirme grubundaki tüm VM aileleri türlerini birleştiremeyebilirsiniz. Bu sınırlamalar, belirli bir sanal makine türünü çalıştırmak için gereken ana bilgisayar donanımının, yerleştirme grubunun "kapsamlı VM" öğesinin dağıtıldığı veri merkezinde mevcut olmaması nedeniyle oluşur.
- Bu tür bir SAP sisteminin yaşam döngüsü boyunca, sistemi başka bir veri merkezine taşımaya zorlanabilirsiniz. Bu taşıma, genişleme HANA DBMS katmanınızın, örneğin dört düğümden 16 düğüme taşınması ve veri merkezinde kullandığınız türde 12 VM 'ye kadar yeterli kapasiteye sahip olması gerektiğine karar verirseniz gerekli olabilir.
- Microsoft, donanım yetkisini alma nedeniyle, ilk kullandığınız bir veri merkezinde kullandığınız bir VM türü için kapasiteler oluşturabilir. Bu senaryoda, tüm yakınlık yerleşimi grubunun sanal makinelerini başka bir veri merkezine taşımanız gerekebilir.

## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Yalnızca Azure VM 'Leri kullanan SAP sistemleri olan yakınlık yerleştirme grupları
Azure 'da SAP NetWeaver ve S/4HANA sistem dağıtımları, [Hana büyük örnekleri](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)kullanmaz. HANA büyük örnekleri kullanmayan dağıtımlar için SAP uygulama katmanı ve DBMS katmanı arasında en iyi performansı sağlamak önemlidir. Bunu yapmak için, yalnızca sistem için bir Azure yakınlık yerleşimi grubu tanımlayın.

Çoğu müşteri dağıtımında, müşteriler SAP sistemleri için tek bir [Azure Kaynak grubu](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) oluşturur. Bu durumda, örneğin, üretim ERP sistem kaynak grubu ve yakınlık yerleşimi grubu arasında bire bir ilişki vardır. Diğer durumlarda, müşteriler kaynak gruplarını yatay olarak düzenler ve tüm üretim sistemlerini tek bir kaynak grubunda toplar. Bu durumda, üretim SAP sistemleri için kaynak grubunuz ve üretim SAP ERP, SAP BW gibi çeşitli yakınlık yerleştirme grupları arasında bire çok bir ilişkiye sahip olursunuz.

Tek bir yakınlık yerleşimi grubunda birkaç SAP üretimi veya üretim dışı sistemler paketlemeyi önleyin. Az sayıda SAP sistemi veya SAP sistemi ve çevresindeki uygulamaların düşük gecikmeli ağ iletişimine ihtiyacı olduğunda, bu sistemleri tek bir yakınlık yerleşimi grubuna taşımayı düşünebilirsiniz. Bir yakınlık yerleşimi grubunda daha fazla sistem gruplandırdığı için sistem paketlerinin olmaması gerekir, bu da daha yüksek bir şansınız olur:

- Bu, yakınlık yerleşimi grubunun kapsamında olduğu belirli bir veri merkezinde çalıştırılamaz bir VM türüne ihtiyacınız olduğunu unutmayın.
- M serisi VM 'Ler gibi temel olmayan VM 'lerin kaynakları, zaman içinde bir yakınlık yerleşimi grubuna yazılım eklerken daha fazla ihtiyacınız olduğunda yerine getirilir.

Aşağıda açıklandığı gibi ideal yapılandırma şöyle görünür:

![Yalnızca Azure VM 'Leri olan yakınlık yerleştirme grupları](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

Bu durumda, tek SAP sistemleri her biri tek bir kaynak grubunda gruplandırılır ve her biri bir yakınlık yerleşimi grubuyla birlikte gruplandırılır. HANA genişleme veya DBMS genişleme yapılandırması kullanmanıza bakılmaksızın bir bağımlılık yoktur.

## <a name="proximity-placement-groups-and-hana-large-instances"></a>Yakınlık yerleştirme grupları ve HANA büyük örnekleri
SAP sistemlerinizden bazıları uygulama katmanı için [Hana büyük örnekleri](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) kullanıyorsa, Hana büyük örnekler birimi Ile Azure VM 'ler arasındaki ağ gecikmesi üzerinde önemli geliştirmeler yaşayabilirsiniz [Düzeltme 4 satırları veya damgaları](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)' nda dağıtılır. Bir iyileştirme, dağıtılan büyük örnek birimlerinin, dağıtıldığı gibi bir yakınlık yerleşimi grubuyla dağıtılması. Uygulama katmanı sanal makinelerinizi dağıtmak için bu yakınlık yerleşimi grubunu kullanabilirsiniz. Sonuç olarak, bu VM 'Ler HANA büyük örnek biriminizi barındıran veri merkezinde dağıtılır.

HANA büyük örnek biriminizdeki bir düzeltme 4 damgasında veya satırında dağıtılıp dağıtılmadığını öğrenmek için [Azure Portal aracılığıyla Azure Hana büyük örnekler denetimi](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit)makalesini kontrol edin. HANA büyük örnek biriminizdeki özniteliklere genel bakış bölümünde, HANA büyük örnekler biriminiz dağıtıldığında oluşturulduğu için yakınlık yerleşimi grubunun adını da belirleyebilirsiniz. Özniteliklerde genel bakışta görüntülenen ad, uygulama katmanı VM 'lerinizi dağıtmanız gereken yakınlık yerleşimi grubunun adıdır.

Yalnızca Azure sanal makinelerini kullanan SAP sistemleri ile karşılaştırıldığında, HANA büyük örnekleri kullandığınızda, kaç [Azure Kaynak grubunun](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) kullanılacağına karar verirken daha az esneklik elde edersiniz. Bir [Hana büyük örnek kiracının](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms) tüm Hana büyük örnek birimleri, [Bu makalede](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal)açıklandığı gibi tek bir kaynak grubunda gruplandırılır. Örneğin, üretim ve üretim dışı sistemler veya diğer sistemler gibi ayrı kiracılara dağıtmadığınız takdirde, tüm HANA büyük örnek birimleriniz tek bir HANA büyük örnek kiracısında dağıtılır. Bu kiracının bir kaynak grubuyla bire bir ilişkisi vardır. Ancak tek bir birimin her biri için ayrı bir yakınlık yerleşimi grubu tanımlanacaktır.

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

Yukarıdaki komut, Windows tabanlı bir VM dağıtır. Bu VM dağıtımı başarılı olduktan sonra, yakınlık yerleşimi grubunun veri merkezi kapsamı Azure bölgesi içinde tanımlanır. Önceki komutta gösterildiği gibi, yakınlık yerleşimi grubuna başvuran sonraki tüm VM dağıtımları aynı Azure veri merkezinde dağıtılır. Bu, VM türü bu veri merkezinde yer alan donanımda barındırılabilecek ve bu VM türü için kapasiteye sahip olduğu sürece kullanılabileceğini.

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

Yakınlık yerleşimi grubunu tanımlamanız ve oluşturmanız gerekir. Kullanılabilirlik kümesi oluşturma komutu, yakınlık yerleşimi grup KIMLIĞINE (adı değil) ek bir başvuru gerektirir. Şu komutu kullanarak yakınlık yerleşimi grubunun KIMLIĞINI alabilirsiniz:

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
Zaten dağıtılmış SAP sistemleri varsa, bazı kritik sistemlerinizden oluşan ağ gecikmesini iyileştirmek ve aynı veri merkezinde uygulama katmanını ve DBMS katmanını bulmak isteyebilirsiniz. Yakınlık yerleşimi gruplarının genel önizlemesi sırasında, VM 'Leri silmeniz ve yeni bir tane oluşturmanız gerekir ve sistemi yakınlık yerleşimi gruplarına taşıyabilirsiniz. Şu anda yalnızca VM 'Leri kapatıp yakınlık yerleşimi gruplarına atayamazsınız.


## <a name="next-steps"></a>Sonraki adımlar
Belgelere göz atın:

- [Azure 'da SAP iş yükleri: planlama ve dağıtım denetim listesi](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [Önizleme: Azure CLı kullanarak yakınlık yerleşimi gruplarına VM 'Leri dağıtma](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [Önizleme: PowerShell kullanarak sanal makineleri yakınlık yerleştirme gruplarına dağıtma](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [SAP iş yükleri için Azure sanal makineler DBMS dağıtımına yönelik konular](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

