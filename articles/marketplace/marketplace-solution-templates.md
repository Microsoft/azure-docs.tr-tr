---
title: Azure uygulamaları çözüm şablonu teklif yayımlama Kılavuzu | Azure Marketi
description: Bu makalede, Azure Marketi 'nde bir çözüm şablonu yayımlama gereksinimleri açıklanmaktadır.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 9/25/2019
ms.author: ellacroi
ms.openlocfilehash: 934a5e050e190c9a1f90bb3a22c2d1323a3ccecf
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808293"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Azure uygulamaları: çözüm şablonu teklif yayımlama Kılavuzu

Çözüm şablonları, Market 'te çözüm yayımlamanın ana yöntemlerinden biridir. Bu teklifin gereksinimlerini anlamak için bu kılavuzu kullanın. 

Çözümünüz tek bir VM 'nin ötesinde ek dağıtım ve yapılandırma Otomasyonu gerektirdiğinde Azure uygulaması: çözüm şablonu teklif türünü kullanın. Azure uygulamaları kullanarak bir veya daha fazla VM 'nin sağlanmasını otomatikleştirebiliriz: çözüm şablonları. Ayrıca, ağ ve depolama kaynakları da sağlayabilirsiniz. Azure uygulamaları: çözüm şablonları teklif türü, tek VM 'Ler ve IaaS tabanlı çözümlerin tamamına yönelik otomasyon avantajları sağlar.

Bu çözüm şablonları işlem teklifleri değildir, ancak Microsoft 'un ticari Marketi aracılığıyla faturalandırılan ücretli VM tekliflerini dağıtmak için kullanılabilir. Kullanıcının gördüğü eyleme yapılan çağrı "Şimdi al" dır.


## <a name="requirements-for-solution-templates"></a>Çözüm şablonları için gereksinimler

| **Gereksinimler** | **Ayrıntılar**  |
| ---------------  | -----------  |
|Faturalandırma ve ölçüm    |  Kaynaklar, müşterinin Azure aboneliğinden temin edilir. Kullandıkça Öde (PAYGO) sanal makineleri, müşterinin Azure aboneliği (PAYGO) aracılığıyla faturalandırılır.  <br/> Kendi lisansını getir (KLG) durumunda Microsoft, müşteri aboneliğinde oluşan altyapı maliyetlerini faturalandırtıracaktır, ancak yazılım lisans ücretlerinizi müşteriye doğrudan Transact.   |
|Azure ile uyumlu sanal sabit disk (VHD)  |   VM 'Ler Windows veya Linux üzerinde oluşturulmalıdır.  Daha fazla bilgi için [bkz. Azure ile uyumlu bır VHD oluşturma](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md). |
| Müşteri kullanım Attributıon | Azure Marketi 'nde yayınlanan tüm çözüm şablonlarında müşteri kullanım attributıon özelliğinin etkinleştirilmesi gerekir. Müşteri kullanımı atımı ve nasıl etkinleştirileceği hakkında daha fazla bilgi için bkz. [Azure iş ortağı müşteri kullanımı atısyonu](./azure-partner-customer-usage-attribution.md).  |
| Yönetilen Diskleri kullanma | [Yönetilen diskler](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) , Azure 'Da IaaS VM 'lerinin kalıcı diskleri için varsayılan seçenektir. Çözüm şablonlarında yönetilen diskleri kullanmanız gerekir. <br> <br> 1. çözüm şablonlarınızı güncelleştirmek için Azure ARM şablonlarındaki yönetilen diskleri kullanmaya yönelik [yönergeleri](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) ve [örnekleri](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) izleyin. <br> <br> 2. VHD 'yi Market 'te görüntü olarak yayımlamak için yönetilen disklerin temel VHD 'sini bir depolama hesabına aktarmak üzere aşağıdaki yönergeleri izleyin: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Sonraki adımlar
Henüz yapmadıysanız Market 'e [kaydolun](https://azuremarketplace.microsoft.com/sell) .

Kaydolduysanız ve yeni bir teklif oluşturuyor ya da mevcut bir teklif üzerinde çalışıyorsanız, teklifinizi oluşturmak veya tamamlayabilmeniz için [bulut iş ortağı portalı](https://cloudpartner.azure.com) oturum açın.
