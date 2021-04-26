---
title: REST kullanarak Azure Media Services içeriği yayımlama
description: Akış URL 'SI oluşturmak için kullanılan bir bulucu oluşturmayı öğrenin. Kod REST API kullanır.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: ff332c30-30c6-4ed1-99d0-5fffd25d4f23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 650c0847942635e2a6a901db40ed0e51e9412057
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600072"
---
# <a name="publish-azure-media-services-content-using-rest"></a>REST kullanarak Azure Media Services içeriği yayımlama

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [Portal](media-services-portal-publish.md)
> 
> 

Bir Ondemandstreaming Locator ve akış URL 'SI oluşturarak bir uyarlamalı bit hızı MP4 kümesini akışla aktarabilirsiniz. [Bir varlık kodlama](media-services-rest-encode-asset.md) makalesi, uyarlamalı bit hızı bir MP4 kümesinin nasıl kodlanacağını gösterir. İçeriğiniz şifrelendiyse, bir bulucu oluşturmadan önce varlık teslim ilkesini ( [Bu](media-services-rest-configure-asset-delivery-policy.md) makalede açıklandığı gibi) yapılandırın. 

Ayrıca, aşamalı olarak indirilebilen MP4 dosyalarını işaret eden URL 'Ler oluşturmak için bir OnDemand akış Bulucu de kullanabilirsiniz.  

Bu makalede, varlığınızı yayımlamak ve sorunsuz, MPEG DASH ve HLS akış URL 'Leri derlemek için bir Ondemandstreaming Locator oluşturma gösterilmektedir. Ayrıca, aşamalı indirme URL 'Lerinin nasıl oluşturulacağını gösterir.

[Aşağıdaki](#types) bölümde, değerleri Rest çağrılarında kullanılan enum türleri gösterilmektedir.   

> [!NOTE]
> Media Services varlıklara erişirken, HTTP isteklerinizin belirli üstbilgi alanlarını ve değerlerini ayarlamanız gerekir. Daha fazla bilgi için bkz. [Media Services REST API Geliştirme Için kurulum](media-services-rest-how-to-use.md).
> 

## <a name="connect-to-media-services"></a>Media Services’e bağlanmak

AMS API 'sine bağlanma hakkında daha fazla bilgi için bkz. [Azure AD kimlik doğrulamasıyla Azure MEDIA SERVICES API 'Sine erişme](media-services-use-aad-auth-to-access-ams-api.md). 

>[!NOTE]
>Hizmetine başarıyla bağlandıktan sonra `https://media.windows.net` , başka bir MEDIA SERVICES URI belirten 301 yeniden yönlendirme alırsınız. Yeni URI 'ye sonraki çağrıları yapmanız gerekir.

## <a name="create-an-ondemand-streaming-locator"></a>Bir Ondemandstreaming Locator oluşturma
OnDemand akış bulucusu oluşturmak ve URL 'Leri almak için aşağıdakileri yapmanız gerekir:

1. İçerik şifrelendiyse, bir erişim ilkesi tanımlayın.
2. Bir Ondemandstreaming Bulucu oluşturun.
3. Akışı planlıyorsanız, varlığın akış bildirim dosyasını (. ISM) alın. 
   
   Aşamalı olarak indirmeyi planlıyorsanız, varlık içindeki MP4 dosyalarının adlarını alın. 
4. Bildirim dosyasının veya MP4 dosyalarının URL 'Lerini oluşturun. 
5. Yazma veya silme izinleri içeren bir AccessPolicy kullanarak akış Bulucu oluşturamazsınız.

### <a name="create-an-access-policy"></a>Erişim ilkesi oluşturma

