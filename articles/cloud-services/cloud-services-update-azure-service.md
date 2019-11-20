---
title: Bulut hizmetini güncelleştirme | Microsoft Docs
description: Azure 'da Cloud Services 'ı güncelleştirmeyi öğrenin. Bulut hizmetindeki bir güncelleştirmenin kullanılabilirliği güvence altına almak için nasıl devam eden hakkında bilgi edinin.
services: cloud-services
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: gwallace
ms.openlocfilehash: ae9d124391a1b17187ca98964874f681352498da
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945346"
---
# <a name="how-to-update-a-cloud-service"></a>Bulut hizmetini güncelleştirme

Hem rollerinin hem de konuk işletim sisteminin dahil olduğu bir bulut hizmetini güncelleştirmek, üç adımlı bir işlemdir. İlk olarak, yeni bulut hizmeti veya işletim sistemi sürümü için ikili dosyalar ve yapılandırma dosyaları karşıya yüklenmelidir. Daha sonra Azure, yeni bulut hizmeti sürümünün gereksinimlerine bağlı olarak bulut hizmeti için işlem ve ağ kaynaklarını ayırır. Son olarak, Azure 'un kullanılabilirliği korurken kiracıyı yeni sürüme veya Konuk işletim sistemine artımlı olarak güncellemek için sıralı bir yükseltme gerçekleştirir. Bu makalede, bu son adımın ayrıntıları (sıralı yükseltme) açıklanmaktadır.

## <a name="update-an-azure-service"></a>Azure hizmetini güncelleştirme
Azure, rol örneklerinizi yükseltme etki alanları (UD) adlı mantıksal gruplandırmalar halinde düzenler. Yükseltme etki alanları (UD), Grup olarak güncellenen rol örneklerinin mantıksal kümeleridir.  Azure, bir tek seferde bir UD hizmeti güncelleyen ve diğer UDs 'deki örneklerin trafiğe hizmet vermeye devam etmesine olanak tanır.

Varsayılan yükseltme etki alanı sayısı 5 ' tir. Hizmetin tanım dosyasına (. csdef) upgradeDomainCount özniteliğini ekleyerek, farklı sayıda yükseltme etki alanı belirtebilirsiniz. UpgradeDomainCount özniteliği hakkında daha fazla bilgi için bkz. [Azure Cloud Services Tanım Şeması (. csdef dosyası)](https://docs.microsoft.com/azure/cloud-services/schema-csdef-file).

