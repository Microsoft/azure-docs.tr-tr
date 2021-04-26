---
title: Öğretici-API 'nizi Azure API Management dönüştürme ve koruma | Microsoft Docs
description: Bu öğreticide, API 'nizi dönüştürme ve daraltma (hız sınırlama) ilkeleriyle API Management nasıl koruyacağınızı öğreneceksiniz.
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/28/2020
ms.author: apimpm
ms.openlocfilehash: 979bdaa1e0dac4f45a321abda2a208f46983f9cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010240"
---
# <a name="tutorial-transform-and-protect-your-api"></a>Öğretici: API 'nizi dönüştürme ve koruma

Öğreticide, özel arka uç hakkındaki bilgileri açığa çıkarmaması için API 'nizi nasıl dönüştürebileceğinizi gösterir. Örneğin, arka uçta çalışan teknoloji yığını bilgilerini gizlemek isteyebilirsiniz. Ayrıca, API 'nin HTTP yanıtının gövdesinde görüntülenen özgün URL 'Leri gizlemek ve bunun yerine onları APıM ağ geçidine yönlendirmek isteyebilirsiniz.

Bu öğretici Ayrıca Azure API Management bir hız sınırı yapılandırarak arka uç API 'niz için koruma eklemenin ne kadar kolay olduğunu gösterir. Örneğin API çağrısı oranını kısıtlamak isteyebilirsiniz, bu sayede API, geliştiriciler tarafından aşırı kullanılmaz. Daha fazla bilgi için bkz. [API Management ilkeleri](api-management-policies.md).

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> -   Yanıt üst bilgilerini gizlemek için API’yi dönüştürme
> -   API yanıt gövdesindeki özgün URL’leri, APIM ağ geçidi URL’leri ile değiştirme
> -   Hız sınırı ilkesi ekleyerek bir API 'YI koruma (daraltma)
> -   Dönüştürmeleri test etme

:::image type="content" source="media/transform-api/api-management-management-console.png" alt-text="Portaldaki ilkeler":::

## <a name="prerequisites"></a>Önkoşullar

