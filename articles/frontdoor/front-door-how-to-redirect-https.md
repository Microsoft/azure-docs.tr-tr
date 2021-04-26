---
title: Azure portal kullanarak HTTP ile HTTPS yönlendirmesi arasında ön kapı oluşturma
description: Azure portal kullanarak HTTP 'den HTTPS 'ye yeniden yönlendirilen trafikle ön kapı oluşturmayı öğrenin.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: edc1ebe53969ceac0452818bee016e35de1e9322
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167847"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Azure portal kullanarak HTTP ile HTTPS yönlendirmesi arasında ön kapı oluşturma

TLS sonlandırma sertifikasıyla bir [ön kapı oluşturmak](quickstart-create-front-door.md) için Azure Portal kullanabilirsiniz. HTTP trafiğini HTTPS 'ye yönlendirmek için bir yönlendirme kuralı kullanılır.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Mevcut bir Web uygulaması kaynağıyla ön kapı oluşturma

1. [https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

1. Azure portal sol üst köşesinde bulunan **kaynak oluştur** ' u seçin.

1. Arama çubuğunu kullanarak **ön kapı** araması yapın ve kaynak türünü bulduktan sonra **Oluştur**' u seçin.

1. Bir *abonelik* seçin ve ardından var olan bir kaynak grubunu kullanın veya yeni bir tane oluşturun. Yapılandırma sekmesini girmek için **İleri ' yi** seçin.

    > [!NOTE]
    > Kullanıcı arabiriminde istenen konum yalnızca kaynak grubu içindir. Ön kapı yapılandırmanız, tüm [Azure ön KAPıSıNıN pop konumlarına](front-door-faq.yml#what-are-the-pop-locations-for-azure-front-door-)dağıtılır.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-basics.png" alt-text="Yeni ön kapıya yönelik temelleri yapılandırın":::

1. Ön kapıya yönelik yapılandırma üç adımda gerçekleşir. varsayılan ön uç konak ekleyerek, arka uç havuzunda arka uçlar ekleyerek ve ardından ön uç ana bilgisayar için yönlendirme davranışını eşlemek üzere yönlendirme kuralları oluşturma. Ön uç **+** konağı oluşturmak Için _ön uç konaklarındaki_ ' ' simgesini seçin.

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer.png" alt-text="Ön kapı yapılandırma Tasarımcısı":::

1. Ön kapılarınız için varsayılan ön uç ana bilgisayarınız için genel olarak benzersiz bir ad girin. Sonraki adıma devam etmek için **Ekle** ' yi seçin.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-frontend-host.png" alt-text="Ön uç konağı ekleme":::

### <a name="create-backend-pool"></a>Arka uç Havuzu Oluştur

1. Arka uç **+** havuzu oluşturmak Için _arka uç havuzlarındaki_ ' ' simgesini seçin. Arka uç havuzu için bir ad girin ve ardından **arka uç Ekle**' yi seçin.

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-backend-pool.png" alt-text="Ön kapı yapılandırma Tasarımcısı arka uç havuzu":::

1. Arka uç konak türünü _App Service_ olarak seçin. Web uygulamanızın barındırıldığı aboneliği seçin ve ardından **arka uç ana bilgisayar adı** açılır listesinden belirli bir Web uygulamasını seçin.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-backend-pool.png" alt-text="Arka uç havuzunda arka uç ekleme":::

1. Arka ucu kaydetmek için **Ekle** ' yi seçin ve arka uç havuzu yapılandırmasını kaydetmek Için yeniden **Ekle** ' yi seçin. 

## <a name="create-http-to-https-redirect-rule"></a>HTTP-HTTPS yeniden yönlendirme kuralı oluştur

1. **+** *Yönlendirme kuralları* ' ' simgesini seçerek bir yol oluşturun. Yol için bir ad sağlayın (örneğin, ' HttpToHttpsRedirect ') ve ardından *kabul edilen protokol* alanını **' http Only '** olarak ayarlayın. Uygun *ön uç/etki alanlarının* seçildiğinden emin olun.  

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-routing-rule.png" alt-text="Ön kapı yapılandırma Tasarımcısı yönlendirme kuralı":::

1. *Rota ayrıntıları* bölümü altında *rota türünü* **yeniden yönlendir** olarak ayarlayın. *Yeniden yönlendirme türü* al **(302)** ve *yeniden yönlendirme protokolü* almanın **yalnızca https** olarak ayarlandığını doğrulayın. 

    :::image type="content" source="./media/front-door-url-redirect/front-door-redirect-config-example.png" alt-text="Bir HTTP-HTTPS yeniden yönlendirme yolu ekleyin":::

1. HTTP ve HTTPS yönlendirmesi için yönlendirme kuralını kaydetmek üzere **Ekle** ' yi seçin.

## <a name="create-forwarding-rule"></a>İletme kuralı oluştur

1. HTTPS trafiğini işlemek için başka bir yönlendirme kuralı ekleyin. **+** *Yönlendirme kurallarında* ' ' işaretini seçin ve rota için bir ad sağlayın, örneğin ' DefaultForwardingRoute '. Ardından *kabul edilen protokoller* alanını **yalnızca https** olarak ayarlayın. Uygun *ön uç/etki alanlarının* seçildiğinden emin olun.

1. Rota ayrıntıları bölümünde *rota türünü* **ilet** olarak ayarlayın. Sağ arka uç havuzunun seçili olduğundan ve *Iletme protokolünün* **yalnızca https** olarak ayarlandığından emin olun. 

    :::image type="content" source="./media/front-door-url-redirect/front-door-forward-route-example.png" alt-text="HTTPS trafiği için bir iletme yolu ekleyin" border="false":::

1. İstek iletimi için yönlendirme kuralını kaydetmek üzere **Ekle** ' yi seçin.

1. Ön kapı profilinizi oluşturmak için **gözden geçir + oluştur** ' u ve ardından **Oluştur**' u seçin. Oluşturulduktan sonra kaynağa gidin.

## <a name="next-steps"></a>Sonraki adımlar

- [Front Door’un nasıl çalıştığını](front-door-routing-architecture.md) öğrenin.
- [Ön kapıda URL yeniden yönlendirme](front-door-url-redirect.md)hakkında daha fazla bilgi edinin.
