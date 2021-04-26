---
title: Azure ön kapı Standart/Premium 'daki özel bağlantıyla başlangıç sürecinizi güvenli hale getirme (Önizleme)
description: Bu sayfa, özel bağlantı kullanarak kaynağınıza bağlantı güvenliğini sağlama hakkında bilgi sağlar.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: tyao
ms.custom: references_regions
ms.openlocfilehash: c927682231bab1fc92f198c56a2f6aeff2f4c516
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553541"
---
# <a name="secure-your-origin-with-private-link-in-azure-front-door-standardpremium-preview"></a>Azure ön kapı Standart/Premium 'daki özel bağlantıyla başlangıç sürecinizi güvenli hale getirme (Önizleme)

> [!Note]
> Bu belge, Azure ön kapısının Standart/Premium (Önizleme) içindir. Azure ön kapısı hakkında bilgi mi arıyorsunuz? [Azure ön kapı belgelerini](../front-door-overview.md)görüntüleyin.

## <a name="overview"></a>Genel Bakış

[Azure özel bağlantısı](../../private-link/private-link-overview.md) , Azure PaaS hizmetlerine ve Azure 'da barındırılan hizmetlere, sanal ağınızdaki özel bir uç nokta üzerinden erişmenizi sağlar. Sanal ağınız ve hizmet arasındaki trafik, Microsoft omurga ağı üzerinden geçer ve genel İnternet’ten etkilenme olasılığı ortadan kaldırılır.

> [!IMPORTANT]
> Azure ön kapı Standart/Premium (Önizleme) Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure ön kapısı Premium SKU 'SU, özel bağlantı hizmeti aracılığıyla kaynağına bağlanabilir. Uygulamalarınız özel VNet 'iniz içinde veya Web uygulaması ve depolama hesabı gibi bir PaaS hizmetinin arkasında barındırılabilir ve bu nedenle, kaynağınıza genel olarak erişilebilmesini sağlayabilirsiniz.

:::image type="content" source="../media/concept-private-link/front-door-private-endpoint-architecture.png" alt-text="Ön kapı özel uç noktaları mimarisi":::

Azure ön kapısı Premium yapılandırmasında özel bağlantı noktasını etkinleştirdiğinizde, ön kapı, ön kapıdaki bölgesel özel ağdan sizin adınıza özel bir uç nokta oluşturur. Bu uç nokta Azure ön kapısı tarafından yönetiliyor. Kaynak üzerinde onay iletisi için bir Azure ön kapısı özel uç noktası isteği alacaksınız. İsteği onayladıktan sonra, ön kapıdan sanal ağdan özel bir IP adresi atanır, Azure ön kapısı ile başlangıç arasındaki trafik, Azure ağ omurgası ile belirlenen özel bağlantıyı alır. Azure ön kapıdan geldiği zaman, kaynağınıza gelen trafik artık güvenli hale getirilir.

:::image type="content" source="../media/concept-private-link/enable-private-endpoint.png" alt-text="Özel uç noktayı etkinleştir":::

> [!NOTE]
> Özel bağlantı kaynağını etkinleştirdikten ve özel uç nokta onayını onayladıktan sonra, bağlantının kurulması birkaç dakika sürer. Bu süre boyunca, kaynağa yönelik istekler bir ön kapı hata iletisi alır. Bağlantı kurulduktan sonra hata iletisi açılır.

## <a name="limitations"></a>Sınırlamalar

Azure ön kapı özel uç noktaları, genel önizleme sırasında şu bölgelerde kullanılabilir: Doğu ABD, Batı 2 ABD, Orta Güney ABD ve UK Güney.

En iyi gecikme süresi boyunca, ön kapı özel bağlantı uç noktasını etkinleştirmeyi seçerken, her zaman başlangıç noktanıza en yakın bir Azure bölgesi seçmeniz gerekir.

Azure ön kapı özel uç noktaları, platform tarafından ve Azure ön kapısının aboneliği kapsamında yönetilir. Azure ön kapısı, ön kapı profilini oluşturmak için kullanılan aynı müşteri aboneliğine yönelik özel bağlantı bağlantılarına izin verir.

## <a name="next-steps"></a>Sonraki adımlar

* Azure ön kapı Premium 'u özel bağlantı hizmeti aracılığıyla Web uygulamanıza bağlamak için bkz. [özel bir uç nokta kullanarak Web uygulamasına bağlanma](../../private-link/tutorial-private-endpoint-webapp-portal.md).
* Azure ön kapı Premium 'u özel bağlantı hizmeti aracılığıyla depolama hesabınıza bağlamak için bkz. [Özel uç nokta kullanarak depolama hesabına bağlanma](../../private-link/tutorial-private-endpoint-storage-portal.md).
