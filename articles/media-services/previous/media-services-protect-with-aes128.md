---
title: AES-128 dinamik şifrelemesini ve anahtar teslim hizmetini kullanın | Microsoft Docs
description: Microsoft Azure Media Services kullanarak içeriğinizi AES 128 bit şifreleme anahtarlarıyla şifrelendi şekilde sunun. Media Services, yetkili kullanıcılara şifreleme anahtarları sunan anahtar teslim hizmetini de sağlar. Bu konu, AES-128 ile dinamik olarak nasıl şifreleneceğini ve anahtar teslim hizmetini nasıl kullanacağınızı gösterir.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 2b96d968cb1ad2ec903dbf9788e1fbae22bd2b7d
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/12/2019
ms.locfileid: "69014962"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>AES-128 dinamik şifrelemesini ve anahtar teslim hizmetini kullanın
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>  

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>En son sürüm olan [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/)’ü inceleyin. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-from-v2-to-v3.md)

Media Services kullanarak, 128 bit şifreleme anahtarlarını kullanarak HTTP Canlı Akışı (HLS) ve AES ile şifreli Kesintisiz Akış sağlayabilirsiniz. Media Services, yetkili kullanıcılara şifreleme anahtarları sunan anahtar teslim hizmetini de sağlar. Bir varlığı şifrelemek Media Services istiyorsanız, bir şifreleme anahtarını varlıkla ilişkilendirir ve ayrıca anahtar için yetkilendirme ilkelerini yapılandırırsınız. Bir Player tarafından bir akış istendiğinde, Media Services,, AES şifrelemesi kullanarak içeriğinizi dinamik olarak şifrelemek için belirtilen anahtarı kullanır. Oynatıcı, akışın şifresini çözmek için anahtar teslim hizmetinden anahtarı ister. Kullanıcının anahtarı almak için yetkilendirilip yetkilendirilmediğini belirleme hizmeti, anahtar için belirttiğiniz yetkilendirme ilkelerini değerlendirir.

