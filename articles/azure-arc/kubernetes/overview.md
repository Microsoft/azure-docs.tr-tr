---
title: Azure Arc etkin Kubernetes 'e genel bakış
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Bu makalede, Azure Arc etkin Kubernetes 'e genel bakış sunulmaktadır.
keywords: Kubernetes, yay, Azure, kapsayıcılar
ms.custom: references_regions
ms.openlocfilehash: 69e9886f214d0076c8e66231fd6ad15bb060828f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449657"
---
# <a name="what-is-azure-arc-enabled-kubernetes"></a>Azure Arc özellikli Kubernetes nedir?

Azure Arc etkin Kubernetes sayesinde, Azure içinde veya dışında bulunan Kubernetes kümelerini iliştirebilir ve yapılandırabilirsiniz. Bir Kubernetes kümesini Azure yaya bağladığınızda, şu şekilde olur:
* Azure Resource Manager KIMLIĞI ve yönetilen kimlik ile Azure portal görüntülenir. 
* , Bir Azure aboneliğine ve kaynak grubuna yerleştirilir.
* Diğer tüm Azure kaynakları gibi etiketleri alın. 

Bir Kubernetes kümesini Azure 'a bağlamak için, küme yöneticisinin aracıları dağıtması gerekir. Bu aracılar:
* `azure-arc`Kubernetes ad alanında standart Kubernetes dağıtımları olarak çalıştırın.
* Azure ile bağlantıyı işleyin.
* Azure Arc günlüklerini ve ölçümlerini toplayın.
* Yapılandırma isteklerini izleyin. 

Azure Arc etkin Kubernetes, transit verileri güvenli hale getirmek için sektör standardı SSL 'yi destekler. Ayrıca veriler, verilerin gizliliğini sağlamak için bir Azure Cosmos DB veritabanında şifreli olarak depolanır.

## <a name="supported-kubernetes-distributions"></a>Desteklenen Kubernetes dağıtımları

Azure Arc etkin Kubernetes, tüm bulut Yerel Bilgi Işlem altyapısı (CNCF) sertifikalı Kubernetes kümeleriyle birlikte çalışmaktadır. Azure Arc ekibi, Azure Arc etkinleştirilmiş Kubernetes ile Kubernetes dağıtımlarının uyumlu olduğunu [doğrulamak için temel sektör ortaklarıyla](./validation-program.md) çalıştık.

## <a name="supported-scenarios"></a>Desteklenen senaryolar 

Azure Arc etkin Kubernetes aşağıdaki senaryoları destekler: 

* Envanter, gruplama ve etiketleme için Azure dışında çalışan Kubernetes 'i bağlayın.

* Gilar tabanlı yapılandırma yönetimini kullanarak uygulamaları dağıtın ve yapılandırma uygulayın. 

* Kapsayıcılar için Azure Izleyici 'yi kullanarak kümelerinizi görüntüleyin ve izleyin.

* Kubernetes için Azure Defender 'ı kullanarak tehdit koruması zorlayın.

* Kubernetes için Azure Ilkesini kullanarak ilkeleri uygulayın.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Desteklenen bölgeler 

Şu bölgelerde Azure Arc etkin Kubernetes Şu anda destekleniyor: 

* Doğu ABD
* West Europe
* Orta Batı ABD
* Orta Güney ABD
* Güneydoğu Asya
* Güney Birleşik Krallık
* Batı ABD 2
* Doğu Avustralya
* Doğu ABD 2
* Kuzey Avrupa

## <a name="next-steps"></a>Sonraki adımlar

Bir kümeyi Azure yaya bağlamayı öğrenin.
> [!div class="nextstepaction"]
> [Bir kümeyi Azure yaya bağlama](./quickstart-connect-cluster.md)
