---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: d5a483cb239893d04d2c2581fb378f411878f4ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102620175"
---
## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services)
* Azure Active Directory kimlik doğrulaması için yapılandırılmış bir tam ekran okuyucu kaynağı. Kurulumunu yapmak için [Bu yönergeleri](../../how-to-create-immersive-reader.md) izleyin.  Ortam özellikleri yapılandırılırken burada oluşturulan bazı değerler gerekir. Daha sonra başvurmak üzere oturumunuzun çıkışını bir metin dosyasına kaydedin.
* [Git](https://git-scm.com/).
* [Modern Okuyucu SDK 'sı](https://github.com/microsoft/immersive-reader-sdk).
* [Android Studio](https://developer.android.com/studio).

## <a name="configure-authentication-credentials"></a>Kimlik doğrulama kimlik bilgilerini yapılandırma

1. Android Studio başlatın ve projeyi **tam ekran-okuyucu-SDK/js/örnekler/hızlı başlangıç-Java-Android** dizininden (Java) veya tam ekran-- **SDK/js/örnekler/hızlı başlangıç-Kotlin** dizininden (Kotlin) açın.

1. **/Varlıklar** klasörünün içinde **env** adlı bir dosya oluşturun. Aşağıdaki adları ve değerleri ekleyin ve değerleri uygun şekilde sağlayın. Ortak yapılmaması gereken gizli dizileri içerdiğinden, bu dosyayı kaynak denetimine teslim etmeyin.
    
    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET=<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

## <a name="start-the-immersive-reader-with-sample-content"></a>Örnek içerikle modern okuyucu başlatma

AVD yöneticisinden bir cihaz öykünücüsü seçin ve projeyi çalıştırın.

## <a name="next-steps"></a>Sonraki adımlar

* [Modern Okuyucu SDK 'sını](https://github.com/microsoft/immersive-reader-sdk) ve [tam ekran okuyucu SDK başvurusunu](../../reference.md)bulun.
* [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)'daki kod örneklerini görüntüleyin.