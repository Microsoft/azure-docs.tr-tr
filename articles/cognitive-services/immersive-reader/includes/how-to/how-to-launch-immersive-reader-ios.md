---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: a3345ff9a6cc1d5f8e2fbc78c2a76ba6f183aeb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102620199"
---
## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services)
* Azure Active Directory kimlik doğrulaması için yapılandırılmış bir tam ekran okuyucu kaynağı. Kurulumunu yapmak için [Bu yönergeleri](../../how-to-create-immersive-reader.md) izleyin.  Ortam özellikleri yapılandırılırken burada oluşturulan bazı değerler gerekir. Daha sonra başvurmak üzere oturumunuzun çıkışını bir metin dosyasına kaydedin.
* [MacOS](https://www.apple.com/macos).
* [Git](https://git-scm.com/).
* [Modern Okuyucu SDK 'sı](https://github.com/microsoft/immersive-reader-sdk).
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="configure-authentication-credentials"></a>Kimlik doğrulama kimlik bilgilerini yapılandırma

1. Xcode ' u açın ve **immersive-Reader-SDK/js/Samples/iOS/QuickStart-Swift/QuickStart-Swift. XCODEPROJ** dosyasını açın.
1. Üstteki menüde **ürün**  >  **düzeni**  >  **düzenleme şeması**' nı seçin.
1. **Çalıştır** görünümünde, **bağımsız değişkenler** sekmesini seçin.
1. **Ortam değişkenleri** bölümünde aşağıdaki adları ve değerleri ekleyin. Tam ekran okuyucuyu oluştururken verilen değerleri sağlayın.

    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

Ortak yapılmaması gereken gizli dizileri içerdiğinden bu değişikliği kaynak denetimine oluşturmayın.

## <a name="start-the-immersive-reader-with-sample-content"></a>Örnek içerikle modern okuyucu başlatma

Xcode 'da projeyi çalıştırmak için **CTRL + R** ' yi seçin.

## <a name="next-steps"></a>Sonraki adımlar

* [Modern Okuyucu SDK 'sını](https://github.com/microsoft/immersive-reader-sdk) ve [tam ekran okuyucu SDK başvurusunu](../../reference.md)bulun.
* [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)'daki kod örneklerini görüntüleyin.
