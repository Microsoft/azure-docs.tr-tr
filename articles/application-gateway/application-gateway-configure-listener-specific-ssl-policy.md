---
title: Azure Application Gateway portal üzerinden dinleyiciye özgü SSL ilkelerini yapılandırma
description: Portal üzerinden Application Gateway için dinleyiciye özgü SSL ilkelerini yapılandırma hakkında bilgi edinin
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/30/2021
ms.author: caya
ms.openlocfilehash: b0bda1921c63b4e353365c3b391e17f620740396
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231645"
---
# <a name="configure-listener-specific-ssl-policies-on-application-gateway-through-portal-preview"></a>Portal üzerinden Application Gateway dinleyiciye özgü SSL ilkelerini yapılandırma (Önizleme)

Bu makalede, Application Gateway, dinleyiciye özgü SSL ilkelerini yapılandırmak için Azure portal nasıl kullanılacağı açıklanır. Dinleyiciye özgü SSL ilkeleri, belirli dinleyicileri birbirinden farklı SSL ilkeleri kullanacak şekilde yapılandırmanıza olanak tanır. Dinleyiciye özgü SSL ilkesi tarafından üzerine yazılmadıkça, tüm dinleyicilerinin kullanacağı varsayılan bir SSL ilkesi de ayarlayabileceksiniz. 

> [!NOTE]
> Yalnızca Standard_v2 ve WAF_v2 SKU 'Ları, dinleyiciye özgü ilkeler SSL profillerinin bir parçası olduğundan ve SSL profillerinin yalnızca V2 ağ geçitlerinde desteklendiğinden, dinleyiciye özgü ilkeleri destekler. 



Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="create-a-new-application-gateway"></a>Yeni bir Application Gateway oluştur

İlk olarak portalda olduğu gibi yeni bir Application Gateway oluşturun-Bu, dinleyiciye özgü SSL ilkelerini yapılandırmak için oluşturma konusunda ek adım gerekmez. Portalda Application Gateway oluşturma hakkında daha fazla bilgi için [Portal Hızlı Başlangıç öğreticimize](./quick-create-portal.md)göz atın.

## <a name="set-up-a-listener-specific-ssl-policy"></a>Dinleyiciye özgü SSL ilkesi ayarlama

Dinleyiciye özgü bir SSL ilkesi ayarlamak için öncelikle portalda **SSL ayarları (Önizleme)** sekmesine gitmeniz ve yenı bir SSL profili oluşturmanız gerekir. Bir SSL profili oluşturduğunuzda iki sekme görürsünüz: **Istemci kimlik doğrulaması** ve **SSL ilkesi**. **SSL ilkesi** sekmesi, dinleyiciye özgü bir SSL ilkesi yapılandırmaktır. **Istemci kimlik doğrulaması** sekmesi, karşılıklı kimlik doğrulaması için bir istemci sertifikasının karşıya yükleneceği yerdir-daha fazla bilgi için, [karşılıklı kimlik doğrulaması yapılandırma](./mutual-authentication-portal.md)konusuna bakın.

> [!NOTE]
> TLS 1,2, gelecekte uygulanan olacağı için TLS 1,2 kullanmanızı öneririz. 

1. Portalda **Application Gateway** arayın, **uygulama ağ geçitleri**' ni seçin ve mevcut Application Gateway tıklayın.

2. Sol taraftaki menüden **SSL ayarlarını (Önizleme)** seçin.

3. Yeni bir SSL profili oluşturmak için en üstteki **SSL profilleri** ' nin yanındaki artı işaretine tıklayın.

4. **SSL profili adı** altında bir ad girin. Bu örnekte, SSL profili *Applicationgatewaysslprofile*' i çağırıyoruz. 

5. **SSL ilkesi** sekmesine gidin ve **DINLEYICIYE özgü SSL ilkesini etkinleştir** onay kutusunu işaretleyin. 

6. Gereksinimleriniz için dinleyiciye özgü SSL ilkenizi ayarlayın. Önceden tanımlanmış SSL ilkeleri arasından seçim yapabilir ve kendi SSL ilkenizi özelleştirebilirsiniz. SSL ilkeleri hakkında daha fazla bilgi için [SSL ilkesine genel bakış](./application-gateway-ssl-policy-overview.md)' ı ziyaret edin. TLS 1,2 kullanmanızı öneririz

7. Kaydetmek için **Ekle** ' yi seçin.

    > [!NOTE]
    > Bir SSL profilinde istemci kimlik doğrulamasını bir dinleyiciyle ilişkilendirmek için yapılandırmanız gerekmez. Yalnızca istemci kimlik doğrulaması yapılandırabilir veya SSL profilinizde yapılandırılmış yalnızca belirli bir SSL ilkesi veya her ikisi de yapılandırılabilir.  

    ![SSL profiline dinleyici 'e özgü SSL ilkesi ekleyin](./media/application-gateway-configure-listener-specific-ssl-policy/listener-specific-ssl-policy-ssl-profile.png)
    
## <a name="associate-the-ssl-profile-with-a-listener"></a>SSL profilini bir dinleyiciyle ilişkilendir

Artık dinleyiciye özgü SSL ilkesiyle bir SSL profili oluşturduğumuz için, dinleyiciye özgü ilkeyi eyleme koymak üzere SSL profilini dinleyiciyle ilişkilendirmemiz gerekir. 

1. Mevcut Application Gateway gidin. Yukarıdaki adımları tamamladığınız takdirde, burada herhangi bir şey yapmanız gerekmez. 

2. Sol taraftaki menüden **dinleyicileri** seçin. 

3. Önceden ayarlanmış bir HTTPS dinleyiciniz yoksa, **dinleyici Ekle** ' ye tıklayın. Zaten bir HTTPS dinleyiciniz varsa, listeden üzerine tıklayın. 

4. Gereksinimlerinize uyacak şekilde **dinleyici adı**, **ön uç IP**, **bağlantı noktası**, **protokol** ve diğer **https ayarlarını** doldurun.

5. Dinleyiciyle ilişkilendirilecek SSL profilini seçebilmeniz için **SSL profilini etkinleştir** onay kutusunu işaretleyin. 

6. Açılan listeden oluşturduğunuz SSL profilini seçin. Bu örnekte, önceki adımlardan oluşturduğumuz SSL profilini seçtik: *Applicationgatewaysslprofile*. 

7. Dinleyicinin geri kalanını gereksinimlerinize uyacak şekilde yapılandırmaya devam edin. 

8. Yeni dinleyicinizi onunla ilişkili SSL profiliyle birlikte kaydetmek için **Ekle** ' ye tıklayın. 

    ![SSL profilini yeni dinleyiciye ilişkilendir](./media/mutual-authentication-portal/mutual-authentication-listener-portal.png)        

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure CLI kullanarak bir uygulama ağ geçidi ile web trafiğini yönetme](./tutorial-manage-web-traffic-cli.md)