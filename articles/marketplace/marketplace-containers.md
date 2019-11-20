---
title: Azure Marketi için kapsayıcılar teklif yayımlama Kılavuzu
description: Bu makalede, Market 'te kapsayıcıları yayımlama gereksinimleri açıklanır
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: ffdc6b22209c1d334ac7b75c7079e755a7405154
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823005"
---
# <a name="containers-offer-publishing-guide"></a>Kapsayıcılar teklif yayımlama Kılavuzu

Kapsayıcı, kapsayıcı görüntünüzü Azure Marketi 'Nde yayımlamanıza yardımcı olur. Bu teklifin gereksinimlerini anlamak için bu kılavuzu kullanın. 

Bunlar Market aracılığıyla dağıtılan ve faturalandırılan işlem teklifleridir. Kullanıcının gördüğü eyleme yapılan çağrı "Şimdi al" dır.

Çözümünüz, Kubernetes tabanlı bir Azure Container Service olarak sağlanan bir Docker kapsayıcı görüntüsü olduğunda kapsayıcı teklif türünü kullanın.

>[!NOTE]
>Örneğin, Azure Kubernetes hizmeti veya Azure Container Instances gibi bir Kubernetes tabanlı Azure kapsayıcı hizmeti olan bir Kubernetes tabanlı kapsayıcı çalışma zamanı için Azure müşterilerinin seçimi.  

Microsoft şu anda ücretsiz ve kendi lisansını getir (KLG) lisanslama modellerini desteklemektedir.

## <a name="containers-offer"></a>Kapsayıcılar teklifi

| Gereksinim | Ayrıntılar |  
|:--- |:--- |  
| Faturalandırma ve ölçüm | Ücretsiz veya KLG faturalandırma modelini destekler. |  
| Dockerfile 'dan oluşturulan resim | Kapsayıcı görüntülerinin Docker görüntü belirtimine dayalı olması ve bir Dockerfile 'dan oluşturulması gerekir.<ul> <li>Docker görüntülerini oluşturma hakkında daha fazla bilgi için, [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage)adresinde bulunan kullanım bölümünü ziyaret edin.</li> </ul> |  
| ACR 'de barındırma | Kapsayıcı görüntülerinin Azure Container Registry (ACR) deposunda barındırılması gerekir.<ul> <li>ACR ile çalışma hakkında daha fazla bilgi için, [docs.Microsoft.com/Azure/Container-Registry/Container-Registry-Get-Started-Portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal)adresinde bulunan Azure Portal sayfasını kullanarak bir kapsayıcı kayıt defteri oluşturma hızlı başlangıç sayfasını ziyaret edin.</li> </ul> |  
| Resim etiketleme | Kapsayıcı görüntülerinin en az 1 etiket içermesi gerekir (en fazla etiket: 16).<ul> <li>Bir görüntüyü etiketleme hakkında daha fazla bilgi için, [docs.Docker.com/Engine/Reference/CommandLine/Tag](https://docs.docker.com/engine/reference/commandline/tag)adresinde bulunan Docker etiketi sayfasını ziyaret edin.</li> </ul> |  

## <a name="next-steps"></a>Sonraki adımlar

Daha önce yapmadıysanız, 

- Market 'e [kaydolun](https://azuremarketplace.microsoft.com/sell) .

Kaydolduysanız ve yeni bir teklif oluşturuyor ya da mevcut bir teklif üzerinde çalışıyorsanız,

- Teklifinizin oluşturulması veya tamamlanabilmesi için [bulut iş ortağı portalı oturum açın](https://cloudpartner.azure.com) .
- Daha fazla bilgi için bkz. [kapsayıcılar](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) .
