---
title: include dosyası
description: include dosyası
services: virtual-machines
author: rishabv90
ms.service: virtual-machines
ms.topic: include
ms.date: 11/05/2020
ms.author: risverma
ms.custom: include file
ms.openlocfilehash: 83a19dea56693a1caff2c982b9f772543fe1cf2e
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073443"
---
Azure Işlem, belirli bir donanım türüne yalıtılmış ve tek bir müşteriye adanmış sanal makine boyutları sunar. Yalıtılmış boyutlar canlı ve belirli donanım nesli üzerinde çalışır ve donanım oluşturma kullanımdan kaldırıldığında kullanım dışı kalır.

Yalıtılmış sanal makine boyutları, diğer müşterilerin iş yüklerinden, Toplantı uyumluluk ve mevzuat gereksinimlerini kapsayan nedenlerle yüksek derecede yalıtım gerektiren iş yükleri için idealdir.  Yalıtılmış bir boyut kullanılması, sanal makinenizin o belirli sunucu örneğinde çalışan tek bir tane olmasını garanti eder. 


Ayrıca, yalıtılmış boyut sanal makineleri büyük olduğu için müşteriler, [iç içe geçmiş sanal makineler Için Azure desteği](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)'ni kullanarak bu VM 'lerin kaynaklarını alt bölümlere ayırarak seçebiliriz.

Geçerli yalıtılmış sanal makine teklifleri şunları içerir:
* Standard_E80ids_v4
* Standard_E80is_v4
* Standard_F72s_v2
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DC8_v2


> [!NOTE]
> Yalıtılmış VM boyutları bir donanım sınırlı ömrü vardır. Ayrıntılar için lütfen aşağıya bakın

## <a name="deprecation-of-isolated-vm-sizes"></a>Yalıtılmış VM boyutlarının kullanımdan kaldırılması

Yalıtılmış VM boyutları bir donanım sınırlı ömrü vardır. Azure, boyutların resmi kullanım dışı bırakılmasıyla ilgili 12 ay önce anımsatıcılar verir ve sizin de sizin de sizin için güncel bir yalıtılmış teklif sağlar.

| Boyut | Yalıtım kullanımdan kaldırma tarihi | 
| --- | --- |
| Standard_DS15_v2 | 15 Mayıs 2021 |
| Standard_D15_v2  | 15 Mayıs 2021 |
| Standard_G5  | 15 Şubat 2022 |
| Standard_GS5  | 15 Şubat 2022 |
| Standard_E64i_v3  | 15 Şubat 2022 |
| Standard_E64is_v3  | 15 Şubat 2022 |
| Standard_DC8_v2 | 15 Şubat 2022 |


## <a name="faq"></a>SSS
### <a name="q-is-the-size-going-to-get-retired-or-only-its-isolation-feature"></a>S: boyut, kullanımdan kalkması veya yalnızca "yalıtım" özelliğine gidiyor mu?
Y **: Şu** anda yalnızca VM boyutlarının yalıtma özelliği kullanımdan kaldırılıyor. Kullanımdan kaldırılan yalıtılmış Boyutlar, yalıtılmış olmayan durumda olmaya devam edecektir. Yalıtım gerekmiyorsa, gerçekleştirilecek bir eylem yoktur ve VM beklendiği gibi çalışmaya devam eder.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>S: sanal makine, yalıtılmış olmayan bir donanımda yer aldığı zaman kapalı kalma süresi var mı?
Y: yalıtıma gerek duyulmadığında hiçbir işlem yapılması gerekmez ve kapalı kalma süresi olmayacaktır. Yalıtım gerekliyse, duyurumız önerilen değiştirme boyutunu içerir. Değiştirme boyutunu seçmek, müşterilerimizin sanal makinelerini yeniden boyutlandırmasını gerektirir.  

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>S: yalıtılmış olmayan bir sanal makineye geçmek için herhangi bir maliyet Delta mı var?
Y **: Hayır**

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>S: diğer yalıtılmış boyutlar ne zaman devre dışı bırakılacak?
Y **: yalıtılmış** boyutun resmi kullanım dışı bırakılmasının sonunda 12 ay boyunca anımsatıcılar sağlıyoruz. En son duyurumuz Standard_G5, Standard_GS5, Standard_E64i_v3 ve Standard_E64i_v3 yalıtım özelliklerinin devre dışı bırakılması içerir.  

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>S: gümüş veya altın dayanıklılık katmanlarına bağlı bir Azure Service Fabric müşteriyim. Bu değişiklik beni etkiler mi?
Y **: Hayır**. Service Fabric [dayanıklılık katmanları](../articles/service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) tarafından belirtilen garantiler, bu değişiklikten sonra bile çalışmaya devam edecektir. Diğer nedenlerle fiziksel donanım yalıtımına ihtiyacınız varsa yukarıda açıklanan eylemlerden birini yapmanız gerekebilir. 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>S: D15_v2 veya DS15_v2 yalıtımı kullanımdan kaldırma için kilometre taşları nelerdir? 
Y: 
 
| Tarih | Eylem |
|---|---| 
| 15 Mayıs 2020<sup>1</sup> | D/DS15_v2 yalıtımı kullanımdan kaldırma duyurusu| 
| 15 Mayıs 2021 | D/DS15_v2 yalıtım garantisi kaldırıldı| 

<sup>1</sup> bu boyutları kullanan mevcut müşteri, sonraki adımlarda ayrıntılı yönergeler içeren bir duyuru e-postası alacaktır.  

### <a name="q-what-are-the-milestones-for-g5-gs5-e64i_v3-and-e64is_v3-isolation-retirement"></a>S: G5, Gs5, E64i_v3 ve E64is_v3 yalıtımı kullanımdan kaldırma için kilometre taşları nelerdir? 
Y: 
 
| Tarih | Eylem |
|---|---|
| 15 Şub, 2021<sup>1</sup> | G5/GS5/E64i_v3/E64is_v3 yalıtımı kullanımdan kaldırma duyurusu |
| 28 Şubat 2022 | G5/GS5/E64i_v3/E64is_v3 yalıtım garantisi kaldırıldı |

<sup>1</sup> bu boyutları kullanan mevcut müşteri, sonraki adımlarda ayrıntılı yönergeler içeren bir duyuru e-postası alacaktır.  

## <a name="next-steps"></a>Sonraki adımlar

Müşteriler Ayrıca, [iç içe geçmiş sanal makineler Için Azure desteği](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)'ni kullanarak bu yalıtılmış sanal makinelerin kaynaklarını daha fazla alt bölümlere ayırmak da tercih edebilir.
