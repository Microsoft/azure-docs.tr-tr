---
title: Azure Market 'te kapsayıcı teklifleri için Yayımlama Kılavuzu
description: Bu makalede, Azure Marketi 'nde kapsayıcı tekliflerini yayımlama gereksinimleri açıklanmaktadır.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 11/30/2020
ms.openlocfilehash: 83c575aa40b80d9a8e39263e89a5e7860c8f8774
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95741670"
---
# <a name="publishing-guide-for-azure-container-offers"></a>Azure Container tekliflerini Yayımlama Kılavuzu

Azure Container, kapsayıcı görüntünüzü Azure Marketi 'Nde yayımlamanıza yardımcı olur. Bu teklif türü için gereksinimleri anlamak için bu kılavuzu kullanın.

Azure Container teklifleri, Azure Marketi aracılığıyla dağıtılan ve faturalandırılan işlem teklifleridir. Kullanıcının gördüğü listeleme seçeneği "Şimdi al" dır.

Çözümünüz bir Kubernetes tabanlı Azure Container örneği olarak ayarlanmış bir Docker kapsayıcı görüntüsü olduğunda, Azure Container teklif türünü kullanın.

> [!NOTE]
> Azure Container Instance, Azure 'da bir kapsayıcı çalıştırmanın en hızlı ve en basit yolu olan bir çalışma zamanı Docker örneğidir. Bu örnek, herhangi bir sanal makineyi yönetmek zorunda kalmadan ve daha yüksek düzeyde bir hizmeti benimsemeniz gerekmez. Kapsayıcı örnekleri doğrudan Azure 'a dağıtılabilir veya Azure Kubernetes Hizmetleri veya Azure Kubernetes hizmet altyapısı tarafından ayarlanabilir.  

Microsoft şu anda ücretsiz ve kendi lisansını getir (KLG) lisanslama modellerini desteklemektedir.

## <a name="container-offer-requirements"></a>Kapsayıcı teklif gereksinimleri

| Gereksinim | Ayrıntılar |  
|:--- |:--- |  
| Faturalandırma ve ölçüm | Ücretsiz veya KLG faturalandırma modelini destekler.<br><br> |  
| Dockerfile dosyasından oluşturulan resim | Kapsayıcı görüntülerinin Docker görüntü belirtimine dayalı olması ve Dockerfile 'dan oluşturulması gerekir.<br> <br>Docker görüntülerini oluşturma hakkında daha fazla bilgi için [Dockerfile başvurusunun](https://docs.docker.com/engine/reference/builder/#usage)"kullanım" bölümüne bakın.<br><br> |  
| Azure Container Registry deposunda barındırma | Kapsayıcı görüntülerinin bir Azure Container Registry deposunda barındırılması gerekir.<br> <br>Azure Container Registry ile çalışma hakkında daha fazla bilgi için bkz. [hızlı başlangıç: Azure Portal kullanarak özel kapsayıcı kayıt defteri oluşturma](../container-registry/container-registry-get-started-portal.md).<br><br> |  
| Resim etiketleme | Kapsayıcı görüntülerinin en az bir etiket içermesi gerekir (en fazla etiket sayısı: 16).<br><br>Bir görüntüyü etiketleme hakkında daha fazla bilgi için `docker tag` [Docker belgeleri](https://docs.docker.com/engine/reference/commandline/tag) sitesindeki sayfasına bakın.<br><br> |  

## <a name="next-steps"></a>Sonraki adımlar

- Bir kapsayıcı teklifi için teknik varlıkları hazırlamak üzere bkz. [Azure Container teknik varlıkları oluşturma](create-azure-container-technical-assets.md).

- Azure Container teklifi oluşturmak için daha fazla bilgi için bkz. [Azure Market 'te Azure Container teklifi oluşturma](create-azure-container-offer.md) .
