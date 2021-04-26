---
title: Azure Media Services sık sorulan sorular
description: Bu makalede Azure Media Services hakkında sık sorulan sorulara yanıtlar verilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 220aae64bd9ec493af8c8ee61901e27027dc9798
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013389"
---
# <a name="media-services-v2-frequently-asked-questions"></a>Media Services V2 sık sorulan sorular

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Bu makalede, Azure Media Services (AMS) Kullanıcı topluluğu tarafından oluşturulan sık sorulan sorular ele alınmaktadır.

## <a name="general-ams-faqs"></a>Genel AMS SSS

S: Apple iOS cihazlarına nasıl Akışlarım?

A: "(format = M3U8-AAPL)" URL 'sinin "/manifest" yolunu, akış kaynak sunucusuna Apple iOS Native cihazlarda tüketim için içerik geri döndürmesini söylemek için (Ayrıntılar için bkz. [içerik sunma](media-services-deliver-content-overview.md)),

S: Dizin oluşturmayı nasıl ölçeklendirebilirsiniz?

Y: ayrılmış birimler kodlama ve dizin oluşturma görevleri için aynıdır. [Kodlama ayrılmış birimlerinin ölçeklendirilmesi](media-services-scale-media-processing-overview.md)hakkındaki yönergeleri izleyin. Dizin Oluşturucu performansının ayrılmış birim türünden etkilenmediğini **unutmayın** .

S: bir videoyu karşıya yükledim, kodlıyorum ve yayımladım. Akışa almaya çalıştığımda videonun oynatılmasının nedeni ne olur?

Y: en yaygın nedenlerden biri, **çalışan** durumunda kayıttan yürütmeye çalıştığınız akış uç noktasına sahip değildir.  

S: canlı bir akışta birleştirme yapabilir miyim?

Y: canlı akışlarda birleştirme Şu anda Azure Media Services sunulmaz, bu nedenle bilgisayarınızda önceden oluşturmanız gerekir.

S: canlı akışla Azure CDN kullanabilir miyim?

A: Media Services Azure CDN tümleştirmesini destekler (daha fazla bilgi için, bkz. [Media Services hesapta akış uç noktalarını yönetme](media-services-portal-manage-streaming-endpoints.md)).  CDN ile canlı akış kullanabilirsiniz. Azure Media Services, Kesintisiz Akış, HLS ve MPEG-DASH çıkışları sağlar. Tüm bu biçimler, verileri aktarmak ve HTTP önbelleklemesi avantajlarından yararlanmak için HTTP kullanır. Canlı akışta gerçek video/ses verileri parçalara bölünür ve bu tek parçalar CDN 'de önbelleğe alınır. Yalnızca verilerin yenilenmesi gerekir, bildirim verileri. CDN, bildirim verilerini düzenli aralıklarla yeniler.

S: Azure Media Services görüntüleri depolamayı destekliyor mu?

Y: yalnızca JPEG veya PNG görüntülerini depolamak istiyorsanız, bunları Azure Blob depolamada tutmanız gerekir. Bunları, video veya ses varlıklarınızla ilişkili tutmak istemediğiniz müddetçe Media Services hesabınıza yerleştirmenin bir avantajı yoktur. Ya da görüntüleri video Kodlayıcısı 'nda yer paylaşımları olarak kullanmanız gerekiyorsa. Media Encoder Standard, videoların üzerinde görüntülerin üzerine yerleşimi destekler ve desteklenen giriş biçimleri olarak JPEG ve PNG 'yi listeler. Daha fazla bilgi için bkz. [yer paylaşımları oluşturma](media-services-advanced-encoding-with-mes.md#overlay).

S: bir Media Services hesabından diğerine varlıkları nasıl kopyalayabilirim?

A: bir Media Services hesabından .NET kullanarak varlıkları kopyalamak Için, [Azure Media Services .NET SDK uzantıları](https://github.com/Azure/azure-sdk-for-media-services-extensions/) deposundaki [ıvarlık. Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) genişletme yöntemini kullanın. Daha fazla bilgi için [Bu](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) Forum iş parçacığına bakın.

S: AMS ile çalışırken dosyaları adlandırmak için desteklenen karakterler nelerdir?

A: Media Services, akış içeriği için URL 'Ler oluştururken IAssetFile.Name özelliğinin değerini kullanır (örneğin, http://{AMSAccount}. Origin. mediaservices. Windows. net/{GUID}/{ıassetfile. Name}/streamingParameters.) Bu nedenle, yüzde kodlamalı izin verilmez. **Name** özelliğinin değeri, Şu sayıda [kodlamaya ayrılan karakterlerden](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)herhangi birini içeremez:! * ' ();: @ &= + $,/?% # [] ". Ayrıca, dosya adı uzantısı için yalnızca bir '. ' olabilir.

S: REST kullanarak bağlanma

Y: AMS API 'sine bağlanma hakkında daha fazla bilgi Için bkz. [Azure AD kimlik doğrulamasıyla Azure MEDIA SERVICES API 'Sine erişme](media-services-use-aad-auth-to-access-ams-api.md). 

S: kodlama işlemi sırasında bir videoyu nasıl döndürebilirim?

Y: [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) , 90/180/270 açılara dönüşü destekler. Varsayılan davranış, gelen MP4/MOV dosyasındaki döndürme meta verilerini algılamaya ve bu dosyada telafi etmeye çalıştığı "Auto" dır. [Burada](media-services-mes-presets-overview.md)tanımlanan JSON önayarlarından birine aşağıdaki **Sources** öğesini ekleyin:

```json
"Version": 1.0,
"Sources": [
{
  "Streams": [],
  "Filters": {
    "Rotation": "90"
  }
}
],
"Codecs": [

...
```


## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
