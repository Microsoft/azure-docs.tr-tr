---
title: DRM şifrelemesini ve lisans teslimini Media Services
description: Microsoft PlayReady, Google Widevine veya Apple FairPlay lisanslarıyla şifrelenmiş akışlar sunmak için DRM dinamik şifrelemesini ve lisans teslim hizmetini nasıl kullanacağınızı öğrenin.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: dab265c41bc1c951a31ff764c9214e7f21ee3df4
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279570"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>Öğretici: DRM dinamik şifrelemesini ve lisans teslim hizmetini kullanın

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Bu öğretici [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent) örnekleri kullanıyor olsa da, genel adımlar [REST API](/rest/api/media/liveevents), [CLI](/cli/azure/ams/live-event)veya desteklenen diğer [SDK](media-services-apis-overview.md#sdks)'lar için aynıdır.

Azure Media Services'ı kullanarak Microsoft PlayReady, Google Widevine veya Apple FairPlay lisansları ile şifrelenmiş akışlarınızı sunabilirsiniz. Ayrıntılı açıklama için bkz. [dinamik şifreleme Ile içerik koruma](drm-content-protection-concept.md).

Media Services, PlayReady, Widevine ve FairPlay DRM lisanslarını sunmaya yönelik bir hizmet de sunar. Bir Kullanıcı DRM korumalı içerik istediğinde, oynatıcı uygulaması Media Services lisans hizmetinden bir lisans ister. Oynatıcı uygulaması yetkilendirilirse, Media Services lisans hizmeti oynatıcı için bir lisans yayınlar. Lisans, istemci oynatıcısının içeriğin şifresini çözmek ve akışını yapmak için kullanabileceği şifre çözme anahtarını içerir.

Bu makalede, [DRM ile şifreleme](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM) örneği kullanılmıştır.

Bu makalede anlatılan örnek aşağıdaki sonucu üretir:

![Azure Media Player 'de DRM korumalı videonun bulunduğu AMS 'ler](./media/drm-protect-with-drm-tutorial/ams_player.png)

Bu öğretici şunların nasıl yapıldığını gösterir:

> [!div class="checklist"]
> * Kodlama dönüştürmesi oluşturun.
> * Belirtecinizin doğrulanması için kullanılan imzalama anahtarını ayarlama.
> * İçerik anahtarı ilkesinde gereksinimleri ayarlayın.
> * Belirtilen akış ilkesiyle bir StreamingLocator oluşturun.
> * Dosyanızı kayıttan yürütmek için kullanılan bir URL oluşturun.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Öğreticiyi tamamlamak için aşağıdakiler gereklidir:

* [İçerik korumaya genel bakış](drm-content-protection-concept.md) makalesini gözden geçirin.
* [Tasarım çoklu DRM içerik koruma sistemini Access Control ile](architecture-design-multi-drm-system.md)inceleyin.
* Visual Studio Code veya Visual Studio 'Yu yükler.
* [Bu hızlı başlangıçta](./account-create-how-to.md) açıklandığı gibi yeni bir Azure Media Services hesabı oluşturun.
* [Erişim API'lerini](./access-api-howto.md) kullanarak Media Services API'lerini kullanmak için gerekli kimlik bilgilerini edinin.
* Uygulama yapılandırma dosyasında (appsettings.jsüzerinde) uygun değerleri ayarlayın.

## <a name="download-code"></a>Kodu indirin

Aşağıdaki komutu kullanarak, bu makalede anlatılan eksiksiz .NET örneğini içeren bir GitHub havuzunu makinenize kopyalayın:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
"Encrypt with DRM" örneği [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM) klasöründe bulunur.

> [!NOTE]
> Örnek, uygulamayı her çalıştırdığınızda benzersiz kaynaklar oluşturur. Genellikle, dönüşümler ve ilkeler gibi mevcut kaynakları yeniden kullanacaksınız (mevcut kaynak gerekli yapılandırmalara sahipseniz).

## <a name="start-using-media-services-apis-with-net-sdk"></a>.NET SDK ile Media Services API’sini kullanmaya başlama

.NET ile Media Services API 'Leri kullanmaya başlamak için bir **Azudüzeltici Aservicesclient** nesnesi oluşturun. Nesneyi oluşturmak için, Azure AD kullanarak Azure’a bağlanmak üzere istemcinin ihtiyaç duyduğu kimlik bilgilerini sağlamanız gerekir. Makalenin başlangıcında kopyaladığınız koddaki **GetCredentialsAsync** işlevi, yerel yapılandırma dosyasında sağlanan kimlik bilgilerini temel alan ServiceClientCredentials nesnesini oluşturur.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Çıktı varlığı oluşturma  

Çıktı [Varlığı](assets-concept.md), kodlama işinizin sonucunu depolar.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Kodlama Dönüşümü alma veya oluşturma

Yeni bir [Dönüşüm](transform-jobs-concept.md) örneği oluştururken çıktı olarak neyi üretmesi istediğinizi belirtmeniz gerekir. Gerekli parametre `transformOutput` , aşağıdaki kodda gösterildiği gibi bir nesnedir. Her TransformOutput bir **Ön ayar** içerir. Ön ayar, video ve/veya ses işleme işlemlerinin istenen TransformOutput nesnesini oluşturmak üzere kullanılacak adım adım yönergelerini açıklar. Bu makalede açıklanan örnek, **AdaptiveStreaming** adlı yerleşik bir Ön Ayar kullanır. Önceden ayarlanmış, giriş videosunu, giriş çözünürlüğünü ve bit hızını temel alarak otomatik olarak oluşturulmuş bir bit hızı (bit hızı çözme çiftleri) olarak kodlar ve her bit hızı çözme çiftine karşılık gelen H. leneçini ve AAC ses ile ISO MP4 dosyaları üretir. 

Yeni bir **Dönüşüm** oluşturmadan önce ilk olarak aşağıdaki kodda gösterildiği gibi **Get** yöntemi ile bir dönüşümün zaten var olup olmadığını denetlemeniz gerekir.  Media Services v3’te varlıklar üzerindeki **Get** yöntemleri, varlığın mevcut olmaması durumunda **null** değerini döndürür (büyük/küçük harfe duyarlı ad denetimi).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>İşi gönderme

Yukarıda bahsedildiği gibi **Transform** nesnesi tarif, [Job](transform-jobs-concept.md) ise bu **Transform** nesnesini belirli bir giriş videosu veya ses içeriğine uygulamak için Media Services’e gönderilen gerçek istektir. **İş** , giriş videonun konumu ve çıktının konumu gibi bilgileri belirtir.

Bu öğreticide, doğrudan bir [https kaynak URL](job-input-from-http-how-to.md)'sinden alınan bir dosyayı temel alarak iş girişini oluşturacağız.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>İşin tamamlanmasını bekleyin

İşin tamamlaması biraz zaman alır. Ne zaman sorulduğunda bildirim almak istersiniz. Aşağıdaki kod örneği, **İş**’in durumu için hizmette nasıl yoklama yapılacağını gösterir. Yoklama, olası gecikme nedeniyle üretim uygulamaları için önerilen en iyi uygulamadır. Yoklama, bir hesap üzerinde gereğinden fazla kullanılırsa kısıtlanabilir. Geliştiricilerin onun yerine Event Grid kullanmalıdır. Bkz. [Olayları özel bir web uç noktasına yönlendirme](monitoring/job-state-events-cli-how-to.md).

**İş** genellik şu aşamalardan geçer: **Zamanlandı**, **Kuyruğa Alındı**, **İşleniyor**, **Tamamlandı** (son aşama). İş bir hatayla karşılaşmışsa, **hata** durumunu alırsınız. İş iptal edilme süreciiyorsa, tamamlandığında iptal edip **Iptal etmiş** olursunuz **.**

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Içerik anahtarı Ilkesi oluşturma

İçerik anahtarı, Varlıklarınıza güvenli bir şekilde erişilmesini sağlar. İçeriğinizi bir DRM ile şifrelerken bir [Içerik anahtarı ilkesi](drm-content-key-policy-concept.md) oluşturmanız gerekir. İlke, içerik anahtarının son istemcilere nasıl teslim edildiğini yapılandırır. İçerik anahtarı bir akış bulucu ile ilişkilendirilir. Media Services ayrıca şifreleme anahtarlarını ve lisansları yetkili kullanıcılara teslim eden anahtar teslim hizmetini de sunar.

Belirtilen yapılandırmayla anahtar teslim etmek için karşılanması gereken **Içerik anahtarı ilkesinde** gereksinimleri (kısıtlamalar) ayarlamanız gerekir. Bu örnekte aşağıdaki yapılandırmaları ve gereksinimleri belirleyeceğiz:

* Yapılandırma

    [PlayReady](drm-playready-license-template-concept.md) ve [Widevine](drm-widevine-license-template-concept.md) lisansları, Media Services lisans teslim hizmetiyle teslim edilebilecek şekilde yapılandırılmıştır. Bu örnek uygulama [Fairplay](drm-fairplay-license-overview.md) lisansını yapılandırmasa da, FairPlay 'yi yapılandırmak için kullanabileceğiniz bir yöntemi içerir. FairPlay yapılandırmasını başka bir seçenek olarak ekleyebilirsiniz.

* Kısıtlama

    Uygulama, ilkede bir JSON Web Token (JWT) belirteç türü kısıtlaması ayarlar.

Oynatıcı bir akış isteğinde bulunduğunda Media Services belirtilen anahtarı kullanarak içeriğinizi dinamik olarak şifreler. Oynatıcı, akışın şifresini çözmek için anahtar teslim hizmetinden anahtarı ister. Hizmet, kullanıcının anahtarı alma yetkisine sahip olup olmadığını belirlemek için anahtar için belirlediğiniz içerik anahtarı ilkesini değerlendirir.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Akış Bulucu oluşturma

Kodlama tamamlandıktan ve içerik anahtarı ilkesi ayarlandıktan sonra sıradaki adım, çıktı Varlığındaki videoyu yürütmek için istemcilerin kullanımına sunmaktır. Videoyu iki adımda kullanılabilir hale getirebilirsiniz:

1. Bir [akış Bulucu](stream-streaming-locators-concept.md)oluşturun.
2. İstemcilerin kullanabileceği akış URL'sini oluşturma.

**Akış bulucuyu** oluşturma işlemine yayımlama denir. Varsayılan olarak, **akış Bulucu** , API çağrılarını yaptıktan hemen sonra geçerlidir. İsteğe bağlı başlangıç ve bitiş zamanlarını yapılandırmadığınız sürece, silinene kadar sürer.

Bir **akış Bulucu** oluştururken, istenen ' i belirtmeniz gerekir `StreamingPolicyName` . Bu öğreticide, önceden tanımlanmış akış Ilkelerinden birini kullanıyoruz, bu da içeriğin akış için nasıl yayımlanacağını söyler Azure Media Services. Bu örnekte StreamingLocator.StreamingPolicyName öğesini "Predefined_MultiDrmCencStreaming" ilkesi olarak belirliyoruz. PlayReady ve Widevine şifrelemeleri uygulanır ve anahtar, yapılandırılmış DRM lisanslarına göre kayıttan yürütme istemcisine gönderilir. Akışınızı CBCS (FairPlay) ile de şifrelemek isterseniz "Predefined_MultiDrmStreaming" öğesini kullanın.

> [!IMPORTANT]
> Özel bir [akış ilkesi](stream-streaming-policy-concept.md)kullanırken, medya hizmeti hesabınız için sınırlı sayıda ilke kümesi tasarlamalı ve aynı şifreleme seçenekleri ve protokoller gerektiğinde bunları streamingbulucular için yeniden kullanmanız gerekir. Media Service hesabınızda StreamingPolicy girişlerinin sayısı için bir kota bulunur. Her StreamingLocator için yeni bir StreamingPolicy oluşturmamalısınız.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Test belirteci alma

Bu öğreticide içerik anahtarı ilkesi için belirteç sınırlaması getirilmektedir. Belirteç kısıtlamalı ilkenin beraberinde bir güvenlik belirteci hizmeti (STS) tarafından verilmiş bir belirteç bulunmalıdır. Media Services, [JWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) biçimlerindeki belirteçleri destekler ve örnekte yapılandırdığımız şeydir.

ContentKeyPolicy içinde kullanılan ContentKeyIdentifierClaim, anahtar teslim hizmetine sunulan belirtecin içinde ContentKey tanımlayıcısına sahip olması gerektiğini belirtir. Örnek olarak, akış bulucuyu oluştururken bir içerik anahtarı belirtmedik, sistem bizim için rastgele bir tane oluşturur. Test belirtecini oluşturmak için Contentkeyıd öğesini Contentkeyıdentifierclaim talebine koymak üzere almalısınız.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>Akış URL 'SI oluşturma

[Streaminglocator](/rest/api/media/streaminglocators) oluşturuldığına göre, akış URL 'lerini alabilirsiniz. URL oluşturmak için, [Streamingendpoint](/rest/api/media/streamingendpoints) ana bilgisayar adını ve **akış Bulucu** yolunu birleştirmeniz gerekir. Bu örnekte, *varsayılan* **akış uç noktası** kullanılır. İlk olarak bir medya hizmeti hesabı oluşturduğunuzda, bu *varsayılan* **akış uç noktası** durdurulmuş durumda olacaktır, bu yüzden **Start**'ı çağırmanız gerekir.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

Uygulamayı çalıştırdığınızda aşağıdaki ekranı görürsünüz:

![DRM ile koruma](./media/drm-protect-with-drm-tutorial/playready_encrypted_url.png)

Bir tarayıcı açın ve oluşturulan URL'yi yapıştırarak URL'nin ve belirtecin sizin için doldurulmuş olduğu Azure Media Player tanıtım sayfasını açabilirsiniz.

## <a name="clean-up-resources-in-your-media-services-account"></a>Media Services hesabınızdaki kaynakları temizleme

Genellikle, kullanmayı planladığınız nesneler hariç her şeyi temizlemeniz gerekir (genellikle dönüşümler, Streamingkonumlandırıcı vb.). Deneme sonrasında hesabınızın temiz olmasını istiyorsanız, kullanmayı planlamadığınız kaynakları silin. Örneğin, aşağıdaki kod işi, oluşturulan varlıkları ve içerik anahtarı ilkesini siler:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticide oluşturduğunuz Media Services ve depolama hesapları dahil olmak üzere, kaynak grubunuzdaki kaynaklardan herhangi birine artık ihtiyacınız yoksa kaynak grubunu silebilirsiniz.

Aşağıdaki CLı komutunu yürütün:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="additional-notes"></a>Ek notlar

* Widevine, Google Inc. tarafından sunulan bir hizmettir ve Google, Inc 'nin hizmet koşullarına ve gizlilik Ilkesine tabidir.

## <a name="ask-questions-give-feedback-get-updates"></a>Soru sorun, geri bildirimde bulunun, güncelleştirmeleri al

Soru sormak, geri bildirimde bulunmak ve Media Services hakkında güncelleştirmeler almak için [Azure Media Services Community](media-services-community.md) makalesine göz atın.

## <a name="next-steps"></a>Sonraki adımlar

Kullanıma al

> [!div class="nextstepaction"]
> [AES ile koruma-128](drm-playready-license-template-concept.md)
