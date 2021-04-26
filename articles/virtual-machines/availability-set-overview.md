---
title: Kullanılabilirlik kümelerine genel bakış
description: Azure 'da kullanılabilirlik kümeleri hakkında bilgi edinin
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: b4e9f106354915fe40a4bcf9b60bc35443345202
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530575"
---
# <a name="availability-sets-overview"></a>Kullanılabilirlik kümelerine genel bakış

Bu makalede, Azure sanal makinelerinin (VM 'Ler) kullanılabilirlik özelliklerine ilişkin bir genel bakış sunulmaktadır.

## <a name="what-is-an-availability-set"></a>Kullanılabilirlik kümesi nedir? 

Kullanılabilirlik kümesi, Azure’un, uygulamanızın yedeklilik ve kullanılabilirlik sağlamak üzere nasıl oluşturulduğunu anlamasına olanak tanıyan bir mantıksal VM grubudur. Yüksek oranda kullanılabilir bir uygulama sağlamak ve [% 99,95 Azure SLA 'sını](https://azure.microsoft.com/support/legal/sla/virtual-machines/)karşılamak için bir kullanılabilirlik kümesi içinde iki veya daha fazla sanal makine oluşturulmasını öneririz. Kullanılabilirlik kümesinin kendisi için herhangi bir maliyet yoktur, yalnızca oluşturduğunuz her sanal makine örneği için ödeme yaparsınız.

## <a name="how-do-availability-sets-work"></a>Kullanılabilirlik kümeleri nasıl çalışır?
Kullanılabilirlik kümesindeki her bir sanal makineye, temel alınan Azure platformu tarafından bir **güncelleştirme etki alanı** ve bir **hata etki alanı** atanır. Her kullanılabilirlik kümesi, en çok üç hata etki alanı ve Yirmi güncelleştirme etki alanı ile yapılandırılabilir. Güncelleştirme etki alanları, sanal makinelerin ve temel alınan fiziksel donanımların aynı anda yeniden başlatılabilen gruplarını gösterir. Tek bir kullanılabilirlik kümesinde beşten fazla sanal makine yapılandırıldığında, altıncı sanal makine birinci sanal makine ile, yedinci sanal makine ikinci sanal makine ile aynı güncelleme etki alanına yerleştirilir ve yerleştirme işlemi bu düzende devam eder. Yeniden başlatılmakta olan güncelleme etki alanlarının sırası, planlanan bakım sırasında sıralı olarak uygulanmayabilir, ancak aynı anda yalnızca bir güncelleme etki alanı yeniden başlatılır. Yeniden başlatılmış bir güncelleme etki alanının kurtarılması için, farklı bir güncelleme etki alanında bakım başlatılmadan önce 30 dakika beklenir.

Hata etki alanları ortak bir güç kaynağı ve ağ anahtarını paylaşan sanal makine grubunu tanımlar. Varsayılan olarak, kullanılabilirlik kümesi içinde yapılandırılan sanal makineler, en çok üç hata etki alanı arasında ayrılır. Sanal makinelerinizin bir kullanılabilirlik kümesine yerleştirilmesi uygulamanızı işletim sistemine veya uygulamaya özel hatalardan korumasa da, olası fiziksel donanım hatalarının, ağ kesintilerinin veya güç kesintilerinin etkilerini sınırlar.

:::image type="content" source="./media/virtual-machines-common-manage-availability/ud-fd-configuration.png" alt-text="Çeşitli bilgi işlem kümelerinin hata etki alanlarına bölündüğü ve bu hata etki alanlarında birden çok güncelleştirme etki alanı olduğunu gösteren diyagram":::

VM 'Ler aynı zamanda disk hata etki alanları ile hizalanır. Bu hizalama, bir VM 'ye bağlı tüm yönetilen disklerin aynı hata etki alanları içinde olmasını sağlar. 

Yönetilen bir kullanılabilirlik kümesinde yalnızca, yönetilen disklere sahip VM’ler oluşturulabilir. Yönetilen disk hata etki alanlarının sayısı bölgeye göre farklılık gösterir (bölge başına iki ya da üç yönetilen disk hata etki alanı). 

:::image type="content" source="./media/virtual-machines-common-manage-availability/md-fd-updated.png" alt-text="Diskler ve VM 'Ler için hata etki alanlarının nasıl hizalandığını gösteren diyagram.":::

## <a name="next-steps"></a>Sonraki adımlar
Azure ortamınızı oluşturmak için bu kullanılabilirlik ve yedeklilik özelliklerini kullanmaya başlayabilirsiniz. En iyi uygulama bilgileri için bkz. [Azure kullanılabilirlik en iyi uygulamaları](/azure/architecture/checklist/resiliency-per-service).

