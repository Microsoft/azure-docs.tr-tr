---
title: Öğretici-istek izlemeyi kullanarak Azure API Management 'da API 'Leri hata ayıklama
description: Azure API Management 'de istek işleme adımlarını izlemeyi ve incelemeyi etkinleştirmek için Bu öğreticinin adımlarını izleyin.
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: apimpm
ms.openlocfilehash: 531e346569b85ababc382f997fd7764a92b3d05f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542323"
---
# <a name="tutorial-debug-your-apis-using-request-tracing"></a>Öğretici: istek izlemeyi kullanarak API 'lerinizin hatalarını ayıklama

Bu öğreticide, API 'nize hata ayıklamanıza ve sorun gidermenize yardımcı olmak üzere Azure API Management istek işlemenin nasıl denetleneceği açıklanır. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Örnek bir çağrı izleyin
> * İstek işleme adımlarını gözden geçirin

:::image type="content" source="media/api-management-howto-api-inspector/api-inspector-001.png" alt-text="API denetçisi":::

## <a name="prerequisites"></a>Önkoşullar

+ [Azure API Management terminolojisini](api-management-terminology.md) öğrenin.
+ Şu hızlı başlangıcı tamamlayın: [Azure API Management örneği oluşturma](get-started-create-service-instance.md).
+ Aşağıdaki öğreticiyi doldurun: [Ilk API 'Nizi Içeri aktarma ve yayımlama](import-and-publish.md).

## <a name="verify-allow-tracing-setting"></a>İzleme ayarına izin ver ayarını doğrula 

API 'niz için kullanılan aboneliğin **Izlemeye Izin ver** ayarı etkinleştirilmelidir. Yerleşik tüm erişim aboneliğini kullanıyorsanız, varsayılan olarak etkindir. Portalda doğrulamak için API Management örneğinize gidin ve **abonelikler**' i seçin.

   :::image type="content" source="media/api-management-howto-api-inspector/allow-tracing.png" alt-text="Abonelik için izlemeye izin ver":::

## <a name="trace-a-call"></a>Çağrı izleme

1. [Azure Portal](https://portal.azure.com)oturum açın ve API Management örneğinize gidin.
1. **API’ler** seçeneğini belirleyin.
1. API listenizden  **Tanıtım Konferansı API 'si** ' ni seçin.
1. **Test** sekmesini seçin.
1. **GetSpeakers** işlemini seçin.
1. HTTP istek üst bilgisinin **OCP-apim-Trace: true** ve **OCP-apim-Subscription-Key** için geçerli bir değer içerdiğini doğrulayın. Değilse, üstbilgiyi eklemek için **+ üst bilgi Ekle** ' yi seçin.
1. API çağrısı yapmak için **Gönder** ' i seçin.

  :::image type="content" source="media/api-management-howto-api-inspector/06-debug-your-apis-01-trace-call.png" alt-text="API izlemeyi yapılandırma":::

> [!TIP]
> **OCP-apim-Subscription-Key** http isteğinde otomatik olarak doldurulmazsa, bunu portalda alabilirsiniz. **Abonelikler**' i seçin ve sürdürücriklama için bağlam menüsünü (**...**) açın. **Anahtarları göster/gizle**' yi seçin. Gerekirse anahtarları da yeniden oluşturabilirsiniz. Ardından, üstbilgiye bir anahtar ekleyin.

## <a name="review-trace-information"></a>İzleme bilgilerini gözden geçirme

1. Çağrı tamamlandıktan sonra, **http yanıtında** **izleme** sekmesine gidin.
1. Ayrıntılı izleme bilgilerine geçmek için aşağıdaki bağlantılardan birini seçin: **gelen**, **arka uç**, **giden**.

     :::image type="content" source="media/api-management-howto-api-inspector/response-trace.png" alt-text="Gözden geçirme yanıtı izleme":::

    * **Gelen** -çağırandan alınan API Management özgün isteği ve isteğe uygulanan ilkeleri gösterir. Örneğin, Öğreticiye bir ilke eklediyseniz, [API 'Nizi dönüştürün ve koruyun](transform-api.md), burada görünür.

    * **Arka uç** -API arka ucuna gönderilen istekleri ve aldığı yanıtı gösterir API Management.

    * **Giden** -çağırana geri göndermeden önce yanıta uygulanan ilkeleri gösterir.

    > [!TIP]
    > Her bir adım, isteğin API Yönetimi tarafından alınmasından bu yana geçen süreyi de gösterir.

1. **İleti** sekmesinde, **OCP-apim-Trace-location** üst bilgisi, Azure Blob depolama alanında depolanan izleme verilerinin konumunu gösterir. Gerekirse, izlemeyi almak için bu konuma gidin.

     :::image type="content" source="media/api-management-howto-api-inspector/response-message.png" alt-text="Azure Storage 'da izleme konumu":::
## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Örnek bir çağrı izleyin
> * İstek işleme adımlarını gözden geçirin

Sonraki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
> [Düzeltmeleri kullanma](api-management-get-started-revise-api.md)
