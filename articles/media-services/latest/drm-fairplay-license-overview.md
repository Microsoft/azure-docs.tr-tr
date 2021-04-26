---
title: Apple FairPlay lisans desteğini Media Services
description: Bu konu, Apple FairPlay lisans gereksinimlerine ve yapılandırmasına genel bir bakış sunar.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: e17c6a719b472b37a4c7d72ba43934f36e114bb6
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106069196"
---
# <a name="apple-fairplay-license-requirements-and-configuration"></a>Apple FairPlay lisansı gereksinimleri ve yapılandırması

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services, HLS içeriğinizi **Apple Fairplay** (AES-128 CBC) ile şifrelemenizi sağlar. Media Services Ayrıca, FairPlay lisanslarını sunmaya yönelik bir hizmet sağlar. Bir oyuncu FairPlay korumalı içeriğinizi yürütmeye çalıştığında, lisans almak için lisans teslim hizmetine bir istek gönderilir. Lisans hizmeti isteği onayladığında, istemciye gönderilen lisansı yayınlar ve belirtilen içeriğin şifresini çözmek ve yürütmek için kullanılır.

Media Services Ayrıca, FairPlay lisanslarınızı yapılandırmak için kullanabileceğiniz API 'Ler de sağlar. Bu konu, FairPlay lisans gereksinimlerini tartışır ve Media Services API 'Leri kullanarak bir **Fairplay** lisansını nasıl yapılandırabileceğinizi gösterir. 

## <a name="requirements"></a>Gereksinimler

Media Services kullanırken, HLS içeriğinizi **Apple Fairplay** ile şifrelemek için ve Media Services kullanarak Fairplay lisanslarını teslim etmek için aşağıdakiler gerekir:

