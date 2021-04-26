---
title: 'Azure ön kapısı: uç nokta Yöneticisi'
description: Bu makalede, Azure ön kapılı uç nokta Yöneticisi 'ne genel bakış sunulmaktadır.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: c916be9a54d62e16f488c94f4fa88a2207fb8788
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101100538"
---
# <a name="what-is-azure-front-door-standardpremium-preview-endpoint-manager"></a>Azure ön kapı Standart/Premium (Önizleme) uç nokta Yöneticisi nedir?

> [!NOTE]
> * Bu belge, Azure ön kapısının Standart/Premium (Önizleme) içindir. Azure ön kapısı hakkında bilgi mi arıyorsunuz? [Azure ön kapı belgelerini](../front-door-overview.md)görüntüleyin.

Endpoint Manager, Azure ön kapılarınız için yapılandırdığınız uç noktalara genel bir bakış sağlar. Uç nokta, etki alanlarının ve bunlarla ilişkili yapılandırmaların mantıksal gruplandırmasıdır. Endpoint Manager, CRUD (oluşturma, okuma, güncelleştirme ve silme) işlemi için uç nokta koleksiyonunuzu yönetmenize yardımcı olur. Uç noktalarınız için Endpoint Manager aracılığıyla aşağıdaki öğeleri yönetebilirsiniz:

* Etki Alanları
* Kaynak grupları
* Yollar
* Güvenlik

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-1.png" alt-text="Yapılandırma olmadan uç nokta Yöneticisi ekran görüntüsü." lightbox="../media/concept-endpoint-manager/endpoint-manager-1-expanded.png":::

Endpoint Manager bir uç nokta içinde her bir öğenin kaç örneğinin oluşturulduğunu listeler. Her öğe için ilişkilendirme durumu da görüntülenir. Örneğin, birden çok etki alanı ve kaynak grubu oluşturabilir ve bu ilişkiyi farklı yollarla aralarında atayabilirsiniz.

> [!IMPORTANT]
> * Azure ön kapı Standart/Premium (Önizleme) Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [**Microsoft Azure önizlemeleri Için ek kullanım koşulları**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="linked-view"></a>Bağlantılı görünüm

Endpoint Manager 'daki bağlantılı görünüm ile Azure ön kapılarınız arasındaki ilişkiyi kolayca tanımlayabilir, örneğin:

* Geçerli uç noktayla hangi etki alanları ilişkilendirilmiş?
* Hangi kaynak grubu hangi etki alanıyla ilişkili?
* Hangi WAF ilkesi hangi etki alanıyla ilişkili?

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-2.png" alt-text="Yapılandırma ile Endpoint Manager ekran görüntüsü." lightbox="../media/concept-endpoint-manager/endpoint-manager-2-expanded.png":::

## <a name="next-steps"></a>Sonraki Adımlar

[Ön kapı Standart/Premium oluşturmayı](create-front-door-portal.md)öğrenin.