-   [Azure API Management terminolojisini](api-management-terminology.md) öğrenin.
-   [Azure API Management'ta ilke kavramını](api-management-howto-policies.md) anlayın.
-   Şu hızlı başlangıcı tamamlayın: [Azure API Management örneği oluşturma](get-started-create-service-instance.md).
-   Ayrıca, şu öğreticiyi tamamlayın: [İlk API'nizi içeri aktarma ve yayımlama](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>Yanıt üst bilgilerini gizlemek için API’yi dönüştürme

Bu bölümde, kullanıcılarınıza göstermek istemediğiniz HTTP üstbilgilerinin nasıl gizlenmesi gösterilmektedir. Bu örnekte, HTTP yanıtında aşağıdaki üstbilgilerin nasıl silineceği gösterilmektedir:

-   **X-Powered-By**
-   **X-AspNet-Sürümü**

### <a name="test-the-original-response"></a>Özgün yanıtı test etme

Özgün yanıtı görmek için:

1. API Management hizmet Örneğinizde **API 'ler**' i seçin.
1. API listenizden **Tanıtım Konferansı API 'si** ' ni seçin.
1. Ekranın üst kısmındaki **Test** sekmesini seçin.
1. **Gethoparlörler** işlemini seçin ve **Gönder**' i seçin.

Özgün yanıt şuna benzer olmalıdır:

:::image type="content" source="media/transform-api/original-response.png" alt-text="Özgün API yanıtı":::

Görebileceğiniz gibi, yanıt **x-ASPNET-Version** ve **x-destekli** üst bilgilerini içerir.

### <a name="set-the-transformation-policy"></a>Dönüştürme ilkesi ayarlama

1. **Demo Conference API**  >  **tasarımı**  >  **tüm işlemler**' i seçin.
4. **Giden işleme** bölümünde, kod Düzenleyicisi ( **</>** ) simgesini seçin.

   :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Outbound.png" alt-text="Giden ilkeye git" border="false":::

1. İmleci **&lt; giden &gt;** öğesinin içine konumlandırın ve sağ üst köşedeki **parçacıkları göster** ' i seçin.
1. Sağ pencerede, **dönüştürme ilkeleri** altında, **http üst bilgisini** iki kez ayarla ' yı seçin (iki ilke parçacığı eklemek için).

   :::image type="content" source="media/transform-api/transform-api.png" alt-text="HTTP üst bilgi ilkesini ayarla":::

1. **\<outbound>** Kodunuzu aşağıdaki gibi görünecek şekilde değiştirin:

   ```
   <set-header name="X-Powered-By" exists-action="delete" />
   <set-header name="X-AspNet-Version" exists-action="delete" />
   ```

   :::image type="content" source="media/transform-api/set-policy.png" alt-text="HTTP üst bilgisini ayarla":::

1. **Kaydet**’i seçin.

## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>API yanıt gövdesindeki özgün URL’leri, APIM ağ geçidi URL’leri ile değiştirme

Bu bölümde, API 'nin HTTP yanıtının gövdesinde görüntülenen özgün URL 'Lerin nasıl gizlenmesi ve bunun yerine APıM ağ geçidine yönlendirilme gösterilmektedir.

### <a name="test-the-original-response"></a>Özgün yanıtı test etme

Özgün yanıtı görmek için:

1. **Tanıtım Konferansı API 'si**  >  **sınaması**' nı seçin.
1. **Gethoparlörler** işlemini seçin ve **Gönder**' i seçin.

    Görebileceğiniz gibi, yanıt özgün arka uç URL 'Lerini içerir:

    :::image type="content" source="media/transform-api/original-response2.png" alt-text="Yanıt olarak özgün URL 'Ler":::


### <a name="set-the-transformation-policy"></a>Dönüştürme ilkesi ayarlama

1.  **Tanıtım Konferansı API 'si**  >  **tüm işlemler**  >  **tasarımını** seçin.
1.  **Giden işleme** bölümünde, kod Düzenleyicisi ( **</>** ) simgesini seçin.
1.  İmleci **&lt; giden &gt;** öğesinin içine konumlandırın ve sağ üst köşedeki **parçacıkları göster** ' i seçin.
1.  Sağ taraftaki pencerede, **dönüştürme ilkeleri** altında, **Içerikte maske URL 'leri**' ni seçin. 
1.  **Kaydet**’i seçin.

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>Hız sınırı ilkesi ekleyerek (azaltma) bir API’yi koruma

Bu bölümde, hız sınırları yapılandırılarak arka uç API’niz için nasıl koruma ekleneceği gösterilmektedir. Örneğin API çağrısı oranını, API 'nin geliştiriciler tarafından aşırı kullanılmaması için sınırlamak isteyebilirsiniz. Bu örnekte, her abonelik KIMLIĞI için sınır 15 saniye başına 3 çağrı olarak ayarlanır. 15 saniye sonra geliştirici, API’yi çağırmayı yeniden deneyebilir.

1.  **Tanıtım Konferansı API 'si**  >  **tüm işlemler**  >  **tasarımını** seçin.
1.  **Gelen işlem** bölümünde, kod Düzenleyicisi ( **</>** ) simgesini seçin.
1.  İmleci **&lt; gelen &gt;** öğesinin içine konumlandırın ve sağ üst köşedeki **parçacıkları göster** ' i seçin.

    :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Inbound.png" alt-text="Gelen ilkesi ayarlama" border="false":::

1.  Sağ pencerede, **erişim kısıtlama ilkeleri** altında, **anahtar başına çağrı hızını sınırla**' yı seçin.
1.  **Oran-limit anahtar** kodunuzu ( **\<inbound\>** öğesinde) aşağıdaki kodla değiştirin:

    ```
    <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
    ```

## <a name="test-the-transformations"></a>Dönüştürmeleri test etme

Bu noktada, kod düzenleyicisinde koda bakarsanız, ilkeleriniz şöyle görünür:

   ```
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
        <redirect-content-urls />
        <base />
      </outbound>
      <on-error>
        <base />
      </on-error>
   </policies>
   ```

Bu bölümün geri kalanında, bu makalede ayarladığınız ilke dönüştürmeleri test edilmektedir.

### <a name="test-the-stripped-response-headers"></a>Gizlenmiş yanıt üst bilgilerini test etme

1. **Tanıtım Konferansı API 'si**  >  **sınaması**' nı seçin.
1. **Gethoparlörler** işlemini seçin ve **Gönder**' i seçin.

    Görebileceğiniz gibi, üst bilgiler de çıkarılır:

    :::image type="content" source="media/transform-api/final-response1.png" alt-text="Atılmış yanıt üstbilgileri":::

### <a name="test-the-replaced-url"></a>Değiştirilen URL’yi test etme

1. **Tanıtım Konferansı API 'si**  >  **sınaması**' nı seçin.
1. **Gethoparlörler** işlemini seçin ve **Gönder**' i seçin.

    Gördüğünüz gibi, URL değiştirilmiştir.

    :::image type="content" source="media/transform-api/final-response2.png" alt-text="URL 'YI değiştirdi":::

### <a name="test-the-rate-limit-throttling"></a>Hız sınırını test etme (azaltma)

1. **Tanıtım Konferansı API 'si**  >  **sınaması**' nı seçin.
1. **GetSpeakers** işlemini seçin. Bir satırda üç kez **Gönder** ' i seçin.

    3 kez isteği gönderdikten sonra, **429 fazla istek** yanıtı alırsınız.

    :::image type="content" source="media/transform-api/test-throttling.png" alt-text="Çok fazla istek":::

1. 15 saniye bekleyin ve yeniden **Gönder** ' i seçin. Bu defa **200 OK** yanıtını almanız gerekir.

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
