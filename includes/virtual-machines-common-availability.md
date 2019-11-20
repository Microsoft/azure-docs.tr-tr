---
title: include dosyası
description: include dosyası
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 7ea4762684a41e06687adcca91b13872a0c9f740
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73897571"
---
Bu makalede, Azure sanal makinelerinin (VM 'Ler) kullanılabilirlik özelliklerine ilişkin bir genel bakış sunulmaktadır.

## <a name="high-availability"></a>Yüksek kullanılabilirlik

İş yükleri genellikle yüksek aktarım hızı, performans ve bir VM 'nin bir güncelleştirme ya da başka bir olay nedeniyle etkilenmemesi durumunda artıklık oluşturmak için farklı sanal makinelere yayılır. 

Azure 'un yüksek kullanılabilirlik elde etmek için sağladığı birkaç seçenek vardır. İlk olarak temel yapılar hakkında konuşalım. 

### <a name="availability-zones"></a>Kullanılabilirlik alanları

[Kullanılabilirlik alanları](../articles/availability-zones/az-overview.md) , sanal makinelerinizdeki uygulamaların ve verilerin kullanılabilirliğini korumak için sahip olduğunuz denetim düzeyini genişletir. Bir kullanılabilirlik alanı, bir Azure bölgesi içinde fiziksel olarak ayrı bir bölgedir. Desteklenen Azure bölgesi başına üç Kullanılabilirlik Alanları vardır. 

Her Kullanılabilirlik Alanı farklı bir güç kaynağı, ağ ve soğutma sistemine sahiptir. Bölgelerde çoğaltılan VM 'Leri kullanma çözümlerinizi tasarlayarak, uygulamalarınızı ve verilerinizi bir veri merkezi kaybından koruyabilirsiniz. Bir bölge tehlikeye girerse, çoğaltılan uygulamalar ve veriler başka bir bölgede anında kullanılabilir. 

![Kullanılabilirlik alanları](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Bir [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) veya [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) VM 'yi bir kullanılabilirlik alanına dağıtma hakkında daha fazla bilgi edinin.


### <a name="fault-domains"></a>Hata etki alanları

Hata etki alanı, ortak bir güç kaynağı ve ağ anahtarını paylaşan, şirket içi veri merkezindeki rafa benzer bir temel alınan donanım mantık grubudur. 

### <a name="update-domains"></a>Güncelleme etki alanları

Güncelleme etki alanı, bakımdan geçirilebilen ya da aynı anda yeniden başlatılabilen bir temel alınan donanım mantıksal grubudur. 

Bu yaklaşım, Azure platformu periyodik bakımdan geçirilirken uygulamanızın en az bir örneğinin her zaman çalışır durumda kalmasını sağlar. Yeniden başlatılan güncelleştirme etki alanlarının sırası bakım sırasında sırayla ilerlemeyebilir, ancak aynı anda yalnızca bir güncelleştirme etki alanı yeniden başlatılır.


## <a name="virtual-machines-scale-sets"></a>Sanal Makine Ölçek Kümeleri 

Azure sanal makine ölçek kümeleri, yük dengeli bir VM grubu oluşturmanızı ve yönetmenizi sağlar. Tanımlı bir zamanlamaya veya talebe yanıt olarak sanal makine örneği sayısı otomatik olarak artabilir ya da azalabilir. Ölçek Kümeleri, uygulamalarınız için yüksek kullanılabilirlik sağlar ve birçok VM 'yi merkezi olarak yönetmenize, yapılandırmanıza ve güncelleştirmenize olanak tanır. Yüksek oranda kullanılabilir bir uygulama sağlamak ve [% 99,95 Azure SLA 'sını](https://azure.microsoft.com/support/legal/sla/virtual-machines/)karşılamak için ölçek kümesi içinde iki veya daha fazla sanal makine oluşturulmasını öneririz. Ölçek kümesinin kendisi için herhangi bir maliyet yoktur, yalnızca oluşturduğunuz her sanal makine örneği için ödeme yaparsınız. Tek bir VM [Azure Premium SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd)kullanırken, planlanmamış bakım olayları için Azure SLA 'sı geçerlidir. Ölçek kümesindeki sanal makineler, veri merkezi kesintileri ve planlı ya da plansız bakım olayları nedeniyle kullanılabilirliği ve esnekliği arası kesintilere en üst düzeye çıkarmak için birden çok bölgeye ve hata etki alanına dağıtılabilir. Ölçek kümesindeki sanal makineler aynı zamanda tek bir kullanılabilirlik bölgesine veya bölgeye göre dağıtılabilir. Kullanılabilirlik alanı dağıtım seçenekleri, düzenleme moduna göre farklılık gösterebilir.

### <a name="preview-orchestration-mode-preview"></a>Önizleme: Orchestration modu Önizleme
Sanal Makine Ölçek Kümeleri, düzenleme modunu belirtmenize olanak tanır.  Sanal makine ölçek kümesi düzenleme modu (Önizleme) ile artık ölçek kümesinin, bir ölçek kümesi yapılandırma modeli dışında oluşturulan sanal makineleri veya örtük olarak oluşturulan sanal makine örneklerini belirleyip düzenleyemeyeceğini seçebilirsiniz yapılandırma modeline göre. VM Orchestration modelinin, bir bölgede veya bir kullanılabilirlik alanında açıkça tanımlanmış sanal makineleri gruplamanıza izin verdiğini düzenleme modunu seçin. Bir kullanılabilirlik alanında dağıtılan sanal makineler, VM 'lere göre daha fazla yalıtım sağlar ve bölgesel bölge sınırlarına bağlanır ve bölgedeki diğer kullanılabilirlik bölgesinde oluşabilecek hatalara tabi değildir. 

|   | "orchestrationMode": "VM" (VirtualMachine)| "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|----|----|----|
| VM yapılandırma modeli| Yok. VirtualMachineProfile, ölçek kümesi modelinde tanımsızdır. | Gereklidir. VirtualMachineProfile, ölçek kümesi modelinde doldurulur. |
| Ölçek kümesine yeni VM ekleniyor| VM oluşturulduğunda, sanal makineler ölçek kümesine açıkça eklenir. | VM 'Ler örtük olarak oluşturulur ve VM yapılandırma modeli, örnek sayısı ve otomatik ölçeklendirme kuralları temelinde ölçek kümesine eklenir. |
| Kullanılabilirlik Alanları| Tek bir kullanılabilirlik bölgesindeki bölgesel dağıtımı veya VM 'Leri destekler| Bölgesel dağıtımı veya birden çok Kullanılabilirlik Alanları destekler; Bölge Dengeleme stratejisini tanımlayabilir |
| Hata etki alanları| , Hata etki alanı sayısını tanımlayabilir. 2 veya 3 kullanılabilirlik alanı için bölgesel destek ve 5 ' i temel alır. Atanan VM hata etki alanı, serbest bırakma ve yeniden başlatma dahil olmak üzere VM yaşam döngüsüyle kalır. | , ZGen olmayan dağıtımlar için 1, 2 veya 3 hata etki alanı tanımlayabilir ve kullanılabilirlik alanı dağıtımları için 5 ' i kullanabilirsiniz. Atanan VM hata etki alanı VM yaşam döngüsüyle kalıcı olmadığından, sanal makinelere ayırma sırasında bir hata etki alanı atanır. |
| Güncelleme etki alanları| Yok. Güncelleştirme etki alanları hata etki alanlarına otomatik olarak eşlenir| Yok. Güncelleştirme etki alanları hata etki alanlarına otomatik olarak eşlenir |

**Hata etki alanları ve güncelleştirme etki alanları**

Sanal Makine Ölçek Kümeleri, hata etki alanlarını ve güncelleştirme etki alanlarını hizalayarak yüksek kullanılabilirlik için tasarlamayı basitleştirir. Ölçek kümesi için yalnızca hata etki alanı sayısı tanımlamanız gerekir. Ölçek kümelerinin kullanabildiği hata etki alanlarının sayısı bölgeye göre farklılık gösterebilir. Bkz. [bölge başına hata etki alanı sayısı](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#number-of-fault-domains-per-region).


## <a name="availability-sets"></a>Kullanılabilirlik kümeleri
Kullanılabilirlik kümesi, Azure 'un, uygulamanızın artıklık ve kullanılabilirlik sağlamak üzere nasıl oluşturulduğunu anlamasına olanak tanıyan bir veri merkezi içindeki VM 'lerin mantıksal bir gruplandırmasıdır. Yüksek oranda kullanılabilir bir uygulama sağlamak ve [% 99,95 Azure SLA 'sını](https://azure.microsoft.com/support/legal/sla/virtual-machines/)karşılamak için bir kullanılabilirlik kümesi içinde iki veya daha fazla sanal makine oluşturulmasını öneririz. Kullanılabilirlik kümesinin kendisi için herhangi bir maliyet yoktur, yalnızca oluşturduğunuz her sanal makine örneği için ödeme yaparsınız. Tek bir VM [Azure Premium SSD](../articles/virtual-machines/windows/disks-types.md#premium-ssd)kullanırken, planlanmamış bakım olayları için Azure SLA 'sı geçerlidir.

Bir kullanılabilirlik kümesinde VM 'Ler bu hata etki alanlarına otomatik olarak dağıtılır. Bu yaklaşım, olası fiziksel donanım hatalarının, ağ kesintilerinin veya güç kesintilerinin etkisini sınırlar.

[Azure Yönetilen Diskler](../articles/virtual-machines/windows/faq-for-disks.md)’i kullanan sanal makineler, yönetilen kullanılabilirlik kümesi kullanılırken yönetilen disk hata etki alanları ile hizalanır. Bu hizalama, bir VM'ye bağlı tüm yönetilen disklerin, aynı yönetilen disk hata etki alanı içinde olmasını sağlar. 

Yönetilen bir kullanılabilirlik kümesinde yalnızca, yönetilen disklere sahip VM’ler oluşturulabilir. Yönetilen disk hata etki alanlarının sayısı bölgeye göre farklılık gösterir (bölge başına iki ya da üç yönetilen disk hata etki alanı). [Linux VM 'leri](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) veya [Windows VM 'leri](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set)için bu yönetilen disk hata etki alanları hakkında daha fazla bilgi edinebilirsiniz.

![Yönetilen kullanılabilirlik kümesi](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


Bir kullanılabilirlik kümesindeki VM 'Ler Ayrıca güncelleştirme etki alanları arasında otomatik olarak dağıtılır. 

![Kullanılabilirlik kümeleri](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>Sonraki adımlar
Azure ortamınızı oluşturmak için bu kullanılabilirlik ve yedeklilik özelliklerini kullanmaya başlayabilirsiniz. En iyi uygulama bilgileri için bkz. [Azure kullanılabilirlik en iyi uygulamaları](/azure/architecture/checklist/resiliency-per-service).

