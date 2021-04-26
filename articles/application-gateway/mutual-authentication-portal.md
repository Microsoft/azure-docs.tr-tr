---
title: Portal üzerinden Azure Application Gateway karşılıklı kimlik doğrulamasını yapılandırma
description: Portal üzerinden karşılıklı kimlik doğrulaması yapmak için bir Application Gateway yapılandırma hakkında bilgi edinin
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: b2118a257f029a63445b09dfa618d6e3ceacddda
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231618"
---
# <a name="configure-mutual-authentication-with-application-gateway-through-portal-preview"></a>Portal üzerinden Application Gateway karşılıklı kimlik doğrulamasını yapılandırma (Önizleme)

Bu makalede, Application Gateway karşılıklı kimlik doğrulamasını yapılandırmak için Azure portal nasıl kullanılacağı açıklanır. Karşılıklı kimlik doğrulaması, Application Gateway karşıya yüklediğiniz istemci sertifikasını kullanarak isteği gönderen istemcinin kimlik doğrulamasını Application Gateway anlamına gelir. 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="before-you-begin"></a>Başlamadan önce

Application Gateway karşılıklı kimlik doğrulamasını yapılandırmak için, ağ geçidine yüklemek üzere bir istemci sertifikasına ihtiyacınız vardır. İstemci sertifikası, istemcinin Application Gateway için sunacağı sertifikayı doğrulamak için kullanılacaktır. Sınama amacıyla, kendinden imzalı bir sertifika kullanabilirsiniz. Ancak, bu, üretim iş yükleri için önerilmez çünkü yönetilmesi zordur ve tamamen güvende değildir. 