* [Apple geliştirme programı](https://developer.apple.com/)ile kaydolun.
* Apple, içerik sahibinin [dağıtım paketini](https://developer.apple.com/contact/fps/)almasını gerektirir. Anahtar güvenlik modülünü (KSM) Media Services ile zaten uygulamış ve son FPS paketini istediğini unutmayın. Son FPS paketinde sertifika oluşturmak ve uygulama gizli anahtarı (ASK) almak için yönergeler vardır. FairPlay yapılandırmak için sorma ' yı kullanın.
* Media Services anahtar/lisans teslim tarafında aşağıdaki şeyler ayarlanmalıdır:

    * **Uygulama sertifikası (AC)**: Bu, özel anahtarı içeren bir. pfx dosyasıdır. Bu dosyayı oluşturun ve bir parolayla şifreleyin. . Pfx dosyası Base64 biçiminde olmalıdır.

        Aşağıdaki adımlarda, FairPlay için bir. pfx sertifika dosyasının nasıl oluşturulacağı açıklanır:

        1. Kaynağından OpenSSL 'yi yükler https://slproweb.com/products/Win32OpenSSL.html .

            FairPlay sertifikasının ve Apple tarafından sunulan diğer dosyaların bulunduğu klasöre gidin.
        2. Komut satırından aşağıdaki komutu çalıştırın. Bu,. cer dosyasını. ped dosyasına dönüştürür.

            "C:\OpenSSL-Win32\bin\openssl.exe" x509-ın FairPlay. cer-Out FairPlay-out. ped
        3. Komut satırından aşağıdaki komutu çalıştırın. Bu,. PEA dosyasını özel anahtarla bir. pfx dosyasına dönüştürür. . Pfx dosyasının parolasına daha sonra OpenSSL sorulur.

            "C:\OpenSSL-Win32\bin\openssl.exe" PKCS12-Export-Out FairPlay-out. pfx-INKEY PrivateKey. ped-in FairPlay-out. pek-passin file:privatekey-pem-pass.txt
            
    * **Uygulama sertifikası parolası**:. pfx dosyası oluşturmak için parola.
    * **Sorun**: Bu anahtar, Apple Developer Portal 'ı kullanarak sertifika oluşturduğunuzda alınır. Her geliştirme ekibi, benzersiz bir sorun alır. ASK bir kopyasını kaydedin ve güvenli bir yerde saklayın. Media Services ile ASK FairPlayAsk olarak yapılandırmanız gerekir.
    
* Aşağıdaki noktalar, FPS istemci tarafında ayarlanmalıdır:

  * **Uygulama sertifikası (AC)**: Bu, işletim sisteminin bazı yükü şifrelemek için kullandığı ortak anahtarı içeren bir. cer/. der dosyasıdır. Oyuncu, oynatıcı için gerekli olduğu için Media Services hakkında bilgi sahibi olmalıdır. Anahtar teslim hizmeti, karşılık gelen özel anahtarı kullanarak onun şifresini çözer.

* FairPlay şifreli bir akış geri oynatmak için önce gerçek bir sorun alın ve ardından gerçek bir sertifika oluşturun. Bu işlem üç bölümden oluşur:

  * . der dosyası
  * . pfx dosyası
  * . pfx için parola
  
> [!NOTE]
> Azure Media Services paketleme veya anahtar teslimi sırasında sertifikanın sona erme tarihini denetlemez. Sertifikanın süresi dolduktan sonra çalışmaya devam edecektir.

## <a name="fairplay-and-player-apps"></a>FairPlay ve oynatıcı uygulamaları

İçeriğiniz **Apple Fairplay** ile şifrelendiğinde, tek tek video ve ses örnekleri **AES-128 CBC** modu kullanılarak şifrelenir. **Fairplay streaming** (fps), IOS ve Apple TV 'de yerel destek ile cihaz işletim sistemleriyle tümleşiktir. OS X üzerinde Safari, şifreli Medya Uzantıları (EME) arabirimi desteğini kullanarak FPS 'yi sunar.

Azure Media Player ayrıca FairPlay oynatmayı destekler. Daha fazla bilgi için bkz. [Azure Media Player belgeleri](https://amp.azure.net/libs/amp/latest/docs/index.html).

İOS SDK 'sını kullanarak kendi oynatıcı uygulamalarınızı geliştirebilirsiniz. FairPlay içeriğini oynatmak için, lisans değişim protokolünü uygulamanız gerekir. Bu protokol Apple tarafından belirtilmedi. Her bir uygulamaya, anahtar teslim istekleri gönderme işlemi yapılır. Media Services FairPlay anahtar teslimi hizmeti, aşağıdaki biçimde SPC 'nin bir www-form-URL kodlamalı gönderi iletisi olarak gelmesini bekler:

```
spc=<Base64 encoded SPC>
```

## <a name="fairplay-configuration-net-example"></a>FairPlay yapılandırması .NET örneği

FairPlay lisanslarını yapılandırmak için Media Services API kullanabilirsiniz. Oynatıcı FairPlay korumalı içeriğinizi yürütmeye çalıştığında, lisans almak için lisans teslim hizmetine bir istek gönderilir. Lisans hizmeti isteği onayladığında, hizmet lisansı yayınlar. İstemciye gönderilir ve belirtilen içeriğin şifresini çözmek ve yürütmek için kullanılır.

> [!NOTE]
> Genellikle yalnızca bir sertifika kümesine ve SORMANıZ gerektiğinden, FairPlay ilke seçeneklerini yalnızca bir kez yapılandırmak isteyeceksiniz.

Aşağıdaki örnek, lisansı yapılandırmak için [.NET SDK Media Services](/dotnet/api/microsoft.azure.management.media.models) kullanır.

```csharp
private static ContentKeyPolicyFairPlayConfiguration ConfigureFairPlayPolicyOptions()
{

    string askHex = "";
    string FairPlayPfxPassword = "";

    var appCert = new X509Certificate2("FairPlayPfxPath", FairPlayPfxPassword, X509KeyStorageFlags.Exportable);

    byte[] askBytes = Enumerable
        .Range(0, askHex.Length)
        .Where(x => x % 2 == 0)
        .Select(x => Convert.ToByte(askHex.Substring(x, 2), 16))
        .ToArray();

    ContentKeyPolicyFairPlayConfiguration fairPlayConfiguration =
    new ContentKeyPolicyFairPlayConfiguration
    {
        Ask = askBytes,
        FairPlayPfx =
                Convert.ToBase64String(appCert.Export(X509ContentType.Pfx, FairPlayPfxPassword)),
        FairPlayPfxPassword = FairPlayPfxPassword,
        RentalAndLeaseKeyType =
                ContentKeyPolicyFairPlayRentalAndLeaseKeyType
                .PersistentUnlimited,
        RentalDuration = 2249 // in seconds
    };

    return fairPlayConfiguration;
}
```

## <a name="next-steps"></a>Sonraki adımlar

[DRM ile koruma](drm-protect-with-drm-tutorial.md)
