---
title: Azure API Management şirket içinde barındırılan ağ geçidi sağlama | Microsoft Docs
description: Azure API Management 'de şirket içinde barındırılan bir ağ geçidini sağlamayı öğrenin.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: d33c5f75234ad7165a9062ecc3bb2a00d502f8c3
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075282"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Azure API Management şirket içinde barındırılan ağ geçidi sağlama

Azure API Management Örneğinizde bir ağ geçidi kaynağı sağlama, şirket içinde barındırılan bir ağ geçidini dağıtmak için bir önkoşuldur. Bu makalede, API Management bir ağ geçidi kaynağı sağlama adımları gösterilmektedir.

> [!NOTE]
> Şirket içinde barındırılan ağ geçidi özelliği önizlemededir. Önizleme sırasında, şirket içinde barındırılan ağ geçidi, yalnızca geliştirici ve Premium katmanlarda ek ücret ödemeden kullanılabilir. Geliştirici katmanı, tek bir şirket içinde barındırılan ağ geçidi dağıtımıyla sınırlıdır.

## <a name="prerequisites"></a>Önkoşullar

Şu hızlı başlangıcı tamamlayın: [Azure API Management örneği oluşturma](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>Şirket içinde barındırılan ağ geçidini sağlama

1. **Ayarlar**altında **ağ geçitlerini** seçin.
2. **+ Ekle**'ye tıklayın.
3. Ağ geçidinin **adını** ve **bölgesini** girin.
> [!TIP]
> **Bölge** , bu ağ geçidi kaynağıyla ilişkilendirilecek ağ geçidi düğümlerinin amaçlanan konumunu belirtir. Bu, herhangi bir Azure kaynağıyla ilişkili benzer bir özelliğe anlam olarak eşdeğerdir, ancak rastgele bir dize değeri atanabilir.

4. İsteğe bağlı olarak, ağ geçidi kaynağı için bir **Açıklama** girin.
5. İsteğe bağlı olarak, bu ağ geçidi kaynağıyla bir veya daha fazla API ilişkilendirmek için **API 'ler** altında **+** seçin.
> [!TIP]
> API 'nin **Ayarlar** sekmesindeki bir ağ GEÇIDIYLE bir API 'yi ilişkilendirebilir ve silebilirsiniz.

> [!IMPORTANT]
> Varsayılan olarak, mevcut API 'lerden hiçbiri yeni ağ geçidi kaynağıyla ilişkilendirilecektir. Bu nedenle, bunları yeni ağ geçidi aracılığıyla çağırma girişimleri `404 Resource Not Found` yanıtlara neden olur.

6. **Ekle**'ye tıklayın.

Ağ Geçidi kaynağı artık API Management örneğiniz tarafından sağlandı. Ağ geçidini dağıtmaya devam edebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* Şirket içinde barındırılan ağ geçidi hakkında daha fazla bilgi edinmek için bkz. [Azure API Management Self-barındırılan ağ geçidine genel bakış](self-hosted-gateway-overview.md)
* [Şirket içinde barındırılan ağ geçidini Kubernetes 'e dağıtma](api-management-howto-deploy-self-hosted-gateway-to-k8s.md) hakkında daha fazla bilgi edinin
* [Şirket içinde barındırılan bir ağ geçidini Docker 'A dağıtma](api-management-howto-deploy-self-hosted-gateway-to-docker.md) hakkında daha fazla bilgi edinin
