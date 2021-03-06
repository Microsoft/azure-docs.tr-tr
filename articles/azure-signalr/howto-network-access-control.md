---
title: Ağ erişim denetimini yapılandırma
titleSuffix: Azure SignalR Service
description: Azure SignalR hizmetiniz için ağ erişim denetimi 'ni yapılandırın.
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 24e56736e0d033420f9aaf976b0fb7d9727c1a5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92143226"
---
# <a name="configure-network-access-control"></a>Ağ erişim denetimini yapılandırma

Azure SignalR hizmeti, kullanılan ağların istek türüne ve alt kümesine bağlı olarak hizmet uç noktanıza erişim düzeyini sağlamanıza ve denetlemenize olanak sağlar. Ağ kuralları yapılandırıldığında, yalnızca belirtilen ağ kümesi üzerinde veri isteyen uygulamalar Azure SignalR hizmetinize erişebilir.

Azure SignalR hizmeti, internet üzerinden erişilebilen genel bir uç noktaya sahiptir. Ayrıca, [Azure SignalR hizmetiniz Için özel uç noktalar](howto-private-endpoints.md)oluşturabilirsiniz. Özel uç nokta, sanal ağınızdan Azure SignalR hizmetine özel bir IP adresi atar ve sanal ağınız ile Azure SignalR hizmeti arasındaki tüm trafiğin özel bir bağlantı üzerinden güvenliğini sağlar. Azure SignalR hizmeti ağ erişim denetimi, genel uç nokta ve özel uç noktalar için erişim denetimi sağlar.

İsteğe bağlı olarak, genel uç nokta ve her özel uç nokta için belirli istek türlerine izin vermeyi veya reddetme seçeneğini belirleyebilirsiniz. Örneğin, genel uç noktadan tüm [sunucu bağlantılarını](signalr-concept-internals.md#server-connections) engelleyebilir ve bunların yalnızca belirli bir sanal ağdan kaynaklandıklarından emin olabilirsiniz.

Ağ erişim denetimi kuralları etkin olduğunda Azure SignalR hizmetine erişen bir uygulama, istek için uygun yetkilendirme gerektirir.

## <a name="scenario-a---no-public-traffic"></a>Senaryo A-ortak trafik yok

Tüm genel trafiği tamamen reddetmek için, önce genel ağ kuralını istek türü olmadan izin verecek şekilde yapılandırmanız gerekir. Ardından, belirli sanal ağlardan gelen trafiğe erişim veren kurallar yapılandırmanız gerekir. Bu yapılandırma, uygulamalarınız için güvenli bir ağ sınırı oluşturmanıza olanak sağlar.

## <a name="scenario-b---only-client-connections-from-public-network"></a>Senaryo B-yalnızca genel ağdan istemci bağlantıları

Bu senaryoda, genel ağ kuralını yalnızca ortak ağdan gelen [Istemci bağlantılarına](signalr-concept-internals.md#client-connections) izin verecek şekilde yapılandırabilirsiniz. Daha sonra özel ağ kurallarını, belirli bir sanal ağdan gelen diğer istek türlerine izin verecek şekilde yapılandırabilirsiniz. Bu yapılandırma, uygulama sunucularınızı ortak ağdan gizler ve uygulama sunucularınız ile Azure SignalR hizmeti arasında güvenli bağlantılar oluşturur.

## <a name="managing-network-access-control"></a>Ağ erişim denetimini yönetme

Azure SignalR hizmeti için ağ erişim denetimini Azure portal aracılığıyla yönetebilirsiniz.

### <a name="azure-portal"></a>Azure portalı

1. Güvenli hale getirmek istediğiniz Azure SignalR hizmetine gidin.

1. **Ağ erişim denetimi** adlı ayarlar menüsüne tıklayın.

    ![Portalda ağ ACL 'SI](media/howto-network-access-control/portal.png)

1. Varsayılan eylemi düzenlemek için **Izin ver/Reddet** düğmesine geçin.

    > [!TIP]
    > Varsayılan eylem, hiçbir ACL kuralı eşleşmesi olmadığında gerçekleştirilecek eylemdir. Örneğin **, varsayılan eylem reddedilirse, aşağıdaki** açıkça onaylanmamış istek türleri reddedilir.

1. Ortak ağ kuralını düzenlemek için **ortak ağ** altında izin verilen istek türleri ' ni seçin.

    ![Portalda ortak ağ ACL 'sini düzenleme ](media/howto-network-access-control/portal-public-network.png)

1. Özel uç nokta ağ kurallarını düzenlemek için, **Özel uç nokta bağlantıları** altındaki her satırda izin verilen istek türlerini seçin.

    ![Portalda özel uç nokta ACL 'sini düzenleme ](media/howto-network-access-control/portal-private-endpoint.png)

1. Değişikliklerinizi uygulamak için **Kaydet**’e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

[Azure özel bağlantısı](../private-link/private-link-overview.md)hakkında daha fazla bilgi edinin.