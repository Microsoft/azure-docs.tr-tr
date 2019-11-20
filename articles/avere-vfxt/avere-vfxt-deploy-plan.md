---
title: Avere vFXT sisteminizi planlayın-Azure
description: Azure için avere vFXT dağıtılmadan önce yapılacak planlamayı açıklar
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 1317e900fd4448ded046ffea481313f8ea9f68e3
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256245"
---
# <a name="plan-your-avere-vfxt-system"></a>Avere vFXT sisteminizi planlayın

Bu makalede, Azure kümesi için gereksinimlerinize uygun şekilde yerleştirilmiş ve boyutlandırılabilen yeni bir avere vFXT 'nin nasıl planlanacağı açıklanır. 

Azure Market 'e geçmeden veya herhangi bir VM oluşturmadan önce, kümenin Azure 'daki diğer öğelerle nasıl etkileşime gireceğini göz önünde bulundurun. Küme kaynaklarının özel ağınızda ve alt ağlarında bulunacağı yeri planlayın ve arka uç depolamanın nerede olacağını belirleyin. Oluşturduğunuz küme düğümlerinin iş akışınızı desteklemeye yetecek kadar güçlü olduğundan emin olun. 

Daha fazla bilgi edinmek için okumaya devam edin.

## <a name="resource-group-and-network-infrastructure"></a>Kaynak grubu ve ağ altyapısı

Azure dağıtımı için avere vFXT 'nizin öğelerinin nerede olacağını göz önünde bulundurun. Aşağıdaki diyagramda Azure bileşenleri için avere vFXT için olası bir düzenleme gösterilmektedir:

![Küme denetleyicisinin ve küme VM 'lerinin bir alt ağ içinde gösterildiği diyagram. Alt ağ sınırının etrafında VNET sınırı vardır. VNET 'in içinde, depolama hizmeti uç noktasını temsil eden bir altıdır; VNET dışında bir BLOB depolama alanına kesik çizgili bir okla bağlanır.](media/avere-vfxt-components-option.png)

Avere vFXT sisteminizin ağ altyapısını planlarken aşağıdaki yönergeleri izleyin:

* Tüm öğeler, avere vFXT dağıtımı için oluşturulan yeni bir abonelikle yönetilmelidir. Avantajları şunlardır: 
  * Daha basit maliyet izleme-bir abonelikte kaynakların, altyapıdan ve işlem döngülerinin tüm maliyetlerini görüntüleyin ve denetleyin.
  * Daha kolay temizlik-proje ile işiniz bittiğinde aboneliğin tamamını kaldırabilirsiniz.
  * Kaynak kotalarına uygun bölümlendirme-tek bir abonelikte avere vFXT istemcilerini ve kümesini yalıtarak olası kaynak azaltmasından diğer kritik iş yüklerini koruyun. Bu, yüksek performanslı bilgi işlem iş akışı için çok sayıda istemci getirirken çakışmayı önler.

* VFXT kümesine yakın istemci işlem sistemlerinizi bulun. Arka uç depolama daha uzak olabilir.  

* VFXT kümesi ve küme denetleyicisi VM aynı kaynak grubunda aynı sanal ağda (VNet) bulunmalıdır ve aynı depolama hesabını kullanmalıdır. Otomatik küme oluşturma şablonu çoğu durum için bunu işler.

* IP adresinin istemcilerle veya işlem kaynaklarıyla çakışmasını önlemek için kümenin kendi alt ağında bulunması gerekir. 

* Küme oluşturma şablonu, kaynak grupları, sanal ağlar, alt ağlar ve depolama hesapları dahil olmak üzere küme için gereken altyapı kaynaklarının çoğunu oluşturabilir. Zaten var olan kaynakları kullanmak istiyorsanız, bu tablodaki gereksinimleri karşıladığından emin olun. 

  | Kaynak | Mevcut mi? | Gereksinimler |
  |----------|-----------|----------|
  | Kaynak grubu | Boşsa, Evet | Boş olmalıdır| 
  | Depolama hesabı | Küme oluşturulduktan sonra var olan bir blob kapsayıcısını bağlıyorsanız Evet <br/>  Küme oluşturma sırasında yeni bir blob kapsayıcısı oluşturulmaksam Hayır | Mevcut blob kapsayıcısı boş olmalıdır <br/> &nbsp; |
  | Sanal ağ | Evet | Yeni bir Azure Blob kapsayıcısı oluşturulana bir depolama hizmeti uç noktası içermelidir | 
  | Alt ağ | Evet |   |

## <a name="ip-address-requirements"></a>IP adresi gereksinimleri 