Media Services, anahtar isteğinde bulunan kullanıcıların kimlik doğrulamasını yapmanın birden çok yöntemini destekler. İçerik anahtarı yetkilendirme ilkesinin açık veya belirteç kısıtlaması şeklinde bir veya daha fazla yetkilendirme kısıtlaması olabilir. Belirteç kısıtlamalı ilkenin beraberinde bir güvenlik belirteci hizmeti (STS) tarafından verilmiş bir belirteç bulunmalıdır. Media Services, [basit web belirteci](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) ve [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) biçimlerindeki belirteçleri destekler. Daha fazla bilgi edinmek için bkz. [İçerik anahtarının yetkilendirme ilkesini yapılandırma](media-services-protect-with-aes128.md#configure_key_auth_policy).

Dinamik şifrelemeden yararlanmak için, bir grup çoklu bit hızlı MP4 dosyası ya da çoklu bit hızlı Kesintisiz Akış kaynak dosyası içeren bir varlığınız olması gerekir. Ayrıca, varlık için teslim ilkesini yapılandırmanız gerekir (Bu makalenin ilerleyen kısımlarında açıklanmıştır). Ardından, akış URL'sinde belirtilen biçime bağlı olarak, isteğe bağlı akış sunucusu akışın seçtiğiniz protokolde teslim edilmesini sağlar. Sonuç olarak, yalnızca dosyaları tek depolama biçiminde depolamanız ve ödemeniz gerekir. Media Services, bir istemciden alınan isteklere göre uygun yanıtı oluşturur ve sunar.

Bu makale, korumalı medya teslim eden uygulamalar üzerinde çalışan geliştiriciler için yararlıdır. Makalede, yalnızca yetkili istemcilerin şifreleme anahtarları alabilmesi için, anahtar teslim hizmetinin yetkilendirme ilkeleriyle nasıl yapılandırılacağı gösterilir. Ayrıca, dinamik şifrelemeyi nasıl kullanacağınızı gösterir.

MacOS 'ta Safari 'ye teslim edilmek üzere Gelişmiş Şifreleme Standardı (AES) ile içerik şifreleme hakkında daha fazla bilgi için [Bu blog gönderisine](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)bakın.
Medya içeriğinizi AES şifrelemesiyle nasıl koruyabileceğiniz konusunda genel bir bakış için [Bu videoya](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption)bakın.


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>AES-128 dinamik şifreleme ve anahtar teslimi hizmeti iş akışı

Media Services anahtar teslim hizmetini ve ayrıca dinamik şifrelemeyi kullanarak, varlıklarınızı AES ile şifrelerken aşağıdaki genel adımları gerçekleştirin:

1. [Bir varlık oluşturun ve dosyaları varlığa yükleyin](media-services-protect-with-aes128.md#create_asset).

2. [Dosyayı içeren varlığı Uyarlamalı bit hızı MP4 kümesine kodlayın](media-services-protect-with-aes128.md#encode_asset).

3. [Bir içerik anahtarı oluşturun ve kodlanmış varlıkla ilişkilendirin](media-services-protect-with-aes128.md#create_contentkey). Media Services’de, içerik anahtarı varlığın şifreleme anahtarını içerir.

4. [İçerik anahtarının yetkilendirme Ilkesini yapılandırın](media-services-protect-with-aes128.md#configure_key_auth_policy). İçerik anahtarı yetkilendirme ilkesini yapılandırmanız gerekir. İçerik anahtarının istemciye teslim edilebilmesi için önce istemcinin ilkeyi karşılaması gerekir.

5. [Bir varlık için teslim Ilkesini yapılandırın](media-services-protect-with-aes128.md#configure_asset_delivery_policy). Teslim ilkesi yapılandırması, anahtar alma URL 'SI ve bir başlatma vektörü (IV) içerir. (AES-128 şifreleme ve şifre çözme için aynı IV gerektirir.) Yapılandırma ayrıca teslim protokolünü (örneğin, MPEG-DASH, HLS, Kesintisiz Akış veya All) ve dinamik şifreleme türünü (örneğin, zarf veya dinamik şifreleme yok) içerir.

    Bir varlıktaki her bir protokole farklı birer ilke uygulayabilirsiniz. Örneğin, Kesintisiz/DASH için PlayReady şifreleme uygularken HLS için bir AES zarfı uygulayabilirsiniz. Bir teslim ilkesinde tanımlanmayan tüm protokollerin akışı engellenir. (Protokol olarak yalnızca HLS 'yi belirten tek bir ilke eklerseniz bu örnek bir örnektir.) Bunun tek istisnası, hiçbir varlık teslim ilkesinin tanımlanmadığı durumdur. Bu halde tüm protokollere açık bir şekilde izin verilir.

6. Akış URL 'SI almak için [bir OnDemand Bulucu oluşturun](media-services-protect-with-aes128.md#create_locator) .

Makalede ayrıca [bir istemci uygulamasının anahtar teslim hizmetinden bir anahtar isteme yöntemi](media-services-protect-with-aes128.md#client_request)gösterilmektedir.

Makalenin sonunda, tamamlanmış bir [.net örneği](media-services-protect-with-aes128.md#example) bulabilirsiniz.

Aşağıdaki görüntüde, daha önce açıklanan iş akışı gösterilmektedir. Burada kimlik doğrulaması için belirteç kullanılmaktadır.

![AES-128 ile koruma](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

Bu makalenin geri kalanında, açıklamalar, kod örnekleri ve daha önce açıklanan görevlerin nasıl elde edilebilmesi gerektiğini gösteren konuların bağlantıları yer almaktadır.

## <a name="current-limitations"></a>Geçerli sınırlamalar
Varlığınızın teslim ilkesini ekler veya güncelleştirirseniz, varsa mevcut bulucuyu silip yeni bir bulucu oluşturmanız gerekir.

## <a id="create_asset"></a>Varlık oluşturma ve dosyaları varlığa yükleme
Videolarınızı yönetmek, kodlamak ve akışla aktarmak için önce içeriğinizi Media Services’a yüklemeniz gerekir. Dosyanın karşıya yüklenmesinin ardından içeriğiniz, sonraki işleme ve akışla aktarma faaliyetleri için güvenli bir şekilde bulutta depolanır. 

Daha fazla bilgi için bkz. [Media Services hesabına dosya yükleme](media-services-dotnet-upload-files.md).

## <a id="encode_asset"></a>Dosyayı içeren varlığı Uyarlamalı bit hızı MP4 kümesine kodla
Dinamik şifreleme ile bir grup çoklu bit hızlı MP4 dosyası ya da çoklu bit hızlı Kesintisiz Akış kaynak dosyası içeren bir varlık oluşturursunuz. Daha sonra, bildirim veya parça isteğindeki belirtilen biçime bağlı olarak, isteğe bağlı akış sunucusu akışı seçtiğiniz protokolde almanızı sağlar. Daha sonra, dosyaları yalnızca tek bir depolama biçiminde depolamanız ve ödemenizi yeterlidir. Media Services, bir istemciden alınan isteklere göre uygun yanıtı oluşturur ve sunar. Daha fazla bilgi için bkz. [Dinamik paketlemeye genel bakış](media-services-dynamic-packaging-overview.md).

>[!NOTE]
>Media Services hesabınız oluşturulduğunda hesabınıza “Durdurulmuş” durumda bir varsayılan akış uç noktası eklenir. İçerik akışını başlatmak ve dinamik paketleme ile dinamik şifrelemeden yararlanmak için içerik akışı yapmak istediğiniz akış uç noktasının “Çalışıyor” durumda olması gerekir. 
>
>Ayrıca, dinamik paketleme ve dinamik şifrelemeyi kullanmak için, varlığınızın bir uyarlamalı bit hızı MP4 'leri veya Uyarlamalı bit hızı Kesintisiz Akış dosyası içermesi gerekir.

Kodlama yönergeleri için bkz. [Media Encoder Standard kullanarak varlık kodlama](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a id="create_contentkey"></a>Bir içerik anahtarı oluşturup kodlanmış varlıkla ilişkilendirme
Media Services’de, içerik anahtarı bir varlığı şifrelerken kullanmak istediğiniz anahtarı içerir.

Daha fazla bilgi için bkz. [İçerik anahtarı oluşturma](media-services-dotnet-create-contentkey.md).

## <a id="configure_key_auth_policy"></a>İçerik anahtarının yetkilendirme ilkesini yapılandırma
Media Services, anahtar isteğinde bulunan kullanıcıların kimlik doğrulamasını yapmanın birden çok yöntemini destekler. İçerik anahtarı yetkilendirme ilkesini yapılandırmanız gerekir. Anahtarın istemciye teslim edilebilmesi için istemci (oynatıcı) ilkeyi karşılaması gerekir. İçerik anahtarı Yetkilendirme ilkesinde, açık, belirteç kısıtlaması veya IP kısıtlaması olmak üzere bir veya daha fazla yetkilendirme kısıtlaması olabilir.

Daha fazla bilgi edinmek için bkz. [İçerik anahtarı yetkilendirme ilkesi yapılandırma](media-services-dotnet-configure-content-key-auth-policy.md).

## <a id="configure_asset_delivery_policy"></a>Varlık teslim ilkesi yapılandırma
Varlığınıza ilişkin teslim ilkesini yapılandırın. Varlık teslim ilkesi yapılandırmasının içerdiklerinden bazıları şunlardır:

* Anahtar alma URL 'SI. 
* Zarf şifrelemesi için kullanılacak başlatma vektörü (IV). AES-128 şifreleme ve şifre çözme için aynı IV gerektirir. 
* Varlık teslim protokolü (örneğin MPEG DASH, HLS, Kesintisiz Akış veya tümü).
* Dinamik şifrelemenin türü (örneğin, AES zarfı) veya dinamik şifreleme yoktur. 

Daha fazla bilgi için bkz. [Varlık teslim ilkesi yapılandırma](media-services-dotnet-configure-asset-delivery-policy.md).

## <a id="create_locator"></a>Akış URL’si almak için bir OnDemand akış bulucusu oluşturma
Kullanıcınıza Kesintisiz Akış, DASH veya HLS için akış URL’sini sağlamanız gerekir.

> [!NOTE]
> Varlığınızın teslim ilkesini ekler veya güncelleştirirseniz, varsa mevcut bulucuyu silip yeni bir bulucu oluşturmanız gerekir.
> 
> 

Varlık yayımlama ve akış URL'si oluşturma yönergeleri için bkz. [Akış URL'si oluşturma](media-services-deliver-streaming-content.md).

## <a name="get-a-test-token"></a>Test belirteci alma
Anahtar yetkilendirme ilkesi için kullanılan belirteç kısıtlamasına dayalı olarak bir test belirteci alın.

```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word "Bearer" in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
```

Akışınızı test etmek için [Azure Media Services Oynatıcısı](https://aka.ms/azuremediaplayer)’nı kullanabilirsiniz.

## <a id="client_request"></a>İstemciniz, anahtar teslim hizmetinden bir anahtar talep edebilir mi?
Önceki adımda, bir bildirim dosyasına işaret eden URL 'YI inşa edersiniz. Anahtar teslim hizmetine bir istek yapmak için istemciniz, akış bildirim dosyalarından gerekli bilgileri ayıklaması gerekir.

### <a name="manifest-files"></a>Bildirim dosyaları
İstemci, bildirim dosyasından URL 'YI (içerik anahtar KIMLIĞI [KID] da içerir) ayıklaması gerekir. İstemci daha sonra anahtar teslim hizmetinden şifreleme anahtarını almaya çalışır. İstemcinin Ayrıca IV değerini ayıklaması ve akışın şifresini çözmek için kullanması gerekir. Aşağıdaki kod parçacığında kesintisiz akış bildiriminin `<Protection>` öğesi gösterilmektedir:

```xml
    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>
```

HLS söz konusu olduğunda, kök bildirimi kesim dosyalarına bozulur. 

Örneğin, kök bildirimi: http:\//test001.Origin.mediaservices.Windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest (format = M3U8-AAPL). Segment dosya adlarının bir listesini içerir.

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Segment dosyalarından birini bir metin düzenleyicisinde açarsanız (örneğin, http:\//test001.Origin.mediaservices.Windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels (514369)/manifest (video, biçim = M3U8-AAPL), dosyanın şifrelendiğini belirten #EXT-X anahtarı içerir.

    #EXTM3U
    #EXT-X-VERSION:4
    #EXT-X-ALLOW-CACHE:NO
    #EXT-X-MEDIA-SEQUENCE:0
    #EXT-X-TARGETDURATION:9
    #EXT-X-KEY:METHOD=AES-128,
    URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
         kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
            IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
    #EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
    #EXTINF:8.425708,no-desc
    Fragments(video=0,format=m3u8-aapl)
    #EXT-X-ENDLIST

>[!NOTE] 
>Safari 'de AES şifreli bir HLS oynamasını planlıyorsanız, [Bu bloga](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)bakın.

### <a name="request-the-key-from-the-key-delivery-service"></a>Anahtar teslim hizmetinden anahtar isteyin

Aşağıdaki kod, anahtar teslim URI 'Si (bildirimden ayıklanan) ve bir belirteç kullanarak Media Services anahtar teslim hizmetine nasıl istek gönderileceğini gösterir. (Bu makale bir STS 'den SWTs alma hakkında açıklamaz.)

```csharp
    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);

        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;

        var response = request.GetResponse();

        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }

        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();

        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }
```

## <a name="protect-your-content-with-aes-128-by-using-net"></a>.NET kullanarak, AES-128 ile içeriğinizi koruyun

### <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio projesi oluşturup yapılandırma

1. Geliştirme ortamınızı ayarlayın ve app.config dosyanızı [.NET ile Media Services geliştirme](media-services-dotnet-how-to-use.md) bölümünde açıklandığı gibi bağlantı bilgileriyle doldurun.

2. Aşağıdaki öğeleri, App. config dosyanızda tanımlandığı şekilde appSettings 'e ekleyin:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

### <a id="example"></a>Örnek

Bu bölümde gösterilen kodu Program.cs dosyanızdaki kodun üzerine yazın.
 
>[!NOTE]
>Farklı Media Services ilkeleri (örneğin, bulucu ilkesi veya ContentKeyAuthorizationPolicy) için 1.000.000 ilke sınırlaması vardır. Aynı gün/erişim izinlerini her zaman kullanıyorsanız aynı ilke KIMLIĞINI kullanın. Örnek olarak uzun süre olduğu gibi kalması amaçlanan bulucu ilkeleri (karşıya yükleme olmayan ilkeler) verilebilir. Daha fazla bilgi için, [Media Services .NET SDK ile varlıkları ve ilgili varlıkları yönetme](media-services-dotnet-manage-entities.md#limit-access-policies)konusundaki "erişim ilkelerini sınırlandırma" bölümüne bakın.

Değişkenleri, giriş dosyalarınızın bulunduğu klasörlere işaret edecek şekilde güncelleştirdiğinizden emin olun.

[!code-csharp[Main](../../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
