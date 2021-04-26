---
title: Azure portal kullanarak bir içerik anahtarı yetkilendirme ilkesi yapılandırma | Microsoft Docs
description: Bu makalede, bir içerik anahtarı için yetkilendirme ilkesini yapılandırma gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 68bd4bd2472e80a663294745368fb505767a230a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103008323"
---
# <a name="configure-a-content-key-authorization-policy"></a>İçerik anahtarı yetkilendirme ilkesini yapılandırma

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Genel Bakış
 128-bit şifreleme anahtarları veya [PlayReady Digital Rights Management (DRM)](https://www.microsoft.com/playready/overview/)kullanarak GELIŞMIŞ ŞIFRELEME standardı (AES) Ile korunan MPEG-DASH, Kesintisiz Akış ve HTTP canlı akışı (HLS) akışları sunmak için Azure Media Services kullanabilirsiniz. Media Services ile Widevine DRM ile şifrelenmiş DASH akışları da sunabilirsiniz. PlayReady ve Widevine, ortak şifreleme (ISO/IEC 23001-7 CENC) belirtimi uyarınca şifrelenir.

Media Services Ayrıca, istemcilerin şifreli içeriği oynatmak için AES anahtarları veya PlayReady/Widevine lisansları alabileceği bir anahtar/lisans teslim hizmeti sağlar.

Bu makalede, içerik anahtarı yetkilendirme ilkesini yapılandırmak için Azure portal nasıl kullanılacağı gösterilmektedir. Anahtar daha sonra içeriğinizi dinamik olarak şifrelemek için kullanılabilir. Şu anda, HLS, MPEG-DASH ve Kesintisiz Akış biçimlerini şifreleyebilirsiniz. Aşamalı İndirmeleri şifrelenemez.

Oynatıcı, dinamik olarak şifrelenecek şekilde ayarlanmış bir akış istediğinde, Media Services AES veya DRM şifrelemesini kullanarak içeriğinizi dinamik olarak şifrelemek için yapılandırılmış anahtarı kullanır. Oynatıcı, akışın şifresini çözmek için anahtar teslim hizmetinden anahtarı ister. Kullanıcının anahtarı almak için yetkilendirilip yetkilendirilmediğini belirleme hizmeti, anahtar için belirttiğiniz yetkilendirme ilkelerini değerlendirir.

Birden çok içerik anahtarı olmasını planlıyorsanız veya Media Services anahtar teslim hizmeti dışında bir anahtar/lisans teslim hizmeti URL 'SI belirtmek istiyorsanız, Media Services .NET SDK veya REST API 'Lerini kullanın. Daha fazla bilgi için bkz.

* [Media Services .NET SDK 'sını kullanarak bir içerik anahtarı yetkilendirme ilkesi yapılandırma](media-services-dotnet-configure-content-key-auth-policy.md)
* [Media Services kullanarak bir içerik anahtarı yetkilendirme ilkesi yapılandırın REST API](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Bazı konular geçerlidir
* Media Services hesabınız oluşturulduğunda hesabınıza “Durdurulmuş” durumda bir varsayılan akış uç noktası eklenir. İçeriğinizi akışa başlamak ve dinamik paketleme ve dinamik şifreleme avantajlarından yararlanmak için, akış uç noktanızın "çalışıyor" durumunda olması gerekir. 
* Varlığınız bir uyarlamalı bit hızı MP4 'leri veya Uyarlamalı bit hızı Kesintisiz Akış dosyaları içermelidir. Daha fazla bilgi için bkz. [varlık kodlama](media-services-encode-asset.md).
* Anahtar teslim hizmeti, ContentKeyAuthorizationPolicy ve ilgili nesneleri (ilke seçenekleri ve kısıtlamalar) 15 dakika boyunca önbelleğe alır. Bir ContentKeyAuthorizationPolicy oluşturup bir belirteç kısıtlaması kullanmayı belirtebilir, test edebilir ve ardından ilkeyi açık kısıtlama olarak güncelleştirebilirsiniz. Bu işlem, ilke açık sürüme geçmeden yaklaşık 15 dakika sürer.
* Media Services akış uç noktası, "" joker karakteri olarak ön kontrol yanıtında CORS erişim-denetim-Izin-kaynak üst bilgisinin değerini ayarlar \* . Bu değer, Azure Media Player, Roku ve JWPlayer gibi birçok oyuncunun yanı sıra diğerleri için de geçerlidir. Ancak, kimlik bilgileri "içerme" olarak ayarlandığı için dash.js kullanan bazı oyuncular işe yaramazsa, bu dash.js XMLHttpRequest, "" joker karakterine \* Access-Control-Allow-Origin değeri olarak izin vermez. dash.js, bu sınırlamaya yönelik bir geçici çözüm olarak, istemcinizi tek bir etki alanından barındırdıysanız Media Services, bu etki alanını ön kontrol yanıt üstbilgisinde belirtebilir. Yardım için Azure portal aracılığıyla bir destek bileti açın.

## <a name="configure-the-key-authorization-policy"></a>Anahtar yetkilendirme ilkesini yapılandırma
Anahtar yetkilendirme ilkesini yapılandırmak için **IÇERIK koruma** sayfasını seçin.

Media Services, anahtar istekleri yapan kullanıcıların kimliğini doğrulamak için birden çok yolu destekler. İçerik anahtarı yetkilendirme ilkesi açık, belirteç veya IP yetkilendirme kısıtlamalarına sahip olabilir. (IP REST veya .NET SDK ile yapılandırılabilir.)

### <a name="open-restriction"></a>Açık kısıtlama
Açık kısıtlama, sistemin anahtarı bir anahtar isteği yapan herkese teslim eder anlamına gelir. Bu kısıtlama, test amacıyla yararlı olabilir.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>Belirteç kısıtlaması
Belirteç kısıtlı ilkesini seçmek için **belirteç** düğmesini seçin.

Belirteç kısıtlı ilkesi, bir güvenlik belirteci hizmeti (STS) tarafından verilmiş bir belirteç ile birlikte kullanılmalıdır. Media Services basit Web belirteci ([SWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_2)) ve JSON Web token (JWT) biçimlerindeki belirteçleri destekler. Daha fazla bilgi için bkz. [JWT kimlik doğrulaması](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Media Services STS sağlamıyor. Belirteçleri vermek için özel bir STS oluşturabilirsiniz. STS, belirtilen anahtarla imzalanmış bir belirteç oluşturacak ve belirteç kısıtlama yapılandırmasında belirttiğiniz talepler verecek şekilde yapılandırılmalıdır. Belirteç geçerliyse ve belirteçteki talepler içerik anahtarı için yapılandırılananlarla eşleşiyorsa, Media Services anahtar teslim hizmeti, istemciye şifreleme anahtarını döndürür.

Belirteç kısıtlı ilkesini yapılandırırken, birincil doğrulama anahtarını, verenin ve hedef kitle parametrelerini belirtmeniz gerekir. Birincil doğrulama anahtarı, belirtecin imzalandığı anahtarı içerir. Veren, belirteci veren STS 'dir. Hedef kitle (bazen kapsam olarak adlandırılır) belirtecin amacını veya belirtecin erişim yetkisi aldığı kaynağı açıklar. Media Services anahtar teslim hizmeti, belirteçteki bu değerlerin şablondaki değerlerle eşleştiğini doğrular.

### <a name="playready"></a>PlayReady
İçeriğinizi PlayReady ile koruduğunuzda, yetkilendirme ilkenizde belirtmeniz gereken işlemlerden biri PlayReady lisans şablonunu tanımlayan bir XML dizesidir. Varsayılan olarak, aşağıdaki ilke ayarlanır:

```xml
<PlayReadyLicenseResponseTemplate xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
  <LicenseTemplates>
    <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
      <ContentKey i:type="ContentEncryptionKeyFromHeader" />
      <LicenseType>Nonpersistent</LicenseType>
      <PlayRight>
        <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
      </PlayRight>
    </PlayReadyLicenseTemplate>
  </LicenseTemplates>
</PlayReadyLicenseResponseTemplate>
```

**İlke XML Al** düğmesini seçebilir ve [Media Services PlayReady lisans şablonuna genel bakış](media-services-playready-license-template-overview.md)' da tanımlanan XML şemasına uygun olan farklı bir XML sağlayabilirsiniz.

## <a name="additional-notes"></a>Ek notlar

* Widevine, Google Inc. tarafından sunulan bir hizmettir ve Google, Inc 'nin hizmet koşullarına ve gizlilik Ilkesine tabidir.

## <a name="next-steps"></a>Sonraki adımlar
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png