Hizmetinizdeki bir veya daha fazla rolün yerinde güncelleştirilmesini gerçekleştirdiğinizde, Azure Updates, ait oldukları yükseltme etki alanına göre rol örnekleri kümesi sağlar. Azure, belirli bir yükseltme etki alanındaki tüm örnekleri güncelliyor, bunları güncelleştirirken, bunları çevrimiçi olarak geri getiren bir sonraki etki alanına taşınıyor. Azure, yalnızca geçerli yükseltme etki alanında çalışan örnekleri durdurarak, çalışan hizmete en az etkisi olan bir güncelleştirmenin meydana geldiğinden emin olur. Daha fazla bilgi için bu makalenin sonraki kısımlarında bulunan [güncelleştirme](#howanupgradeproceeds) bölümüne bakın.

> [!NOTE]
> Bu koşulların **güncelleştirilmesi** ve **yükseltmesinin** Azure bağlamında biraz farklı anlamı olduğundan, bu belgedeki özelliklerin süreçler ve açıklamaları için birbirlerinin yerine kullanılabilir.
>
>

Hizmetiniz, bu rolün kapalı kalma süresi olmadan yerinde güncelleştirilmesini sağlamak için bir rolün en az iki örneğini tanımlamalıdır. Hizmet bir rolün yalnızca bir örneğinden oluşuyorsa, hizmetiniz yerinde güncelleştirme tamamlanana kadar kullanılamaz.

Bu konu başlığı altında, Azure güncelleştirmeleriyle ilgili aşağıdaki bilgiler yer almaktadır:

* [Güncelleştirme sırasında izin verilen hizmet değişiklikleri](#AllowedChanges)
* [Bir yükseltmenin nasıl devam eder](#howanupgradeproceeds)
* [Bir güncelleştirmeyi geri alma](#RollbackofanUpdate)
* [Devam eden bir dağıtımda birden çok değiştirici işlem başlatılıyor](#multiplemutatingoperations)
* [Yükseltme etki alanları arasında rollerin dağıtılması](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>Güncelleştirme sırasında izin verilen hizmet değişiklikleri
Aşağıdaki tabloda, bir güncelleştirme sırasında bir hizmette izin verilen değişiklikler gösterilmektedir:

| Barındırma, hizmetler ve rollere izin verilen değişiklikler | Yerinde güncelleştirme | Hazırlandı (VIP takas) | Sil ve yeniden Dağıt |
| --- | --- | --- | --- |
| İşletim sistemi sürümü |Evet |Evet |Evet |
| .NET güven düzeyi |Evet |Evet |Evet |
| Sanal makine boyutu<sup>1</sup> |Evet<sup>2</sup> |Evet |Evet |
| Yerel depolama ayarları |Yalnızca<sup>2</sup> artır |Evet |Evet |
| Bir hizmette rol ekleme veya kaldırma |Evet |Evet |Evet |
| Belirli bir rolün örnek sayısı |Evet |Evet |Evet |
| Bir hizmet için uç nokta sayısı veya türü |Evet<sup>2</sup> |Hayır |Evet |
| Yapılandırma ayarlarının adları ve değerleri |Evet |Evet |Evet |
| Yapılandırma ayarlarının değerleri (ancak adları değil) |Evet |Evet |Evet |
| Yeni sertifika ekle |Evet |Evet |Evet |
| Mevcut sertifikaları Değiştir |Evet |Evet |Evet |
| Yeni kod dağıtma |Evet |Evet |Evet |

<sup>1</sup> Boyut değişikliği, bulut hizmeti için kullanılabilen boyutların alt kümesiyle sınırlıdır.

<sup>2</sup> Azure SDK 1,5 veya sonraki sürümleri gerektirir.

> [!WARNING]
> Sanal makine boyutunu değiştirmek, yerel verileri yok eder.
>
>

Güncelleştirme sırasında aşağıdaki öğeler desteklenmez:

* Rolün adını değiştirme. Rolü kaldırın ve yeni adla ekleyin.
* Yükseltme etki alanı sayısını değiştirme.
* Yerel kaynakların boyutunu azaltma.

Hizmetin tanımında yerel kaynak boyutunu azaltmak gibi başka güncelleştirmeler yapıyorsanız, bunun yerine bir VIP takas güncelleştirmesi gerçekleştirmeniz gerekir. Daha fazla bilgi için bkz. [değiştirme dağıtımı](/previous-versions/azure/reference/ee460814(v=azure.100)).

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>Bir yükseltmenin nasıl devam eder
Hizmetinizdeki tüm rolleri mi yoksa hizmette tek bir rol mi güncelleştirmek istediğinize karar verebilirsiniz. Her iki durumda da, yükseltilen ve ilk yükseltme etki alanına ait olan her rolün tüm örnekleri durdurulur, yükseltilir ve yeniden çevrimiçi hale getirilir. Yeniden çevrimiçi olduktan sonra, ikinci yükseltme etki alanındaki örnekler durdurulur, yükseltilir ve yeniden çevrimiçi hale getirilir. Bir bulut hizmetinde aynı anda en fazla bir yükseltme etkin olabilir. Yükseltme her zaman bulut hizmeti 'nin en son sürümüne karşı gerçekleştirilir.

Aşağıdaki diyagramda, hizmette tüm rolleri yükseltiyorsanız yükseltmenin nasıl devam eden gösterilmektedir:

![Hizmeti Yükselt](media/cloud-services-update-azure-service/IC345879.png "Hizmeti Yükselt")

Bu sonraki diyagramda, yalnızca tek bir rolü yükseltiyorsanız güncelleştirmenin nasıl devam eden gösterilmektedir:

![Rolü Yükselt](media/cloud-services-update-azure-service/IC345880.png "Rolü Yükselt")  

Otomatik güncelleştirme sırasında, Azure yapı denetleyicisi, bulut hizmetinin sistem durumunu düzenli aralıklarla değerlendirir ve bir sonraki UD 'nin ne kadar güvenli olduğunu tespit edin. Bu sistem durumu değerlendirmesi rol başına temelinde gerçekleştirilir ve yalnızca en son sürümdeki örnekleri (yani, zaten daha önceden eklenmiş olan UDs 'lerden örnekler) dikkate alır. Her rol için en az sayıda rol örneğinin, tatmin edici bir terminal durumu elde edildiğini doğrular.

### <a name="role-instance-start-timeout"></a>Rol örneği başlangıç zaman aşımı
Yapı denetleyicisi, her rol örneğinin başlatılmış bir duruma ulaşması için 30 dakika bekler. Zaman aşımı süresi aşmazsa, yapı denetleyicisi sonraki rol örneğine yürümeye devam eder.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>Bulut hizmeti yükseltmeleri sırasında veri sürücüsü etkisi

Bir hizmeti tek bir örnekten birden fazla örneğe yükseltirken, Azure yükseltme Hizmetleri 'nin bir yolu nedeniyle yükseltme gerçekleştirilirken hizmetiniz kapatılacak. Hizmet düzeyi sözleşmesi, hizmet kullanılabilirliğinin yalnızca birden fazla örnekle dağıtılan hizmetler için geçerli olmasını garanti altına alır. Aşağıdaki listede her bir sürücüdeki verilerin her bir Azure hizmeti yükseltme senaryosundan nasıl etkilendiği açıklanmaktadır:

|Senaryo|C sürücüsü|D sürücüsü|E sürücüsü|
|--------|-------|-------|-------|
|VM yeniden başlatma|Korunacağını|Korunacağını|Korunacağını|
|Portalın yeniden başlatılması|Korunacağını|Korunacağını|Yok|
|Portal yeniden görüntüsü|Korunacağını|Yok|Yok|
|Yerinde yükseltme|Korunacağını|Korunacağını|Yok|
|Düğüm geçişi|Yok|Yok|Yok|

Yukarıdaki listede, E: sürücüsünün rolün kök sürücüsünü temsil ettiğini ve sabit olarak kodlanmamadığını unutmayın. Bunun yerine, sürücüyü temsil etmek için **% Roleroot%** ortam değişkenini kullanın.

Tek örnekli bir hizmeti yükseltirken kapalı kalma süresini en aza indirmek için, hazırlama sunucusuna yeni bir çok örnekli hizmet dağıtın ve bir VIP takas işlemi gerçekleştirin.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Bir güncelleştirmeyi geri alma
Azure, ilk güncelleştirme isteği Azure yapı denetleyicisi tarafından kabul edildikten sonra bir hizmette ek işlemler başlatabilmeniz için bir güncelleştirme sırasında hizmetleri yönetme esnekliği sağlar. Geri alma işlemi, yalnızca bir güncelleştirme (yapılandırma değişikliği) veya yükseltmesi dağıtımda **devam ediyor** durumundaysa gerçekleştirilebilir. Bir güncelleştirme veya yükseltme, henüz yeni sürüme güncelleştirilmemiş en az bir hizmet örneği olduğu sürece devam ediyor olarak kabul edilir. Bir geri alma işleminin izin verip vermediğini test etmek için, [dağıtım alma](/previous-versions/azure/reference/ee460804(v=azure.100)) ve [bulut hizmeti özellikleri alma](/previous-versions/azure/reference/ee460806(v=azure.100)) işlemleri tarafından döndürülen RollbackAllowed bayrağının değerini denetleyin, true olarak ayarlanır.

> [!NOTE]
> Bu, yalnızca yerinde güncelleştirme veya yükseltme için geri alma işlemini çağırmak mantıklı **,** çünkü VIP takas yükseltmeleri, hizmetinizin çalışan bir örneğinin tamamını başka bir ile değiştirmeyi içerir.
>
>

Sürmekte olan bir güncelleştirmenin geri alınması, dağıtımda aşağıdaki etkilere sahiptir:

* Henüz güncelleştirilmemiş veya yeni sürüme yükseltilmemiş tüm rol örnekleri, bu örnekler hizmetin hedef sürümünü zaten çalıştırdığından, güncellenmez veya yükseltilmemiştir.
* Hizmet paketi (\*. cspkg) dosyasının veya hizmet yapılandırma (\*. cscfg) dosyasının (ya da her iki dosyanın) yeni sürümüne zaten güncelleştirilmiş veya yükseltilmiş olan herhangi bir rol örneği, bu dosyaların yükseltme öncesi sürümüne geri döndürülür.

Bu işlevsellik aşağıdaki özelliklerle sağlanır:

* Bir yapılandırma güncelleştirmesinde ( [değişiklik dağıtım yapılandırması](/previous-versions/azure/reference/ee460809(v=azure.100))çağırarak tetiklenir) veya bir yükseltmeden ( [yükseltme dağıtımı](/previous-versions/azure/reference/ee460793(v=azure.100))çağırarak tetiklenir) en az bir tane olduğu sürece çağrılabilecek [geri alma güncelleştirme veya yükseltme](/previous-versions/azure/reference/hh403977(v=azure.100)) işlemi. henüz yeni sürüme güncelleştirilmemiş olan hizmette örnek.
* [Dağıtımı al](/previous-versions/azure/reference/ee460804(v=azure.100)) ve [bulut hizmeti al özelliklerinin](/previous-versions/azure/reference/ee460806(v=azure.100)) yanıt gövdesinin bir parçası olarak döndürülen kilitli öğe ve RollbackAllowed öğesi:

  1. Kilitli öğe, belirli bir dağıtımda bir değiştirici işlemin ne zaman çağrılabileceğini tespit etmenize olanak tanır.
  2. RollbackAllowed öğesi, belirli bir dağıtımda [geri alma güncelleştirme veya yükseltme](/previous-versions/azure/reference/hh403977(v=azure.100)) işleminin ne zaman çağrılabilecek olduğunu tespit etmenize olanak tanır.

  Geri alma işlemi gerçekleştirmek için hem kilitli hem de RollbackAllowed öğelerini denetlemeniz gerekmez. RollbackAllowed 'in true olarak ayarlandığını onaylamak için yeterli. Bu öğeler yalnızca bu yöntemler, istek üst bilgisi "x-MS-Version:" olarak ayarlandığında döndürülür. 2011-10-01 "veya sonraki bir sürüm. Sürüm oluşturma üstbilgileri hakkında daha fazla bilgi için bkz. [hizmet yönetimi sürümü oluşturma](/previous-versions/azure/gg592580(v=azure.100)).

Bir güncelleştirme veya yükseltmenin geri alınma işleminin desteklenmediği bazı durumlar vardır, bunlar aşağıdaki gibidir:

* Yerel kaynaklarda azaltma-güncelleştirme, bir rolün yerel kaynaklarını artırdığı takdirde, Azure platformu geri almaya izin vermez.
* Kota sınırlamaları-güncelleştirme bir ölçek azaltma işlemi Ise, geri alma işlemini tamamlamaya yetecek işlem kotası artık olmayabilir. Her Azure aboneliğinin kendisiyle ilişkili bir kotası vardır ve bu aboneliğe ait olan tüm barındırılan hizmetler tarafından tüketilen maksimum çekirdek sayısını belirtir. Belirli bir güncelleştirmeyi geri alma işlemi gerçekleştirmek, aboneliğinizi kota üzerinden koyacağından, geri alma özelliği etkinleştirilmeyecektir.
* Yarış durumu-ilk güncelleştirme tamamlanırsa, geri alma mümkün değildir.

Bir güncelleştirmenin geri alınması, Azure barındırılan hizmetinize büyük bir yerinde yükseltmenin nasıl kullanıma alınacağı oranını denetlemek için el ile modunda [yükseltme dağıtımı](/previous-versions/azure/reference/ee460793(v=azure.100)) işlemini kullanıyorsanız yararlı olabilir.

Yükseltmenin dağıtımı sırasında, [yükseltme dağıtımını](/previous-versions/azure/reference/ee460793(v=azure.100)) el ile modda çağırır ve yükseltme etki alanlarını yürümeye başlayabilirsiniz. Eğer bir noktada, yükseltmeyi izlerken, inceleyebileceğiniz ilk yükseltme etki alanındaki bazı rol örnekleri yanıt vermemeye başlarsa, dağıtımda [geri alma güncelleştirme veya yükseltme](/previous-versions/azure/reference/hh403977(v=azure.100)) işlemini çağırabilirsiniz, bu da dokunulmadan bırakılır henüz yükseltilmemiş ve önceki hizmet paketine ve yapılandırmasına yükseltilen örnekleri geri almamış örnekler.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Devam eden bir dağıtımda birden çok değiştirici işlem başlatılıyor
Bazı durumlarda, devam eden bir dağıtımda birden çok eşzamanlı işlem başlatmak isteyebilirsiniz. Örneğin, bir hizmet güncelleştirmesi gerçekleştirebilir ve bu güncelleştirme hizmetiniz genelinde kullanıma sunulurken, örneğin güncelleştirmeyi geri almak, farklı bir güncelleştirme uygulamak veya dağıtımı silmek için bazı değişiklikler yapmak isteyebilirsiniz. Bir hizmet yükseltmesi, yükseltilen bir rol örneğinin sürekli kilitlenmesine neden olan bir önemlidir Code içeriyorsa bunun gerekli olabileceği bir durumdur. Bu durumda, yükseltilen etki alanındaki yetersiz sayıda örnek olduğundan, Azure yapı denetleyicisi bu yükseltmeyi uygulamak için ilerleme yapamayacak. Bu durum, *takılmış bir dağıtım*olarak adlandırılır. Güncelleştirmeyi geri alarak veya başarısız olan birinin üstüne yeni bir güncelleştirme uygulayarak dağıtımı kaldırabilirsiniz.

Hizmeti güncelleştirmek veya yükseltmek için ilk istek Azure Fabric denetleyicisi tarafından alındıktan sonra, sonraki değiştirici işlemleri başlatabilirsiniz. Diğer bir deyişle, başka bir değiştirici işleme başlamadan önce ilk işlemin tamamlanmasını beklemeniz gerekmez.

İlk güncelleştirme devam ederken ikinci bir güncelleştirme işlemi başlatmak geri alma işlemine benzer. İkinci güncelleştirme otomatik modda ise, ilk yükseltme etki alanı hemen yükseltilir, ancak aynı anda birden çok yükseltme etki alanındaki örneklere de bir kez çevrimdışı olur.

İşlem azaltma işlemleri aşağıdaki gibidir: [Dağıtım yapılandırmasını değiştirme](/previous-versions/azure/reference/ee460809(v=azure.100)), [dağıtımı yükseltme](/previous-versions/azure/reference/ee460793(v=azure.100)), [dağıtım durumunu güncelleştirme](/previous-versions/azure/reference/ee460808(v=azure.100)), [dağıtımı silme](/previous-versions/azure/reference/ee460815(v=azure.100))ve [geri alma güncelleştirme veya yükseltme](/previous-versions/azure/reference/hh403977(v=azure.100)).

İki işlem, [dağıtım](/previous-versions/azure/reference/ee460804(v=azure.100)) yapın ve [bulut hizmeti özelliklerini alın](/previous-versions/azure/reference/ee460806(v=azure.100)), belirli bir dağıtımda bir değiştirici işlemin çağrılabileceğini tespit etmek üzere incelenebilir kilitli bayrağını döndürün.

Kilitli bayrağını döndüren bu yöntemlerin sürümünü çağırmak için, istek üst bilgisini "x-MS-Version:" olarak ayarlamanız gerekir. 2011-10-01 "veya sonraki bir sürümü. Sürüm oluşturma üstbilgileri hakkında daha fazla bilgi için bkz. [hizmet yönetimi sürümü oluşturma](/previous-versions/azure/gg592580(v=azure.100)).

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Yükseltme etki alanları arasında rollerin dağıtılması
Azure, bir rolün örneklerini, hizmet tanımı (. csdef) dosyasının bir parçası olarak yapılandırılabilen bir dizi yükseltme etki alanı arasında eşit olarak dağıtır. En fazla yükseltme etki alanı sayısı 20 ' dir ve varsayılan değer 5 ' tir. Hizmet tanım dosyasının nasıl değiştirileceği hakkında daha fazla bilgi için bkz. [Azure hizmet tanımı şeması (. csdef dosyası)](cloud-services-model-and-package.md#csdef).

Örneğin, rolünüzde on örnek varsa, varsayılan olarak her bir yükseltme etki alanı iki örnek içerir. Rolünüzde 14 örnek varsa, yükseltme etki alanlarının dördü üç örnek içerir ve beşinci bir etki alanı iki içerir.

Yükseltme etki alanları sıfır tabanlı bir dizinle tanımlanır: ilk yükseltme etki alanının KIMLIĞI 0, ikinci yükseltme etki alanı ise KIMLIĞI 1 ' dir ve bu şekilde devam eder.

Aşağıdaki diyagramda, hizmet iki yükseltme etki alanını tanımladığında iki rol içeren bir hizmetin nasıl dağıtıldığı gösterilmektedir. Hizmet, Web rolünün sekiz örneğini ve çalışan rolünün dokuz örneğini çalıştırıyor.

![Yükseltme etki alanlarının dağılımı](media/cloud-services-update-azure-service/IC345533.png "Yükseltme etki alanlarının dağılımı")

> [!NOTE]
> Azure 'un, yükseltme etki alanları arasında örneklerin nasıl ayrıldığını kontrol ettiğine unutmayın. Hangi örneklerin hangi etki alanına ayrılacağını belirtmek mümkün değildir.
>
>

## <a name="next-steps"></a>Sonraki adımlar
[Cloud Services nasıl yönetilir?](cloud-services-how-to-manage-portal.md)  
[Cloud Services Izleme](cloud-services-how-to-monitor.md)  
[Cloud Services’ı Yapılandırma](cloud-services-how-to-configure-portal.md)  
