---
title: Kimlik doğrulama belirteçlerini v3 Media Services geçir | Microsoft Docs
description: İstemciden kimlik doğrulama belirteçlerini Media Services v3 anahtar teslim hizmetine nasıl göndereceğinizi öğrenin
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 590309ad392876ba3c5cb6d21abe777ab5a93efb
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282322"
---
# <a name="pass-tokens-to-the-azure-media-services-v3-key-delivery-service"></a>Belirteçleri Azure Media Services v3 anahtar teslim hizmetine geçirin

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Müşteriler çoğu zaman Player 'ın anahtarı edinebilmesi için Azure Media Services anahtar teslim hizmetine belirteçleri nasıl geçirebilmesini ister. Media Services basit Web belirteci (SWT) ve JSON Web Token (JWT) biçimlerini destekler. Belirteç kimlik doğrulaması, sistemde ortak şifreleme veya Gelişmiş Şifreleme Standardı (AES) zarf şifrelemesi kullanıp kullanmayacağınızı bağımsız olarak herhangi bir anahtar türüne uygulanır.

 Hedeflediğiniz yürütücüye ve platforma bağlı olarak, aşağıdaki yollarla belirteci oyununuza geçirebilirsiniz:

## <a name="pass-a-token-through-the-http-authorization-header"></a>HTTP yetkilendirme üst bilgisi aracılığıyla belirteç geçirme

> [!NOTE]
> "Taşıyıcı" ön eki, OAuth 2,0 özelliklerine göre beklenmektedir. Video kaynağını ayarlamak için **AES (JWT belirteci)** veya **AES (SWT belirteci)** öğesini seçin. Belirteç, yetkilendirme üst bilgisi aracılığıyla geçirilir.

## <a name="pass-a-token-via-the-addition-of-a-url-query-parameter-with-tokentokenvalue"></a>"Token = tokenvalue" ile bir URL sorgu parametresi ekleme yoluyla belirteç geçirin.

> [!NOTE]
> "Taşıyıcı" ön eki beklenmiyor. Belirteç bir URL aracılığıyla gönderildiğinden, belirteç dizesini koruma sağlamak gerekir. Burada nasıl yapılacağını gösteren bir C# örnek kodu verilmiştir:

```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
```

## <a name="pass-a-token-through-the-customdata-field"></a>CustomData alanı aracılığıyla bir belirteç geçirin

Bu seçenek, yalnızca PlayReady lisans alma sınaması 'nın CustomData alanı aracılığıyla PlayReady lisans alımı için kullanılır. Bu durumda, belirtecin XML belgesinin içinde burada açıklandığı gibi olması gerekir:

```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
```

Kimlik doğrulama belirtecinizi belirteç öğesine yerleştirin.
