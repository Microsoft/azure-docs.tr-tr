---
title: Çoklu değer trafik yönlendirmeyi Yapılandırma-Azure Traffic Manager
description: Bu makalede, trafiği bir/AAAA uç noktalarına yönlendirmek üzere Traffic Manager nasıl yapılandırılacağı açıklanmaktadır.
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: allensu
ms.openlocfilehash: 3e5e6cb55b86df8a48f96771fb2436afa8acaa18
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040363"
---
# <a name="configure-multivalue-routing-method-in-traffic-manager"></a>Traffic Manager çoklu değer yönlendirme yöntemini yapılandırma

Bu makalede, çok değerli trafik yönlendirme yönteminin nasıl yapılandırılacağı açıklanır. Çoklu **değer** trafik yönlendirme yöntemi, birden çok sağlıklı uç nokta döndürmenizi sağlar ve istemcilerin başka bir DNS araması yapmak zorunda kalmadan yeniden denemek için daha fazla seçenek olduğundan uygulamanızın güvenilirliğini artırmaya yardımcı olur. Çoklu değer yönlendirme yalnızca IPv4 veya IPv6 adresleri kullanılarak belirtilen tüm uç noktalarına sahip profiller için etkindir. Bu profil için bir sorgu alındığında, tüm sağlıklı uç noktalar belirtilen yapılandırılabilir maksimum dönüş sayısına göre döndürülür. 

>[!NOTE]
> Şu anda, IPv4 veya IPv6 adreslerini kullanarak uç noktaları eklemek yalnızca **dış** türündeki uç noktalar için desteklenir ve bu nedenle çok değerli yönlendirme yalnızca söz konusu uç noktalar için desteklenir.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma 

https://portal.azure.com adresinden Azure portalında oturum açın.
## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma
Traffic Manager profili için bir kaynak grubu oluşturun.
1. Azure portal sol bölmesinde **kaynak grupları**' nı seçin.
2. **Kaynak grupları**' nda, sayfanın üst kısmında **Ekle**' yi seçin.
3. **Kaynak grubu adı**alanına bir ad *myResourceGroupTM1*yazın. **Kaynak grubu konumu**için **Doğu ABD**' yi seçin ve ardından **Tamam**' ı seçin.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager profili oluşturma
En düşük gecikme süresine sahip bir uç noktaya göndererek Kullanıcı trafiğini yönlendiren bir Traffic Manager profili oluşturun.

1. Ekranın sol üst tarafından **Kaynak oluştur** > **Ağ** > **Traffic Manager profili** > **Oluştur**'u seçin.
2. **Traffic Manager profili oluştur**' da, aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin ve **Oluştur**' u seçin:
    
    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | Ad                   | Bu adın trafficmanager.net bölgesinde benzersiz olması ve Traffic Manager profilinize erişmek için kullanılan trafficmanager.net DNS adı ile sonuçlanması gerekir.                                   |
    | Yönlendirme yöntemi          | **Çoklu değer** yönlendirme yöntemini seçin.                                       |
    | Abonelik            | Aboneliğinizi seçin.                          |
    | Kaynak grubu          | *MyResourceGroupTM1*öğesini seçin. |
    | Konum                | Bu ayar, kaynak grubunun konumunu ifade eder ve genel olarak dağıtılacak Traffic Manager profilini etkilemez.                              |
   |        |           | 
  
   ![Traffic Manager profili oluşturma](./media/traffic-manager-multivalue-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager uç noktalarını ekleme

Önceki adımda oluşturduğunuz çoklu değer Traffic Manager profiline dış uç noktalar olarak iki IP adresi ekleyin.

1. Portalın arama çubuğunda önceki bölümde oluşturduğunuz Traffic Manager profili adını arayın ve görüntülenen sonuçların arasından bu profili seçin.
2. **Traffic Manager profili** sayfasının **Ayarlar** bölümünde **Uç noktalar**'a ve ardından **Ekle**'ye tıklayın.
3. Aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin ve sonra **Tamam**’ı seçin:

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | Tür                    | Dış uç nokta                                   |
    | Ad           | myEndpoint1                                        |
    | Tam etki alanı adı (FQDN) veya IP           | Bu Traffic Manager profiline eklemek istediğiniz uç noktanın genel IP adresini yazın                         |
    |        |           |

4. **Tam etki alanı adı (FQDN) veya IP**için *myEndpoint2*adlı başka bir uç nokta eklemek üzere 2 ve 3. adımları yineleyin, ıkıncı uç noktanın genel IP adresini girin.
5. Her iki uç noktanın eklenmesi tamamlandığında, **Çevrimiçi** izleme durumuyla birlikte **Traffic Manager profili** bölümünde gösterilir.

   ![Traffic Manager uç noktası ekleme](./media/traffic-manager-multivalue-routing-method/add-endpoint.png)
 
## <a name="next-steps"></a>Sonraki adımlar

- [Ağırlıklı trafik yönlendirme yöntemi](traffic-manager-configure-weighted-routing-method.md) hakkında bilgi edinin.
- [Öncelik yönlendirme yöntemi](traffic-manager-configure-priority-routing-method.md) hakkında bilgi edinin.
- [Performans yönlendirme yöntemi](traffic-manager-configure-performance-routing-method.md) hakkında daha fazla bilgi edinin
- [Coğrafi yönlendirme yöntemi](traffic-manager-configure-geographic-routing-method.md) hakkında bilgi edinin.


