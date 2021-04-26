---
Başlık: bir videoyu AES-128 ile şifreleyin: Azure Media Services açıklaması: AES 128 bit şifreleme ile videoyu şifrelemeyi ve Azure Media Services 'de anahtar teslimi hizmeti 'ni kullanmayı öğrenin.
Hizmetler: Media-Services belgetationcenter: ' ' Yazar: ınridatmicrosoft Manager: femila Düzenleyicisi: ' '

MS. Service: Media-Services MS. Workload: Media ms.tgt_pltfrm: na MS. devlang: na MS. Topic: öğretici ms. Date: 03/17/2021 MS. Author: inhenkel

---
# <a name="tutorial-encrypt-video-with-aes-128-and-use-the-key-delivery-service"></a>Öğretici: AES-128 ile videoyu şifreleyin ve anahtar teslim hizmetini kullanın

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Öğretici [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent) örnekleri kullanıyor olsa da, genel adımlar [REST API](/rest/api/media/liveevents), [CLI](/cli/azure/ams/live-event)veya desteklenen diğer [SDK](media-services-apis-overview.md#sdks)'lar için aynıdır.

Media Services, HTTP Canlı Akışı (HLS), MPEG-DASH ve Kesintisiz Akış 128 bit şifreleme anahtarlarını kullanarak AES ile şifreli olarak dağıtmak için kullanabilirsiniz. Media Services, yetkili kullanıcılara şifreleme anahtarları sunan anahtar teslim hizmetini de sağlar. Videonuzu dinamik olarak şifrelemek için Media Services istiyorsanız, şifreleme anahtarını bir akış bulucu ile ilişkilendirir ve ayrıca içerik anahtarı ilkesini de yapılandırırsınız. Player tarafından bir akış istendiğinde, Media Services, içeriğinizi AES-128 ile dinamik olarak şifrelemek için belirtilen anahtarı kullanır. Oynatıcı, akışın şifresini çözmek için anahtar teslim hizmetinden anahtarı ister. Hizmet, kullanıcının anahtarı alma yetkisine sahip olup olmadığını belirlemek için anahtar için belirlediğiniz içerik anahtarı ilkesini değerlendirir.

Her bir varlığı birden fazla şifreleme türü (AES-128, PlayReady, Widevine, FairPlay) ile şifreleyebilirsiniz. Birlikte kullanılabilecek türler hakkında bilgi almak için bkz. [Akış protokolleri ve şifreleme türleri](drm-content-protection-concept.md#streaming-protocols-and-encryption-types). Ayrıca bkz. [DRM ile koruma](drm-protect-with-drm-tutorial.md).

Bu makaleye örnek çıktısı, Azure Media Player, bildirim URL 'si ve içeriği kayıttan yürütmek için gereken AES belirtecinin URL 'sini içerir. Örnek, JSON Web Token (JWT) belirtecinin kullanım süresini 1 saat olarak ayarlar. Bir tarayıcı açabilir ve aşağıdaki biçimde doldurulmuş URL ve belirteç ile Azure Media Player Demo sayfasını başlatmak için elde edilen URL 'yi yapıştırabilirsiniz: ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}``` .

Bu öğretici şunların nasıl yapıldığını gösterir:

> [!div class="checklist"]
> * Makalesinde açıklanan [Encryptwithaes](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) örneğini indirin.
> * .NET SDK ile Media Services API 'Lerini kullanmaya başlayın.
> * Çıkış varlığı oluşturun.
> * Kodlama dönüştürmesi oluşturun.
> * Bir Iş gönderebilirsiniz.
> * Işin tamamlanmasını bekleyin.
> * Içerik anahtarı Ilkesi oluşturma
> * İlkeyi JWT belirteci kısıtlamasını kullanacak şekilde yapılandırın.
> * Bir akış Bulucu oluşturun.
> * Video 'yu AES (ClearKey) ile şifrelemek için akış bulucuyu yapılandırın.
> * Bir test belirteci alın.
> * Akış URL 'SI oluşturun.
> * Kaynakları temizleyin.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Öğreticiyi tamamlamak için aşağıdakiler gereklidir.

* [İçerik korumaya genel bakış](drm-content-protection-concept.md) makalesini gözden geçirin.
* Visual Studio Code veya Visual Studio 'Yu yükler.
* [Media Services hesabı oluşturun](./account-create-how-to.md).
* API ['leri](./access-api-howto.md)kullanarak Media Services API 'leri kullanmak için gereken kimlik bilgilerini alın.

## <a name="download-code"></a>Kodu indirin

Aşağıdaki komutu kullanarak, bu makalede anlatılan eksiksiz .NET örneğini içeren bir GitHub havuzunu makinenize kopyalayın:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

"AES ile şifreleme-128" örneği, [Encryptwithaes](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) klasöründe bulunur.

> [!NOTE]
> Örnek, uygulamayı her çalıştırdığınızda benzersiz kaynaklar oluşturur. Genellikle, dönüşümler ve ilkeler gibi mevcut kaynakları yeniden kullanacaksınız (mevcut kaynak gerekli yapılandırmalara sahipseniz).

## <a name="start-using-media-services-apis-with-net-sdk"></a>.NET SDK ile Media Services API’sini kullanmaya başlama

.NET ile Media Services API 'Leri kullanmaya başlamak için bir **Azudüzeltici Aservicesclient** nesnesi oluşturun. Nesneyi oluşturmak için, Azure AD kullanarak Azure’a bağlanmak üzere istemcinin ihtiyaç duyduğu kimlik bilgilerini sağlamanız gerekir. Makalenin başlangıcında kopyaladığınız koddaki **GetCredentialsAsync** işlevi, yerel yapılandırma dosyasında sağlanan kimlik bilgilerini temel alan ServiceClientCredentials nesnesini oluşturur.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Çıktı varlığı oluşturma  

Çıktı [Varlığı](/rest/api/media/assets), kodlama işinizin sonucunu depolar.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Kodlama Dönüşümü alma veya oluşturma

Yeni bir [Dönüşüm](/rest/api/media/transforms) örneği oluştururken çıktı olarak neyi üretmesi istediğinizi belirtmeniz gerekir. Gerekli parametre, aşağıdaki kodda gösterildiği gibi bir **TransformOutput** nesnesidir. Her **TransformOutput** bir **Ön ayar** içerir. **Ön ayar**, video ve/veya ses işleme işlemlerinin istenen **TransformOutput** nesnesini oluşturmak üzere kullanılacak adım adım yönergelerini açıklar. Bu makalede açıklanan örnek, **AdaptiveStreaming** adlı yerleşik bir Ön Ayar kullanır. Önceden ayarlanmış, giriş videosunu, giriş çözünürlüğünü ve bit hızını temel alarak otomatik olarak oluşturulan bir bit hızı (bit hızı çözünürlük çiftleri) olarak kodlar ve ardından her bit hızı çözme çiftine karşılık gelen H. leneçini ve AAC ses ile ISO MP4 dosyaları üretir.

Yeni bir [dönüşüm](/rest/api/media/transforms)oluşturmadan önce, aşağıdaki kodda gösterildiği gibi **Get** metodunu kullanarak zaten bir tane var olup olmadığını denetleyin. Media Services v3’te varlıklar üzerindeki **Get** yöntemleri, varlığın mevcut olmaması durumunda **null** değerini döndürür (büyük/küçük harfe duyarlı ad denetimi).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>İşi gönderme

Yukarıda bahsedildiği gibi [Transform](/rest/api/media/transforms) nesnesi tarif, [Job](/rest/api/media/jobs) ise bu **Transform** nesnesini belirli bir giriş videosu veya ses içeriğine uygulamak için Media Services’e gönderilen gerçek istektir. **İş** , giriş videonun konumu ve çıktının konumu gibi bilgileri belirtir.

Bu öğreticide, doğrudan bir [https kaynak URL](job-input-from-http-how-to.md)'sinden alınan bir dosyayı temel alarak iş girişini oluşturacağız.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>İşin tamamlanmasını bekleyin

İşin tamamlaması biraz zaman alır. Ne zaman sorulduğunda bildirim almak istersiniz. Aşağıdaki kod örneği, [İş](/rest/api/media/jobs)’in durumu için hizmette nasıl yoklama yapılacağını gösterir. Yoklama, olası gecikme nedeniyle üretim uygulamaları için önerilen en iyi uygulamadır. Yoklama, bir hesap üzerinde gereğinden fazla kullanılırsa kısıtlanabilir. Geliştiricilerin onun yerine Event Grid kullanmalıdır. Daha fazla bilgi için bkz. [olayları özel bir Web uç noktasına yönlendirme](monitoring/job-state-events-cli-how-to.md).

**İş** genellik şu aşamalardan geçer: **Zamanlandı**, **Kuyruğa Alındı**, **İşleniyor**, **Tamamlandı** (son aşama). İş bir hatayla karşılaşmışsa, **hata** durumunu alırsınız. İş iptal edilme süreciiyorsa, tamamlandığında iptal edip **Iptal etmiş** olursunuz **.**

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Içerik anahtarı Ilkesi oluşturma

İçerik anahtarı, Varlıklarınıza güvenli bir şekilde erişilmesini sağlar. İçerik anahtarının son istemcilere nasıl teslim edildiğini yapılandıran bir **Içerik anahtarı ilkesi** oluşturmanız gerekir. İçerik anahtarı, **akış bulucunun** ilişkilendirildiği. Media Services, yetkili kullanıcılara şifreleme anahtarları sunan anahtar teslim hizmetini de sağlar.

Player tarafından bir akış istendiğinde, Media Services içeriğinizi dinamik olarak şifrelemek için belirtilen anahtarı kullanır (Bu durumda, AES şifrelemesini kullanarak). Akışın şifresini çözmek için, Player anahtarı anahtar teslim hizmetinden ister. Hizmet, kullanıcının anahtarı alma yetkisine sahip olup olmadığını belirlemek için anahtar için belirlediğiniz içerik anahtarı ilkesini değerlendirir.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Akış Bulucu oluşturma

Kodlama tamamlandıktan ve içerik anahtarı ilkesi ayarlandıktan sonra sıradaki adım, çıktı Varlığındaki videoyu yürütmek için istemcilerin kullanımına sunmaktır. Videoyu iki adımda kullanılabilir hale getirebilirsiniz:

1. Bir [akış Bulucu](/rest/api/media/streaminglocators)oluşturun.
2. İstemcilerin kullanabileceği akış URL'sini oluşturma.

**Akış bulucuyu** oluşturma işlemine yayımlama denir. Varsayılan olarak, **akış Bulucu** , API çağrılarını yaptıktan hemen sonra geçerlidir. İsteğe bağlı başlangıç ve bitiş zamanlarını yapılandırmadığınız sürece, silinene kadar sürer.

Bir [akış Bulucu](/rest/api/media/streaminglocators)oluştururken, Istenen **Streamingpolicyname**' i belirtmeniz gerekir. Bu öğreticide, PredefinedStreamingPolicies 'den birini kullanıyoruz, bu da içeriğin akış için nasıl yayımlanacağını söyler Azure Media Services. Bu örnekte, AES zarf şifrelemesi uygulanır (anahtar, bir DRM lisansı değil HTTPS aracılığıyla kayıttan yürütme istemcisine teslim edildiğinden, bu şifreleme ClearKey şifrelemesi olarak da bilinir).

> [!IMPORTANT]
> Özel bir [Streamingpolicy](/rest/api/media/streamingpolicies)kullanırken, medya hizmeti hesabınız için sınırlı sayıda ilke kümesi tasarlamalı ve aynı şifreleme seçenekleri ve protokoller gerektiğinde bunları akış bulucular için yeniden kullanmanız gerekir. Media Service hesabınızda StreamingPolicy girişlerinin sayısı için bir kota bulunur. Her akış bulucu için yeni bir StreamingPolicy oluşturmamalısınız.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Test belirteci alma

Bu öğreticide içerik anahtarı ilkesi için belirteç sınırlaması getirilmektedir. Belirteç kısıtlamalı ilkenin beraberinde bir güvenlik belirteci hizmeti (STS) tarafından verilmiş bir belirteç bulunmalıdır. Media Services, [JWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) biçimindeki belirteçleri destekler ve örnekte yapılandırdığımız şeydir.

Contentkeyıdentifierclaim, **Içerik anahtarı ilkesinde** kullanılır. Bu, anahtar teslim hizmetine sunulan belirtecin, içindeki içerik anahtarının tanımlayıcısına sahip olması gerektiği anlamına gelir. Örnekte, akış bulucuyu oluştururken bir içerik anahtarı belirtmedik, sistem bizim için rastgele bir tane oluşturdu. Test belirtecini oluşturmak için Contentkeyıd öğesini Contentkeyıdentifierclaim talebine koymak üzere almalısınız.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>DASH akış URL'si oluşturma

[Akış bulucunun](/rest/api/media/streaminglocators) oluşturuldığına göre, akış URL 'lerini alabilirsiniz. URL oluşturmak için, [Streamingendpoint](/rest/api/media/streamingendpoints) ana bilgisayar adını ve **akış Bulucu** yolunu birleştirmeniz gerekir. Bu örnekte, *varsayılan* **akış uç noktası** kullanılır. İlk olarak bir medya hizmeti hesabı oluşturduğunuzda, bu *varsayılan* **akış uç noktası** durdurulmuş durumda olacaktır, bu yüzden **Start**'ı çağırmanız gerekir.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Media Services hesabınızdaki kaynakları temizleme

Genellikle, yeniden kullanmayı planladığınız nesneler hariç her şeyi temizlemeniz gerekir (genellikle dönüşümler, akış Bulıcıları ve benzerlerini yeniden kullanacaksınız). Deneme sonrasında hesabınızın temiz olmasını istiyorsanız, kullanmayı planlamadığınız kaynakları silin. Örneğin, aşağıdaki kod işi, oluşturulan varlıkları ve içerik anahtarı ilkesini siler:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticide oluşturduğunuz Media Services ve depolama hesapları dahil olmak üzere, kaynak grubunuzdaki kaynaklardan herhangi birine artık ihtiyacınız yoksa kaynak grubunu silebilirsiniz.

Aşağıdaki CLı komutunu yürütün:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Soru sorun, geri bildirimde bulunun, güncelleştirmeleri al

Soru sormak, geri bildirimde bulunmak ve Media Services hakkında güncelleştirmeler almak için [Azure Media Services Community](media-services-community.md) makalesine göz atın.

## <a name="additional-notes"></a>Ek notlar

* Widevine, Google Inc. tarafından sunulan bir hizmettir ve Google, Inc 'nin hizmet koşullarına ve gizlilik Ilkesine tabidir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [DRM ile koruma](drm-protect-with-drm-tutorial.md)
