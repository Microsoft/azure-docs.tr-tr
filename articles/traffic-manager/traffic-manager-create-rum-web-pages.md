---
title: Web sayfalarıyla Gerçek Kullanıcı Ölçümleri-Azure Traffic Manager
description: Bu makalede, Azure Traffic Manager 'a Gerçek Kullanıcı Ölçümleri göndermek üzere Web sayfalarınızı ayarlamayı öğrenin.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
manager: kumud
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/06/2021
ms.author: duau
ms.openlocfilehash: 7ec91945c901f46d7036e8c474f9f5f0714ce65e
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580399"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Web sayfalarını kullanarak Azure Traffic Manager 'a Gerçek Kullanıcı Ölçümleri gönderme

Web sayfalarınızı, bir Gerçek Kullanıcı Ölçümleri (RUM) anahtarı alarak ve oluşturulan kodu Web sayfasına ekleyerek Traffic Manager Gerçek Kullanıcı Ölçümleri gönderecek şekilde yapılandırabilirsiniz.

## <a name="obtain-a-real-user-measurements-key"></a>Gerçek Kullanıcı Ölçümleri anahtarı edinin

İstemci uygulamanızdan Traffic Manager alıp gönderen ölçümler, **gerçek Kullanıcı ölçümleri (Rum) anahtarı** olarak adlandırılan benzersiz bir dize kullanılarak hizmet tarafından tanımlanır. Azure portal, REST API, PowerShell veya Azure CLı kullanarak bir RUM anahtarı edinebilirsiniz.

Azure portal kullanarak RUM anahtarını almak için:
1. Bir tarayıcıdan Azure portalında oturum açın. Henüz bir hesabınız yoksa, bir aylık ücretsiz denemeye kaydolabilirsiniz.

1. Portalın arama çubuğunda, değiştirmek istediğiniz Traffic Manager profili adını arayın ve ardından görüntülenen sonuçlarda Traffic Manager profilini seçin.

1. Traffic Manager profili sayfasında, **Ayarlar** altında **gerçek Kullanıcı ölçümleri** ' i seçin.

1. Yeni bir RUM anahtarı oluşturmak için **anahtar oluştur** ' u seçin.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/generate-rum-key.png" alt-text="Anahtar oluşturma ekranının ekran görüntüsü."::: 

   **Şekil 1: anahtar oluşturmayı Gerçek Kullanıcı Ölçümleri**

1. Sayfada artık oluşturulan RUM anahtarı ve HTML sayfanıza katıştırılması gereken bir JavaScript kod parçacığı görüntülenir.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png" alt-text="Oluşturulan JavaScript kodunun ekran görüntüsü."::: 

    **Şekil 2: anahtar ve ölçüm JavaScript Gerçek Kullanıcı Ölçümleri**
 
1. JavaScript kodunu kopyalamak için **Kopyala** düğmesini seçin. 

> [!IMPORTANT]
> Gerçek Kullanıcı Ölçümleri özelliği için üretilen JavaScript 'ı kullanarak düzgün şekilde çalışır. Bu betikteki veya Gerçek Kullanıcı Ölçümleri tarafından kullanılan betiklerdeki değişiklikler öngörülemeyen davranışlara neden olabilir.

## <a name="embed-the-code-to-an-html-web-page"></a>Kodu bir HTML Web sayfasına ekleme

RUM anahtarını aldıktan sonra, bir sonraki adım, bu kopyalanmış JavaScript 'ı son kullanıcılarınızın ziyaret ettiği bir HTML sayfasına katıştırmanız gerekir. HTML 'nin düzenlenmesine birçok şekilde ve farklı araçlar ve iş akışları kullanılarak yapılabilir. Bu örnek, bu betiği eklemek için bir HTML sayfasının nasıl güncelleştirilmesini gösterir. Bu kılavuzu, HTML kaynak yönetimi iş akışınıza uyarlamak için kullanabilirsiniz.

1. HTML sayfasını bir metin düzenleyicisinde açın.

1. Son bölümde kopyaladığınız JavaScript kodunu HTML 'nin gövde bölümüne yapıştırın. Kopyalanmış kod 8. satırda & 9 ' da yer alır. Şekil 3 ' ü inceleyin.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png" alt-text="Web sayfasına eklenmiş oluşturulan JavaScript ekran görüntüsü."::: 

    **Şekil 3: katıştırılmış Gerçek Kullanıcı Ölçümleri JavaScript ile basit HTML**

1. HTML dosyasını kaydedin ve internet 'e bağlı bir Web sunucusu üzerinde barındırın.

1. Bu sayfa bir Web tarayıcısında daha sonra işlenirse, başvurulan JavaScript indirilir ve betik ölçüm ve raporlama işlemlerini yürütecektir.

## <a name="next-steps"></a>Sonraki adımlar
- [Gerçek Kullanıcı ölçümleri](traffic-manager-rum-overview.md) hakkında daha fazla bilgi edinin
- [Traffic Manager nasıl çalıştığını](traffic-manager-overview.md) öğrenin
- Traffic Manager tarafından desteklenen [trafik yönlendirme yöntemleri](traffic-manager-routing-methods.md) hakkında daha fazla bilgi edinin
- [Traffic Manager profili oluşturmayı](./quickstart-create-traffic-manager-profile.md) öğrenin