Kümenizin alt ağının, kümeyi desteklemek için yeterince büyük bir IP adresi aralığına sahip olduğundan emin olun. 

Avere vFXT kümesi aşağıdaki IP adreslerini kullanır:

* Tek bir küme yönetimi IP adresi. Bu adres, düğümden düğüme geçiş yapabilir, ancak avere Denetim Masası yapılandırma aracına bağlanabilmeniz için her zaman kullanılabilir.
* Her küme düğümü için:
  * En az bir istemciye yönelik IP adresi. (İstemciye yönelik tüm adresler kümenin *vServer*tarafından yönetilir ve bu, gerektiğinde bunları düğümler arasında taşıyabilir.)
  * Küme iletişimi için bir IP adresi
  * Bir örnek IP adresi (VM 'ye atandı)

Azure Blob depolama kullanırsanız, kümenizin VNET 'inizden IP adresleri de gerekebilir:  

* Azure Blob depolama hesabı en az beş IP adresi gerektirir. Blob depolamayı kümenizdeki aynı VNET 'te konumlandırdıysanız bu gereksinimi aklınızda bulundurun.
* Kümeniz için sanal ağ dışında bir Azure Blob depolama alanı kullanırsanız, VNET içinde bir depolama hizmeti uç noktası oluşturmanız gerekir. Bu uç nokta bir IP adresi kullanmaz.

Kümeden farklı kaynak gruplarında ağ kaynaklarını ve BLOB depolamayı (kullanılıyorsa) bulma seçeneğiniz vardır.

## <a name="vfxt-node-size"></a>vFXT düğüm boyutu

Küme düğümleri olarak görev yapan VM 'Ler, önbelleğinizin istek aktarım hızını ve depolama kapasitesini tespit eder. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Her vFXT düğümü aynı olacaktır. Diğer bir deyişle, üç düğümlü bir küme oluşturursanız, aynı türde ve boyutta üç sanal makine olur. 

| Örnek türü | vCPU | Bellek  | Yerel SSD depolaması  | En fazla veri diski | Önbelleğe alınmamış disk aktarım hızı | NIC (sayı) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51.200 ıOPS <br/> 768 MBps | 16.000 MBps (8)  |

Düğüm başına disk önbelleği yapılandırılabilir ve 1000 GB 'den 8000 GB 'a Rage olabilir. düğüm başına 4 TB, Standard_E32s_v3 düğümleri için önerilen önbellek boyutudur.

Bu VM 'Ler hakkında daha fazla bilgi için Microsoft Azure belgelerini okuyun:

* [Bellek için iyileştirilmiş sanal makine boyutları](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Hesap kotası

Aboneliğinizin avere vFXT kümesini çalıştırma kapasitesine ve kullanılan bilgi işlem veya istemci sistemlerine sahip olduğundan emin olun. Ayrıntılar için [vFXT kümesine yönelik kotayı](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) okuyun.

## <a name="back-end-data-storage"></a>Arka uç veri depolama

Avere vFXT kümesi önbellekte olmadığında verilerinizi nerede depolıyor? Çalışma kümesi 'nin, yeni bir blob kapsayıcısında veya var olan bir bulutta ya da donanım depolama sisteminde uzun vadede saklanıp saklanmayacağına karar verin. 

Arka uç için Azure Blob depolamayı kullanmak istiyorsanız, vFXT kümesi oluşturmanın bir parçası olarak yeni bir kapsayıcı oluşturmanız gerekir. Bu seçenek, yeni kapsayıcıyı oluşturup yapılandırıp yapılandırır, bu, küme kullanılabilir duruma geldiğinde kullanıma hazırdır. 

Ayrıntılar için [Azure Için avere vFXT oluşturma](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure) makalesini okuyun.

> [!NOTE]
> Avere vFXT sistemi için yalnızca boş BLOB depolama kapsayıcıları, çekirdek dosyasıs olarak kullanılabilir. VFXT, var olan verileri korumak gerekmeden nesne mağazasını yönetebilmelidir. 
>
> İstemci makinelerini ve avere vFXT önbelleğini kullanarak verilerin kümenin yeni kapsayıcısına etkin bir şekilde kopyalanmasını öğrenmek için [verileri vFXT kümesine taşımayı](avere-vfxt-data-ingest.md) okuyun.

Mevcut bir şirket içi depolama sistemini kullanmak istiyorsanız, oluşturulduktan sonra onu vFXT kümesine eklemeniz gerekir. Avere vFXT kümesine mevcut bir depolama sisteminin nasıl ekleneceği hakkında ayrıntılı yönergeler için [depolama yapılandırma](avere-vfxt-add-storage.md) bölümünü okuyun.

## <a name="cluster-access"></a>Küme erişimi 

Azure kümesi için avere vFXT özel bir alt ağda bulunur ve kümenin ortak bir IP adresi yoktur. Küme yönetimi ve istemci bağlantıları için özel alt ağa erişim yöntemlerinden biri olmalıdır. 

Erişim seçenekleri şunlardır:

* Ana bilgisayar-özel ağ içindeki ayrı bir VM 'ye genel bir IP adresi atayın ve küme düğümlerine bir SSL tüneli oluşturmak için bunu kullanın. 

  > [!TIP]
  > Küme denetleyicisinde genel bir IP adresi ayarlarsanız, bunu bir geçiş ana bilgisayarı olarak kullanabilirsiniz. Daha fazla bilgi için [küme denetleyicisini geçiş ana bilgisayarı olarak](#cluster-controller-as-jump-host) okuyun.

* Sanal özel ağ (VPN)-özel ağınıza bir noktadan siteye veya siteden siteye VPN yapılandırın.

* Azure ExpressRoute-bir ExpressRoute iş ortağı aracılığıyla özel bir bağlantı yapılandırın. 

Bu seçenekler hakkında daha fazla bilgi için [Internet iletişimi hakkındaki Azure sanal ağ belgelerini](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet)okuyun.

### <a name="cluster-controller-as-jump-host"></a>Küme denetleyicisini geçiş ana bilgisayarı olarak

Küme denetleyicisinde genel bir IP adresi ayarlarsanız, özel alt ağın dışından avere vFXT kümesiyle iletişim kurmak için bunu bir bağlantı ana bilgisayarı olarak kullanabilirsiniz. Ancak, denetleyici küme düğümlerini değiştirmek için erişim ayrıcalıklarına sahip olduğundan, bu küçük bir güvenlik riski oluşturur.  

Genel IP adresi olan bir denetleyicinin güvenliğini artırmak için, dağıtım betiği otomatik olarak, gelen erişimi yalnızca 22 numaralı bağlantı noktasına kısıtlayan bir ağ güvenlik grubu oluşturur. IP kaynak adreslerinize erişimi kilitleyerek, diğer bir deyişle, yalnızca küme erişimi için kullanmayı düşündüğünüz makinelerden gelen bağlantılara izin vererek sistemi daha da koruyabilirsiniz.

Kümeyi oluştururken, küme denetleyicisinde genel IP adresi oluşturulup oluşturulmayacağını seçebilirsiniz. 

* Yeni bir VNET veya yeni bir alt ağ oluşturursanız, küme denetleyicisine bir genel IP adresi atanır.
* Var olan bir sanal ağı ve alt ağı seçerseniz, küme denetleyicisinin yalnızca özel IP adresleri olacaktır. 

## <a name="vm-access-roles"></a>VM erişim rolleri 

Azure, belirli görevleri gerçekleştirmek üzere küme VM 'Leri yetkilendirmek için [rol tabanlı erişim denetimi](../role-based-access-control/index.yml) 'ni (RBAC) kullanır. Örneğin, küme denetleyicisinin küme düğümü VM 'lerini oluşturmak ve yapılandırmak için yetkilendirmeye ihtiyacı vardır. Küme düğümlerinin IP adreslerini diğer küme düğümlerine atayabilmesi veya yeniden atayabilmeleri gerekir.

Avere vFXT sanal makineleri için iki yerleşik Azure rolü kullanılır: 

* Küme denetleyicisi yerleşik [avere katkıda](../role-based-access-control/built-in-roles.md#avere-contributor)bulunan rolünü kullanır. 
* Küme düğümleri yerleşik rol [avere işlecini](../role-based-access-control/built-in-roles.md#avere-operator) kullanır

Avere vFXT bileşenleri için erişim rollerini özelleştirmeniz gerekiyorsa, kendi rolünüzü tanımlamanız ve sonra bunları oluşturuldukları sırada VM 'lere atamanız gerekir. Dağıtım şablonunu Azure Marketi 'nde kullanamazsınız. Azure portal bir bileti açarak, [sisteminizle ilgili yardım alın](avere-vfxt-open-ticket.md)bölümünde açıklandığı gibi, Microsoft Müşteri Hizmetleri ve desteği 'ne başvurun. 

## <a name="next-step-understand-the-deployment-process"></a>Sonraki adım: dağıtım sürecini anlama

[Dağıtıma genel bakış](avere-vfxt-deploy-overview.md) , Azure sistemi Için avere vFXT oluşturmak ve verileri sunmaya hazırlamak için gereken tüm adımların büyük bir resmini sağlar.  