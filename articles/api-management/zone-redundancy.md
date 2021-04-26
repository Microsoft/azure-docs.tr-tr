---
title: Azure API Management için kullanılabilirlik alanı desteği
description: Bölge artıklığını etkinleştirerek bir bölgedeki Azure API Management hizmet örneğinizin dayanıklılığını geliştirmeyi öğrenin.
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 04/13/2021
ms.author: apimpm
ms.custom: references_regions
ms.openlocfilehash: 817aebab6af8de59071b5d767b24d15cf46d59d9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559287"
---
# <a name="availability-zone-support-for-azure-api-management"></a>Azure API Management için kullanılabilirlik alanı desteği 

Bu makalede, Azure portal kullanarak API Management örneğiniz için bölge yedekliliği nasıl etkinleştirileceği gösterilmektedir. [Bölge artıklığı](../availability-zones/az-overview.md#availability-zones) , belirli bir Azure bölgesindeki (konum) bir hizmet örneği için dayanıklılık ve yüksek kullanılabilirlik sağlar. Bölge yedekliliği ile, API Management örneğinizin (Yönetim API 'SI, geliştirici portalı, git yapılandırması) ağ geçidi ve denetim düzlemi, fiziksel olarak ayrılmış bölgelerdeki veri merkezlerinde çoğaltılır ve bu da bölge hatasına dayanıklı hale gelir. 

API Management, coğrafi olarak dağıtılan API tüketicileri tarafından algılanan istek gecikmesini azaltmaya ve bir bölgenin çevrimdışı olması durumunda ağ geçidi bileşeninin kullanılabilirliğini artırmasına yardımcı olan [çok bölgeli dağıtımları](api-management-howto-deploy-multi-region.md)da destekler. Bölgesel bir kesinti varsa ağ geçidi kullanılabilirliğini iyileştirmek için bir bölge içinde artıklık için kullanılabilirlik bölgelerinin birleşimi ve çok bölgeli dağıtımlar, API Management örneğinizin hem güvenilirliğini hem de performansını artırmaya yardımcı olur.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="supported-regions"></a>Desteklenen bölgeler

Bölge yedekliliği için API Management yapılandırma Şu anda aşağıdaki Azure bölgelerinde destekleniyor.

* Doğu Avustralya
* Güney Brezilya
* Orta Kanada
* Orta Hindistan
* Doğu ABD
* Doğu ABD 2
* Orta Fransa
* Doğu Japonya
* Orta Güney ABD
* Güneydoğu Asya
* Güney Birleşik Krallık
* Batı ABD 2
* Batı ABD 3

## <a name="prerequisites"></a>Önkoşullar

* Henüz bir API Management hizmet örneği oluşturmadıysanız, bkz. [API Management hizmet örneği oluşturma](get-started-create-service-instance.md). Premium hizmet katmanını seçin.
* API Management örneğiniz bir [Sanal ağda](api-management-using-with-vnet.md)dağıtılmışsa, bölge yedekliliği etkinleştirmeyi planladığınız yeni bir konumda bir sanal ağ, alt ağ ve genel IP adresi oluşturduğunuzdan emin olun.

## <a name="enable-zone-redundancy---portal"></a>Bölge artıklığı etkinleştirme-Portal

Portalda, API Management hizmetinize bir konum eklediğinizde veya var olan bir konumun yapılandırmasını güncelleştirdiğinizde, isteğe bağlı olarak bölge yedekliliği etkinleştirin.

1. Azure portal, API Management hizmetinize gidin ve menüden **konumlar** ' ı seçin.
1. Varolan bir konum seçin veya üstteki çubukta **+ Ekle** ' yi seçin. Konum, [kullanılabilirlik alanlarını desteklemelidir](#supported-regions).
1. Konumdaki ölçek **[birimi](upgrade-and-scale.md)** sayısını seçin.
1. **Kullanılabilirlik alanları**' nda bir veya daha fazla bölge seçin. Seçilen birim sayısı, kullanılabilirlik alanları arasında eşit olarak dağıtılmalıdır. Örneğin, 3 birim seçtiyseniz her bir bölgenin bir birim barındırdığı şekilde 3 bölge ' yi seçin.
1. API Management örneği bir [Sanal ağda](api-management-using-with-vnet.md)dağıtılmışsa, konumdaki sanal ağ ayarlarını yapılandırın. Konumda bulunan mevcut bir sanal ağ, alt ağ ve genel IP adresi seçin.
1. **Uygula** ' yı ve ardından **Kaydet**' i seçin.

:::image type="content" source="media/zone-redundancy/add-location-zones.png" alt-text="Bölge yedekliliğine olanak verme":::

> [!IMPORTANT]
> Kullanılabilirlik bölgelerini etkinleştirdiğinizde, eklediğinizde veya kaldırdığınızda, konumdaki genel IP adresi değişir. Ağ ayarlarına sahip bir bölgedeki kullanılabilirlik bölgelerini güncelleştirirken, daha önce ayarladığınız şekilde farklı bir genel IP adresi kaynağı yapılandırmanız gerekir.

> [!NOTE]
> Değişikliğin API Management örneğine uygulanması 15 ila 45 dakika sürebilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure API Management hizmet örneğini birden çok Azure bölgesine dağıtma](api-management-howto-deploy-multi-region.md)hakkında daha fazla bilgi edinin.
* Ayrıca, [Azure Resource Manager şablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-api-management-simple-zones)kullanarak bölge yedekliliği de etkinleştirebilirsiniz.
* [Kullanılabilirlik bölgelerini destekleyen Azure hizmetleri](../availability-zones/az-region.md)hakkında daha fazla bilgi edinin.
* Azure 'da [güvenilirlik](/azure/architecture/framework/resiliency/overview) için oluşturma hakkında daha fazla bilgi edinin.