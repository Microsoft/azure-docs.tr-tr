---
title: Azure Kubernetes hizmet kümesi kaynağı oluşturma
titleSuffix: Azure Cognitive Services
description: Azure Kubernetes hizmeti (AKS) kaynağı oluşturmayı öğrenin.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: 5e442f38a8e81170eeeac2f31f00a7d0eeb08d2b
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377388"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Azure Kubernetes hizmet kümesi kaynağı oluşturma

1. [Azure Kubernetes hizmetine](https://ms.portal.azure.com/#create/microsoft.aks)gidin ve **Oluştur**' u seçin.

1. **Temel bilgiler** sekmesinde, aşağıdaki bilgileri girin:

    |Ayar|Value|
    |--|--|
    |Subscription|Uygun bir abonelik seçin.|
    |Resource group|Kullanılabilir bir kaynak grubu seçin.|
    |Kubernetes küme adı|Bir ad girin (küçük harf).|
    |Bölge|Yakın bir konum seçin.|
    |Kubernetes sürümü|1.12.8 (varsayılan).|
    |DNS adı ön eki|Otomatik olarak oluşturulur, ancak geçersiz kılabilirsiniz.|
    |Düğüm boyutu|Standart DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Düğüm sayısı|Kaydırıcıyı varsayılan değerde bırakın.|

1. **Ölçek** sekmesinde **sanal düğümleri** ve **sanal makine ölçek kümelerini (Önizleme)** varsayılan değerlere ayarlayın.
1. **Kimlik doğrulama** sekmesinde **hizmet sorumlusu** ' nı bırakın ve **RBAC** kümesini varsayılan değerlere etkinleştirin.
1. **Ağ** sekmesinde, aşağıdaki seçimleri girin:

    |Ayar|Değer|
    |--|--|
    |HTTP uygulama yönlendirmesi|Hayır|
    |Ağ yapılandırması|Temel|

1. **İzleme** sekmesinde, **kapsayıcı izlemeyi etkinleştir** ' in **Evet**olarak ayarlandığından emin olun ve **Log Analytics çalışma alanını** varsayılan değer olarak bırakın.
1. **Etiketler** sekmesinde, ad/değer çiftlerini şimdilik boş bırakın.
1. **Gözden geçir ve Oluştur '** u seçin.
1. Doğrulama geçtikten sonra **Oluştur**' u seçin.

> [!NOTE]
> Doğrulama başarısız olursa, bunun nedeni "hizmet sorumlusu" hatası olabilir. **Kimlik doğrulama** sekmesine dönün ve ardından doğrulama ' nın ardından Pass ve sonra geçmesi gereken **İnceleme + oluştur**' a geri dönün.
