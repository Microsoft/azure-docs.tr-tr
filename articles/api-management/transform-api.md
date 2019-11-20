---
title: Azure API Yönetimi ile API’nizi dönüştürme ve koruma | Microsoft Docs
description: API’nizi kotalar ve azaltma (hız sınırlama) ilkeleriyle korumayı öğrenin.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/26/2019
ms.author: apimpm
ms.openlocfilehash: 5dec08bd4bc0a63a419d2bdc63383348a69b02db
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067463"
---
# <a name="transform-and-protect-your-api"></a>API’nizi dönüştürme ve koruma

Öğreticide, özel bir arka uç bilgisini açığa çıkarmaması için API’nizin nasıl dönüştürüleceği gösterilmektedir. Örneğin, arka uçta çalıştırılan teknoloji yığını hakkındaki bilgileri gizlemek isteyebilirsiniz. API’lerin HTTP yanıt gövdesinde görüntülenen özgün URL’leri gizlemek ve bunları APIM ağ geçidine yeniden yönlendirmek isteyebilirsiniz.

Bu öğreticide size Azure API Yönetimi ile hız sınırı yapılandırarak arka uç API’niz için koruma eklemenin ne kadar kolay olduğu gösterilmektedir. Örneğin, geliştiriciler tarafından aşırı kullanılmaması için API çağrısı sayısını sınırlamak isteyebilirsiniz. Daha fazla bilgi için bkz. [API Yönetimi ilkeleri](api-management-policies.md)

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> -   Yanıt üst bilgilerini gizlemek için API’yi dönüştürme
> -   API yanıt gövdesindeki özgün URL’leri, APIM ağ geçidi URL’leri ile değiştirme
> -   Hız sınırı ilkesi ekleyerek (azaltma) bir API’yi koruma
> -   Dönüştürmeleri test etme

![İlkeler](./media/transform-api/api-management-management-console.png)

## <a name="prerequisites"></a>Önkoşullar

