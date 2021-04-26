---
title: Kimlik doğrulama belirteçlerini Azure Media Services geçir | Microsoft Docs
description: İstemciden Azure Media Services anahtar teslim hizmetine kimlik doğrulama belirteçleri gönderme hakkında bilgi edinin
services: media-services
keywords: içerik koruma, DRM, belirteç kimlik doğrulaması
author: IngridAtMicrosoft
manager: femila
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 1fe692e1eb20956f339c9b861f50163cee9c5063
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564662"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>İstemcilerin belirteçleri Azure Media Services anahtar teslim hizmetine nasıl ilettireceğinizi öğrenin

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Müşteriler çoğu zaman Player 'ın anahtarı edinebilmesi için Azure Media Services anahtar teslim hizmetine belirteçleri nasıl geçirebilmesini ister. Media Services basit Web belirteci (SWT) ve JSON Web Token (JWT) biçimlerini destekler. Belirteç kimlik doğrulaması, sistemde ortak şifreleme veya Gelişmiş Şifreleme Standardı (AES) zarf şifrelemesi kullanıp kullanmayacağınızı bağımsız olarak herhangi bir anahtar türüne uygulanır.

 Hedeflediğiniz yürütücüye ve platforma bağlı olarak, aşağıdaki yollarla belirteci oyununuza geçirebilirsiniz:

- HTTP yetkilendirme üst bilgisi aracılığıyla.
    > [!NOTE]
    > "Taşıyıcı" ön eki, OAuth 2,0 özelliklerine göre beklenmektedir. Video kaynağını ayarlamak için **AES (JWT belirteci)** veya **AES (SWT belirteci)** öğesini seçin. Belirteç, yetkilendirme üst bilgisi aracılığıyla geçirilir.

- "Token = tokenvalue" ile bir URL sorgu parametresi ekleme.  
    > [!NOTE]
    > "Taşıyıcı" ön eki beklenmiyor. Belirteç bir URL aracılığıyla gönderildiğinden, belirteç dizesini koruma sağlamak gerekir. Burada nasıl yapılacağını gösteren bir C# örnek kodu verilmiştir:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- CustomData alanı aracılığıyla.
Bu seçenek, yalnızca PlayReady lisans alma sınaması 'nın CustomData alanı aracılığıyla PlayReady lisans alımı için kullanılır. Bu durumda, belirtecin XML belgesinin içinde burada açıklandığı gibi olması gerekir:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Kimlik doğrulama belirtecinizi belirteç öğesine yerleştirin.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
