---
title: Azure uygulamaları yönetilen uygulama teklifi Yayımlama Kılavuzu
description: Bu makalede, Market 'te yönetilen bir uygulama yayımlama gereksinimleri açıklanır
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: qianw211
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/14/2018
ms.author: v-qiwe
ms.openlocfilehash: bdb2a54748c734d7b44dfa0b1f106d37e817f80e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822928"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Azure uygulamaları: yönetilen uygulama teklifi Yayımlama Kılavuzu

Yönetilen bir uygulama, Market 'te çözüm yayımlamanın ana yöntemlerinden biridir. Bu teklifin gereksinimlerini anlamak için bu kılavuzu kullanın. 

Bunlar Market aracılığıyla dağıtılan ve faturalandırılan işlem teklifleridir. Kullanıcının gördüğü eyleme yapılan çağrı "Şimdi al" dır.

Aşağıdaki koşullar gerektiğinde Azure uygulaması: yönetilen uygulama teklifi türünü kullanın:
- Bir VM veya tüm IaaS tabanlı bir çözümü kullanarak müşteriniz için abonelik tabanlı bir çözüm dağıtırsınız.
- Siz veya müşteriniz, çözümün bir iş ortağı tarafından yönetilmesini gerektirir.

>[!NOTE]
>Örneğin, bir iş ortağı bir sı veya yönetilen hizmet sağlayıcısı (MSP) olabilir.  

## <a name="managed-application-offer"></a>Yönetilen uygulama teklifi

|Gereksinimler |Ayrıntılar  |
|---------|---------|
|Müşterinin Azure aboneliğine dağıtılır | Yönetilen uygulamalar müşterinin aboneliğine dağıtılmalıdır ve üçüncü bir taraf tarafından yönetilebilir. | 
|Faturalandırma ve ölçüm    |  Kaynaklar, müşterinin Azure aboneliğinden temin edilir. Kullandıkça Öde (PAYGO) sanal makineleri, müşterinin Azure aboneliği (PAYGO) aracılığıyla faturalandırılır. <br> Kendi lisansını getir durumunda, Microsoft, müşteri aboneliğinde oluşan altyapı maliyetlerini faturalandırtıracağız, ancak yazılım lisans ücretlerinizi müşteriye doğrudan kullanacaksınız.        |
|Azure ile uyumlu sanal sabit disk (VHD)    |   VM 'Ler Windows veya Linux üzerinde oluşturulmalıdır.<ul> <ul> <li>Linux VHD oluşturma hakkında daha fazla bilgi için bkz. [Azure 'da desteklenen Linux dağıtımları](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Windows VHD oluşturma hakkında daha fazla bilgi için bkz. [Azure ile uyumlu bır VHD oluşturma](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |

>[!NOTE]
> Yönetilen uygulamalar Market üzerinden dağıtılabilir olmalıdır. Müşteri iletişimi sorun oluşturacaksa, müşteri adayı paylaşımını etkinleştirdikten sonra ilgilenen müşterilere ulaşmalısınız.  

>[!Note]
>Bulut çözümü sağlayıcıları (CSP) iş ortağı kanalı kabul etme artık kullanılabilir.  Teklifinizi Microsoft CSP iş ortağı kanalları aracılığıyla pazarlama hakkında daha fazla bilgi için lütfen bkz. [bulut çözümü sağlayıcıları](./cloud-solution-providers.md) .

## <a name="next-steps"></a>Sonraki adımlar
Daha önce yapmadıysanız, 

- Market 'e [kaydolun](https://azuremarketplace.microsoft.com/sell) .

Kaydolduysanız ve yeni bir teklif oluşturuyor ya da mevcut bir teklif üzerinde çalışıyorsanız,

- Teklifinizin oluşturulması veya tamamlanabilmesi için [bulut iş ortağı portalı oturum açın](https://cloudpartner.azure.com) .