>[!NOTE]
>Farklı AMS ilkeleri için sınır 1.000.000 ilkedir (örneğin, Bulucu ilkesi veya ContentKeyAuthorizationPolicy için). Aynı gün/erişim izinlerini (örneğin, uzun bir süre (karşıya yükleme olmayan ilkeler) yerinde kalması amaçlanan konum belirleyicilerinin ilkeleri gibi her zaman kullandığınız ilke KIMLIĞINI kullanın. Daha fazla bilgi için [Bu](media-services-dotnet-manage-entities.md#limit-access-policies) makaleye bakın.

İstek:

```console
POST https://media.windows.net/api/AccessPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
Host: media.windows.net
Content-Length: 68

{"Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}
```

Yanıt:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 311
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https:/media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3A69c80d98-7830-407f-a9af-e25f4b0d3e5f')
Server: Microsoft-IIS/8.5
request-id: a877528a-bdb4-4414-9862-273f8e64f882
x-ms-request-id: a877528a-bdb4-4414-9862-273f8e64f882
x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 18 Feb 2015 06:52:09 GMT

{"odata.metadata":"https://media.windows.net/api/$metadata#AccessPolicies/@Element","Id":"nb:pid:UUID:69c80d98-7830-407f-a9af-e25f4b0d3e5f","Created":"2015-02-18T06:52:09.8862191Z","LastModified":"2015-02-18T06:52:09.8862191Z","Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}
```

### <a name="create-an-ondemand-streaming-locator"></a>Bir Ondemandstreaming Locator oluşturma
Belirtilen varlık ve varlık ilkesi için bulucu oluşturun.

İstek:

```console
POST https://media.windows.net/api/Locators HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
Host: media.windows.net
Content-Length: 181

{"AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872Z","Type":2}
```
Yanıt:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 637
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Abe245661-2bbd-4fc6-b14f-9cf9a1492e5e')
Server: Microsoft-IIS/8.5
request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
x-ms-request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 18 Feb 2015 06:58:37 GMT

{"odata.metadata":"https://media.windows.net/api/$metadata#Locators/@Element","Id":"nb:lid:UUID:be245661-2bbd-4fc6-b14f-9cf9a1492e5e","ExpirationDateTime":"2015-03-20T06:34:47.267872+00:00","Type":2,"Path":"https://amstest1.streaming.mediaservices.windows.net/be245661-2bbd-4fc6-b14f-9cf9a1492e5e/","BaseUri":"https://amstest1.streaming.mediaservices.windows.net","ContentAccessComponent":"be245661-2bbd-4fc6-b14f-9cf9a1492e5e","AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872+00:00","Name":null}
```

### <a name="build-streaming-urls"></a>Akış URL 'Leri oluşturun
Pürüzsüz, HLS ve MPEG DASH URL 'Lerini derlemek için bulucunun oluşturulmasından sonra döndürülen **yol** değerini kullanın. 

Kesintisiz Akış: **yol** + bildirim dosyası adı + "/manifest"

örnek:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest`

HLS: **yol** + bildirim dosyası adı + "/manifest (format = M3U8-AAPL)"

örnek:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)`


DASH: **yol** + bildirim dosyası adı + "/manifest (biçim = MPD-Time-CSF)"

örnek:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)`


### <a name="build-progressive-download-urls"></a>Aşamalı indirme URL 'Leri oluşturma
Aşamalı indirme URL 'sini derlemek için bulucunun oluşturulmasından sonra döndürülen **yol** değerini kullanın.   

URL: **yol** + varlık dosyası MP4 adı

örnek:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`

## <a name="enum-types"></a><a id="types"></a>Sabit listesi türleri

```console
[Flags]
public enum AccessPermissions
{
    None = 0,
    Read = 1,
    Write = 2,
    Delete = 4,
    List = 8,
}

public enum LocatorType
{
    None = 0,
    Sas = 1,
    OnDemandOrigin = 2,
}
```

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Ayrıca bkz.
[Media Services işlemler REST API genel bakış](media-services-rest-how-to-use.md)

[Varlık teslim ilkesini yapılandırma](media-services-rest-configure-asset-delivery-policy.md)