-   [Azure API Management terminolojisini](api-management-terminology.md) öğrenin.
-   [Azure API Management'ta ilke kavramını](api-management-howto-policies.md) anlayın.
-   Aşağıdaki hızlı başlangıcı doldurun: [Azure API Management örneği oluşturun](get-started-create-service-instance.md).
-   Ayrıca, aşağıdaki öğreticiyi de tamamlayabilirsiniz: [Ilk API 'Nizi Içeri aktarın ve yayımlayın](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>Yanıt üst bilgilerini gizlemek için API’yi dönüştürme

Bu bölümde, kullanıcılarınıza göstermek istemediğiniz HTTP üst bilgilerinin nasıl gizleneceği gösterilmektedir. Bu örnekte, aşağıdaki üst bilgiler HTTP yanıtında silinir:

-   **X-Destekleyen:**
-   **X-AspNet-Sürümü**

### <a name="test-the-original-response"></a>Özgün yanıtı test etme

Özgün yanıtı görmek için:

1. APIM hizmet örneğinizde **API'ler**’i (**API YÖNETİMİ** altında) seçin.
2. API listenizden **Tanıtım Konferansı API’sine** tıklayın.
3. Ekranın üst kısmındaki **Test** sekmesine tıklayın.
4. **GetSpeakers** işlemini seçin.
5. Ekranın alt kısmındaki **Gönder** düğmesine basın.

Özgün yanıt şu şekilde görünmelidir:

![İlkeler](./media/transform-api/original-response.png)

### <a name="set-the-transformation-policy"></a>Dönüştürme ilkesi ayarlama

![Giden ilkesini ayarlama](./media/transform-api/04-ProtectYourAPI-01-SetPolicy-Outbound.png)

1. **Tanıtım Konferansı API’si** seçeneğini belirleyin.
2. Ekranın üst kısmında **Tasarım** sekmesini seçin.
3. **Tüm işlemler**’i seçin.
4. **Giden işleme** bölümünde **</>** simgesine tıklayın.
5. İmleci **&lt;giden&gt;** öğesinin içine konumlandırın.
6. Sağ pencerede **Dönüştürme ilkeleri** bölümünde **+ HTTP üst bilgisini ayarla** seçeneğine iki defa tıklayın (iki ilke kod parçacığı eklemek için).

   ![İlkeler](./media/transform-api/transform-api.png)

7. Giden > kodunuzu değiştirin, şöyle görünür:  **\<**

       <set-header name="X-Powered-By" exists-action="delete" />
       <set-header name="X-AspNet-Version" exists-action="delete" />

   ![İlkeler](./media/transform-api/set-policy.png)

8. **Kaydet** düğmesine tıklayın.

## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>API yanıt gövdesindeki özgün URL’leri, APIM ağ geçidi URL’leri ile değiştirme

Bu bölümde, API’lerin HTTP yanıt gövdesinde görüntülenen özgün URL’lerin nasıl gizleneceği ve bunların APIM ağ geçidine nasıl yeniden yönlendirileceği gösterilmektedir.

### <a name="test-the-original-response"></a>Özgün yanıtı test etme

Özgün yanıtı görmek için:

1. **Tanıtım Konferansı API’si** seçeneğini belirleyin.
2. Ekranın üst kısmındaki **Test** sekmesine tıklayın.
3. **GetSpeakers** işlemini seçin.
4. Ekranın alt kısmındaki **Gönder** düğmesine basın.

    Gördüğünüz gibi özgün yanıt şuna benzer:

    ![İlkeler](./media/transform-api/original-response2.png)

### <a name="set-the-transformation-policy"></a>Dönüştürme ilkesi ayarlama

1.  **Tanıtım Konferansı API’si** seçeneğini belirleyin.
2.  **Tüm işlemler**’i seçin.
3.  Ekranın üst kısmında **Tasarım** sekmesini seçin.
4.  **Giden işleme** bölümünde **</>** simgesine tıklayın.
5.  İmleci **&lt;giden&gt;** öğesinin içine konumlandırın.
6.  Sağ pencerede **Dönüştürme ilkeleri** bölümünde **+ Gövdedeki dizeyi bul ve değiştir** seçeneğine tıklayın.
7.  URL’yi APIM ağ geçidinizle eşleşecek şekilde değiştirmek için **find-and-replace** kodunuzu ( **\<giden\>** öğesinde) değiştirin. Örneğin:

        <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>Hız sınırı ilkesi ekleyerek (azaltma) bir API’yi koruma

Bu bölümde, hız sınırları yapılandırılarak arka uç API’niz için nasıl koruma ekleneceği gösterilmektedir. Örneğin, geliştiriciler tarafından aşırı kullanılmaması için API çağrısı sayısını sınırlamak isteyebilirsiniz. Bu örnekte, her bir abonelik kimliği için sınır 15 saniyede 3 çağrıya ayarlanmıştır. 15 saniye sonra geliştirici, API’yi çağırmayı yeniden deneyebilir.

![Gelen ilkesi ayarlama](./media/transform-api/04-ProtectYourAPI-01-SetPolicy-Inbound.png)

1.  **Tanıtım Konferansı API’si** seçeneğini belirleyin.
2.  **Tüm işlemler**’i seçin.
3.  Ekranın üst kısmında **Tasarım** sekmesini seçin.
4.  **Gelen işleme** bölümünde **</>** simgesine tıklayın.
5.  İmleci **&lt;gelen&gt;** öğesinin içine konumlandırın.
6.  Sağ pencerede **Erişim kısıtlama ilkeleri** bölümünde **+ Anahtar başına çağrıyı sınırla** seçeneğine tıklayın.
7.  **rate-limit-by-key** kodunuzu ( **\<gelen\>** öğesinde) aşağıdaki kodla değiştirin:

        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />

## <a name="test-the-transformations"></a>Dönüştürmeleri test etme

Bu noktada kod düzenleyicisinde koda bakarsanız, ilkeleriniz şöyle görünür:

    <policies>
        <inbound>
            <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
            <base />
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <set-header name="X-Powered-By" exists-action="delete" />
            <set-header name="X-AspNet-Version" exists-action="delete" />
            <find-and-replace from="://conferenceapi.azurewebsites.net:443" to="://apiphany.azure-api.net/conference"/>
            <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>

Bu bölümün geri kalanında, bu makalede ayarladığınız ilke dönüştürmeleri test edilmektedir.

### <a name="test-the-stripped-response-headers"></a>Gizlenmiş yanıt üst bilgilerini test etme

1. **Tanıtım Konferansı API’si** seçeneğini belirleyin.
2. **Test** sekmesini seçin.
3. **GetSpeakers** işlemine tıklayın.
4. **Gönder**’e basın.

    Gördüğünüz gibi üst bilgiler gizlenmiştir:

    ![İlkeler](./media/transform-api/final-response1.png)

### <a name="test-the-replaced-url"></a>Değiştirilen URL’yi test etme

1. **Tanıtım Konferansı API’si** seçeneğini belirleyin.
2. **Test** sekmesini seçin.
3. **GetSpeakers** işlemine tıklayın.
4. **Gönder**’e basın.

    Gördüğünüz gibi URL değiştirilmiştir.

    ![İlkeler](./media/transform-api/final-response2.png)

### <a name="test-the-rate-limit-throttling"></a>Hız sınırını test etme (azaltma)

1. **Tanıtım Konferansı API’si** seçeneğini belirleyin.
2. **Test** sekmesini seçin.
3. **GetSpeakers** işlemine tıklayın.
4. Art arda üç defa **Gönder** düğmesine basın.

    İsteği 3 defa gönderdikten sonra **429 Çok sayıda istek var** yanıtını alırsınız.

5. 15 saniye kadar bekleyin ve tekrar **Gönder** düğmesine basın. Bu defa **200 OK** yanıtını almanız gerekir.

    ![Azaltma](./media/transform-api/test-throttling.png)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
>
> -   Yanıt üst bilgilerini gizlemek için API’yi dönüştürme
> -   API yanıt gövdesindeki özgün URL’leri, APIM ağ geçidi URL’leri ile değiştirme
> -   Hız sınırı ilkesi ekleyerek (azaltma) bir API’yi koruma
> -   Dönüştürmeleri test etme

Sonraki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
> [API’nizi izleme](api-management-howto-use-azure-monitor.md)