Daha fazla bilgi edinmek için, özellikle ne tür istemci sertifikaları karşıya yükleyebilir, bkz. [Application Gateway ile karşılıklı kimlik doğrulamaya genel bakış](./mutual-authentication-overview.md#certificates-supported-for-mutual-authentication).

## <a name="create-a-new-application-gateway"></a>Yeni bir Application Gateway oluştur

İlk olarak portalda olduğu gibi yeni bir Application Gateway oluşturun, karşılıklı kimlik doğrulamayı etkinleştirmek için oluşturma konusunda ek bir adım gerekmez. Portalda Application Gateway oluşturma hakkında daha fazla bilgi için [Portal Hızlı Başlangıç öğreticimize](./quick-create-portal.md)göz atın.

## <a name="configure-mutual-authentication"></a>Karşılıklı kimlik doğrulamasını yapılandırma 

Mevcut bir Application Gateway karşılıklı kimlik doğrulamasıyla yapılandırmak için öncelikle portalda **SSL ayarları (Önizleme)** sekmesine gitmeniz ve yenı bir SSL profili oluşturmanız gerekir. Bir SSL profili oluşturduğunuzda iki sekme görürsünüz: **Istemci kimlik doğrulaması** ve **SSL ilkesi**. İstemci **kimlik doğrulaması** sekmesi, istemci sertifikalarınızı karşıya yükleyeceksiniz. **SSL ilkesi** sekmesi, bir DINLEYICIYE özgü SSL ilkesi yapılandırmak içindir. daha fazla bilgi için, [bir dinleyiciye özgü SSL ilkesi yapılandırmaya](./application-gateway-configure-listener-specific-ssl-policy.md)göz atın.

> [!IMPORTANT]
> Lütfen tüm istemci CA sertifika zincirini tek bir dosyaya ve dosya başına yalnızca bir zincir karşıya yüklediğinizden emin olun.

1. Portalda **Application Gateway** arayın, **uygulama ağ geçitleri**' ni seçin ve mevcut Application Gateway tıklayın.

2. Sol taraftaki menüden **SSL ayarlarını (Önizleme)** seçin.

3. Yeni bir SSL profili oluşturmak için en üstteki **SSL profilleri** ' nin yanındaki artı işaretine tıklayın.

4. **SSL profili adı** altında bir ad girin. Bu örnekte, SSL profili *Applicationgatewaysslprofile*' i çağırıyoruz. 

5. **Istemci kimlik doğrulaması** sekmesinde kalın. **Yeni sertifikayı karşıya yükle** düğmesini kullanarak istemci ile Application Gateway arasında karşılıklı kimlik doğrulaması için kullanmak istediğiniz pek sertifikasını karşıya yükleyin. 

    Güvenilen istemci CA sertifikası zincirlerini buradan karşıya yüklemek üzere ayıklama hakkında daha fazla bilgi için bkz. [Güvenilen ISTEMCI CA sertifika zincirlerini ayıklama](./mutual-authentication-certificate-management.md).

   > [!NOTE]
   > İlk SSL profiliniz yoksa ve Application Gateway diğer istemci sertifikalarını karşıya yüklemişseniz, açılır menü aracılığıyla ağ geçidinizdeki mevcut bir sertifikayı yeniden kullanmayı seçebilirsiniz. 

6. İstemci sertifikasının en yakın ayırt edici adını doğrulamak Application Gateway istiyorsanız, **istemci sertifikası VERENIN DN 'Sini doğrula** kutusunu işaretleyin. 

7. Dinleyiciye özgü bir ilke eklemeyi düşünün. [Dinleyiciye özgü SSL ilkelerini ayarlama](./application-gateway-configure-listener-specific-ssl-policy.md)konusundaki yönergelere bakın.

8. Kaydetmek için **Ekle** ' yi seçin.
    > [!div class="mx-imgBorder"]
    > ![İstemci kimlik doğrulamasını SSL profiline ekle](./media/mutual-authentication-portal/mutual-authentication-portal.png)

## <a name="associate-the-ssl-profile-with-a-listener"></a>SSL profilini bir dinleyiciyle ilişkilendir

Karşılıklı kimlik doğrulaması yapılandırılmış bir SSL profili oluşturduğumuz artık, karşılıklı kimlik doğrulamasının kurulumunu tamamlaması için SSL profilini dinleyiciyle ilişkilendirmemiz gerekir. 

1. Mevcut Application Gateway gidin. Yukarıdaki adımları tamamladığınız takdirde, burada herhangi bir şey yapmanız gerekmez. 

2. Sol taraftaki menüden **dinleyicileri** seçin. 

3. Önceden ayarlanmış bir HTTPS dinleyiciniz yoksa, **dinleyici Ekle** ' ye tıklayın. Zaten bir HTTPS dinleyiciniz varsa, listeden üzerine tıklayın. 

4. Gereksinimlerinize uyacak şekilde **dinleyici adı**, **ön uç IP**, **bağlantı noktası**, **protokol** ve diğer **https ayarlarını** doldurun.

5. Dinleyiciyle ilişkilendirilecek SSL profilini seçebilmeniz için **SSL profilini etkinleştir** onay kutusunu işaretleyin. 

6. Açılan listeden yeni oluşturduğunuz SSL profilini seçin. Bu örnekte, önceki adımlardan oluşturduğumuz SSL profilini seçtik: *Applicationgatewaysslprofile*. 

7. Dinleyicinin geri kalanını gereksinimlerinize uyacak şekilde yapılandırmaya devam edin. 

8. Yeni dinleyicinizi onunla ilişkili SSL profiliyle birlikte kaydetmek için **Ekle** ' ye tıklayın. 

    > [!div class="mx-imgBorder"]
    > ![SSL profilini yeni dinleyiciye ilişkilendir](./media/mutual-authentication-portal/mutual-authentication-listener-portal.png)

## <a name="renew-expired-client-ca-certificates"></a>Vadesi geçen istemci CA sertifikalarını Yenile

İstemci CA sertifikanızın süresi dolmuşsa, ağ geçidinizdeki sertifikayı aşağıdaki adımlarla güncelleştirebilirsiniz: 

1. Application Gateway gidin ve sol taraftaki menüdeki **SSL ayarları (Önizleme)** sekmesine gidin. 
 
1. Son kullanma istemci sertifikası olan mevcut SSL profillerini seçin. 
 
1. **Istemci kimlik doğrulaması** sekmesinde **Yeni bir sertifika yükle** ' yi seçin ve yeni İstemci sertifikanızı karşıya yükleyin. 
 
1. Süre dolma sertifikası ' nın yanındaki çöp kutusu simgesini seçin. Bu, bu sertifikanın ilişkilendirmesini SSL profilinden kaldırır. 

1. Yukarıdaki 2-4 adımlarını, aynı süre dolmuþ istemci sertifikasını kullanan başka bir SSL profiliyle tekrarlayın. Diğer SSL profillerindeki açılan menüden 3. adımda karşıya yüklediğiniz yeni sertifikayı seçebileceksiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure CLI kullanarak bir uygulama ağ geçidi ile web trafiğini yönetme](./tutorial-manage-web-traffic-cli.md)