---
title: Media Services v3 Node.js ile canlı akış
titleSuffix: Azure Media Services
description: Node.js kullanarak canlı akış yapmayı öğrenin.
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
ms.custom: mvc, devx-track-nodejs
ms.date: 04/15/2021
ms.author: inhenkel
ms.openlocfilehash: 749d2fc845f036a2802c80c161b3fc8c171c2555
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730549"
---
# <a name="tutorial-stream-live-with-media-services-using-nodejs-and-typescript"></a>Öğretici: Node.js ve TypeScript kullanarak Media Services ile canlı akış

> [!NOTE]
> Öğretici Node.js örnekleri kullanıyor olsa da, genel adımlar [REST API](/rest/api/media/liveevents), [CLI](/cli/azure/ams/live-event)veya desteklenen diğer [SDK](media-services-apis-overview.md#sdks)'lar için aynıdır. 

Azure Media Services, canlı [Olaylar](/rest/api/media/liveevents) canlı akış içeriğini işlemekten sorumludur. Canlı bir olay, daha sonra canlı bir kodlayıcı için sağladığınız bir giriş uç noktası (alma URL 'SI) sağlar. Canlı olay, canlı kodlayıcıdan canlı giriş akışları alır ve bir veya daha fazla [akış uç noktası](/rest/api/media/streamingendpoints)aracılığıyla akış için kullanılabilir hale getirir. Canlı olaylar ayrıca, daha fazla işlem ve teslim yapmadan önce akışınızı önizlemek ve doğrulamak için kullandığınız bir önizleme uç noktası (önizleme URL 'SI) sağlar. Bu öğreticide, canlı bir olayın **doğrudan geçiş** türünü oluşturmak ve [OBS Studio](https://obsproject.com/download)kullanarak BT 'ye canlı akış yayınlamak için Node.js nasıl kullanılacağı gösterilmektedir.

Öğretici şunların nasıl yapıldığını göstermektedir:

> [!div class="checklist"]
> * Konusunda açıklanan örnek kodu indirin.
> * Yapılandırma ve canlı akış gerçekleştiren kodu inceleyin.
> * [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) ile olayı izleyin [https://ampdemo.azureedge.net](https://ampdemo.azureedge.net) .
> * Kaynakları temizleyin.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Öğreticiyi tamamlamak için aşağıdakiler gereklidir:

- [Node.js](https://nodejs.org/en/download/) yüklensin
- [TypeScript](https://www.typescriptlang.org/) 'i yükler
- [Media Services hesabı oluşturun](./create-account-howto.md).<br/>Kaynak grubu adı ve Media Services hesap adı için kullandığınız değerleri anımsadığınızdan emin olun.
- [Azure CLI Ile Access Azure Media Services API 'sindeki](./access-api-howto.md) adımları izleyin ve kimlik bilgilerini kaydedin. API 'ye erişmek ve ortam değişkenleri dosyanızı yapılandırmak için bunları kullanmanız gerekir.
- Node.js istemci SDK 'sını nasıl kullanacağınızı anlamak için [Node.jsIle yapılandırma ve bağlanma ](./configure-connect-nodejs-howto.md) hakkında bilgi edinin
- Visual Studio Code veya Visual Studio 'Yu yükler.
- [Visual Studio Code ortamınızı](https://code.visualstudio.com/Docs/languages/typescript) TypeScript dilini destekleyecek şekilde ayarlayın.

## <a name="additional-settings-for-live-streaming-software"></a>Canlı akış yazılımı için ek ayarlar

- Bir olayı yayımlamak için kullanılan bir kamera veya bir cihaz (dizüstü bilgisayar gibi).
- Kamera akışınızı kodlayan ve RTMP protokolünü kullanarak Media Services canlı akış hizmetine Gönderen şirket içi yazılım Kodlayıcısı, bkz. [Önerilen şirket içi canlı kodlayıcılar](encode-recommended-on-premises-live-encoders.md). Akışın **RTMP** veya **Kesintisiz Akış** biçiminde olması gerekir.  
- Bu örnek için, ücretsiz [Açık yayın yazılımı OBS Studio](https://obsproject.com/download) gibi bir yazılım kodlayıcıyla başlamak önerilir, bu da başlamak için basit hale gelir.

Bu örnek, vınon uç noktasına RTMP yayınlamak için OBS Studio 'Yu kullanacağınızı varsayar. Önce OBS Studio 'Yu yükler.
OBS Studio 'da aşağıdaki kodlama ayarlarını kullanın:

- Kodlayıcı: NVıDıA NVENC (varsa) veya x264
- Hız denetimi: CBR
- Bit hızı: 2500 kbps (veya dizüstü bilgisayarınız için makul bir şey)
- Ana kare aralığı: 2 sn veya düşük gecikme için 1 s  
- Önceden ayarlanmış: x264 kullanarak düşük gecikmeli kalite veya performans (NVENC) veya "veryfast"
- Profil: yüksek
- GPU: 0 (otomatik)
- Maksimum B-çerçeveler: 2

> [!TIP]
> Devam etmeden önce [Media Services v3 ile canlı akış](stream-live-streaming-concept.md) konusunu gözden geçirmeyi unutmayın.

## <a name="download-and-configure-the-sample"></a>Örneği indirin ve yapılandırın

Aşağıdaki komutu kullanarak, canlı akış Node.js örneğini içeren aşağıdaki git hub deposunu makinenize kopyalayın:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

Canlı akış örneği [Live](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live) klasöründe bulunabilir.

[AMSv3Samples](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples) klasöründe, "Sample. env" adlı dosyayı [, Azure clı Ile erişim Azure Media Services API](./access-api-howto.md)' de topladığınız ortam değişkeni ayarlarınızı depolamak için ". env" adlı yeni bir dosyaya kopyalayın.
Bunun, kod örneğiyle doğru çalışması için dosyanın. env önünde "nokta" (.) içerdiğinden emin olun.

[. Env dosyası](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/sample.env) , MEDIA SERVICES hesabınıza SDK erişiminin kimliğini doğrulamak için gereken hesap adı ve abonelik BILGILERIYLE birlikte AAD uygulama anahtarınızı ve gizli anahtarını içerir. . Gitignore dosyası, bu dosyanın otomatik olarak yayınlanmasını engellemek için zaten yapılandırılmış. Hesabınız için önemli gizli dizileri oldukları için bu kimlik bilgilerinin sızmasına izin vermeyin.

> [!IMPORTANT]
> Bu örnek her kaynak için benzersiz bir sonek kullanır. Hata ayıklamayı iptal ederseniz veya uygulamayı üzerinden çalıştırmadan sonlandırdıysanız, hesabınızda birden çok canlı olayla karşılaşırsınız. <br/>Çalışan canlı olayları durdurduğunuzdan emin olun. Aksi takdirde **faturalandırılırsınız**! Kaynakları otomatik olarak temizlemek için programı tamamen tamamlamak üzere bir şekilde çalıştırın. Program kilitlenirse veya hata ayıklayıcıyı yanlışlıkla durdurup programın yürütülmesini sonlandırdıysanız, çalışan veya tek başına durumlardaki canlı olayları, istenmeyen faturalandırma ücretlerine neden olacak şekilde ayrılmadığından emin olmak için portalı çift denetlemeniz gerekir.

## <a name="examine-the-typescript-code-for-live-streaming"></a>Canlı akış için TypeScript kodunu inceleyin

Bu bölüm, *canlı* projenin [index. TS](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/Live/index.ts) dosyasında tanımlanan işlevleri inceler.

Örnek, her kaynak için benzersiz bir sonek oluşturur, böylece örnek birden çok kez temizlemeden çalıştırırsanız ad çarpışmaları olmaz.

### <a name="start-using-media-services-sdk-for-nodejs-with-typescript"></a>TypeScript ile Node.js için Media Services SDK 'sını kullanmaya başlayın

Node.js ile Media Services API 'Leri kullanmaya başlamak için önce [@azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices) NPM paket yöneticisini kullanarak SDK modülünü eklemeniz gerekir

```bash
npm install @azure/arm-mediaservices
```

Üzerinde package.js, bu, sizin için zaten yapılandırılmıştır, bu nedenle modülleri ve bağımlılıkları yüklemek için *NPM install* öğesini çalıştırmanız yeterlidir.

1. Bir **komut istemi** açın, örnek dizinine gidin.
1. Dizini AMSv3Samples klasörü olarak değiştirin.

    ```bash
    cd AMSv3Samples
    ```

1. *packages.jsdosya üzerinde* kullanılan paketleri yükler.

    ```bash
    npm install 
    ```

1. *AMSv3Samples* klasöründen Visual Studio Code başlatın. (Bu, *. vscode* klasörünün ve dosyaların *tsconfig.js* bulunduğu klasörden başlatmak için gereklidir.)

    ```bash
    cd ..
    code .
    ```

Klasörü *canlı* olarak açın ve Visual Studio Code düzenleyicisinde *index. TS* dosyasını açın.
*İndex. TS* dosyasında, hata ayıklayıcıyı başlatmak için F5 tuşuna basın.

### <a name="create-the-media-services-client"></a>Media Services istemcisini oluşturma

Aşağıdaki kod parçacığı Node.js içinde Media Services istemcisinin nasıl oluşturulacağını gösterir.
Bu kodda, Azure Kaynak Yönetimi uç noktasındaki uzun süreli bir işlemin durumunu yoklamak için gereken süreyi azaltmak üzere Azudüzeltici Aservicesoptions 'ın **longRunningOperationRetryTimeout** özelliğini 2 saniyeye ayarlamamız gerektiğini unutmayın.  Canlı olaylardaki işlemlerin büyük bir kısmı zaman uyumsuz olacak ve tamamlanması biraz zaman alacağından, tüm zaman uyumsuz çağrılar olan canlı olaylar oluşturma, başlatma ve durdurma gibi önemli işlemleri tamamlamaya yönelik süreyi hızlandırmak için SDK 'daki bu yoklama aralığını azaltmanız gerekir. Çoğu kullanım örneği senaryosu için önerilen değer iki saniyedir.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Canlı etkinlik oluşturma

Bu bölümde, bir **geçişli** canlı etkinlik türünün nasıl oluşturulduğu gösterilir (LiveEventEncodingType None olarak ayarlanır). Mevcut canlı etkinlik türleri hakkında daha fazla bilgi için bkz. [canlı olay türleri](live-event-outputs-concept.md#live-event-types). Doğrudan geçiş 'nin yanı sıra, 720P veya 1080P Uyarlamalı bit hızı bulut kodlaması için canlı bir dönüştürme canlı olayı kullanabilirsiniz.
 
Canlı olay oluştururken belirtmek isteyebileceğiniz bazı şeyler şunlardır:

* Canlı etkinlik için alma Protokolü (Şu anda, RTMP (S) ve Kesintisiz Akış protokolleri desteklenir).<br/>Canlı olay veya ilişkili canlı çıktıları çalışırken protokol seçeneğini değiştiremezsiniz. Farklı protokollere ihtiyacınız varsa, her akış protokolü için ayrı canlı etkinlik oluşturun.  
* Alma ve önizleme için IP kısıtlamaları. Bu canlı olaya bir video almasına izin verilen IP adreslerini tanımlayabilirsiniz. İzin verilen IP adresleri tek bir IP adresi (örneğin '10.0.0.1'), bir IP adresi ve CIDR alt ağ maskesi kullanan bir IP aralığı (örneğin '10.0.0.1/22') veya bir IP adresi ve bir noktalı ondalık alt ağ maskesi kullanan bir IP aralığı (örneğin '10.0.0.1(255.255.252.0)') olabilir.<br/>Hiçbir IP adresi belirtilmemişse ve kural tanımı yoksa, hiçbir IP adresine izin verilmez. Tüm IP adreslerine izin vermek için, bir kural oluşturun ve 0.0.0.0/0 olarak ayarlayın.<br/>IP adresleri aşağıdaki biçimlerden birinde olmalıdır: dört sayı veya CıDR adres aralığı olan IPv4 adresi.
* Olayı oluştururken, başlatmayı belirtebilirsiniz. <br/>Autostart değeri true olarak ayarlandığında, canlı olay oluşturulduktan sonra başlatılır. Bu, canlı olay çalışmaya başladığı anda faturalandırma başladığı anlamına gelir. Daha fazla faturalandırmayı durdurmak için canlı olay kaynağında durdurmayı açıkça çağırmanız gerekir. Daha fazla bilgi için bkz. [canlı olay durumları ve faturalandırma](live-event-states-billing-concept.md).
Canlı olayı, daha düşük maliyetli bir ' çalışma ' durumuna taşımayı daha hızlı hale getiren daha düşük maliyetli ' bir ' durumda başlatmak için kullanılabilir bekleme modları de vardır. Bu, kanalları hızla havuza alma ihtiyacı olan etkin havuzlar gibi durumlarda faydalıdır.
* Bir alma URL 'sinin tahmine dayalı olması ve donanım tabanlı bir canlı kodlayıcıda daha kolay korunması için, "useStaticHostname" özelliğini true olarak ayarlayın ve "accessToken" içinde özel benzersiz bir GUID kullanın. Ayrıntılı bilgi için bkz. [canlı olay alma URL 'leri](live-event-outputs-concept.md#live-event-ingest-urls).

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveEvent)]

### <a name="create-an-asset-to-record-and-archive-the-live-event"></a>Canlı olayı kaydetmek ve arşivlemek için varlık oluşturma

Bu kod bloğunda, canlı olay arşivinizi kaydetmek için "bant" olarak kullanmak üzere boş bir varlık oluşturacaksınız.
Bu kavramları öğrenirken, "varlık" nesnesini, bir video bant kaydedicisine eski günlerde ekleyebileceğiniz bant olarak düşünmek en iyisidir. "Canlı çıktı", bant kaydedici makinedir. "Canlı etkinlik" yalnızca makinenin arkasına gelen video sinyalinden oluşur.

Her seferinde varlık veya "bant" oluşturulabilir. Bu benzerleme vurguladı içindeki bant Kaydedicisi olan canlı çıktı nesnesine yönelik olan yalnızca boş bir "varlık" olur.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateAsset)]

### <a name="create-the-live-output"></a>Canlı çıktıyı oluşturma

Bu bölümde, canlı olayın nereye kaydedileceği hakkında bilgi için varlık adını girdi olarak kullanan bir canlı çıktı oluşturacağız. Ayrıca, kayıtta kullanılacak zaman kaydırma (DVR) penceresini ayarladık.
Örnek kod, 1 saatlik zaman kaydırma penceresinin nasıl ayarlanacağını gösterir. Bu, istemcilerin olayın Son saatteki her yerde oynamasına olanak tanır.  Ayrıca, canlı etkinliğin yalnızca son 1 saati arşiv içinde kalır. Gerekirse bu süreyi 25 saate kadar genişletebilirsiniz.  Ayrıca, yayımlandığında, URL yollarındaki HLS ve DASH bildirimleri kullanan çıkış bildirimi adlandırmasını denetleyebileceğinizi unutmayın.

Canlı çıktı veya benzerleme vurguladı 'izdeki "bant Kaydedicisi" de herhangi bir zamanda oluşturulabilir. Yani, sinyal akışını başlatmadan önce veya daha sonra canlı bir çıkış oluşturabilirsiniz. Şeyleri hızlandırmaya ihtiyacınız varsa, sinyal akışına başlamadan önce bunu oluşturmak genellikle yararlı olur.

Canlı çıktılar oluşturma sırasında başlar ve silindiğinde durdurulur.  Canlı çıktıyı sildiğinizde, ilgili varlık veya varlığın içeriğini silmezsiniz. Bandı çıkaracağınızı düşünün. Kaydı olan varlık istediğiniz kadar sürer ve çıkarıldığında (yani, canlı çıktı silindiğinde) isteğe bağlı olarak anında görüntüleme için kullanılabilir olacaktır.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveOutput)]


### <a name="get-ingest-urls"></a>Alma URL’leri alma

Canlı olay oluşturulduktan sonra, gerçek zamanlı kodlayıcıya sağlayacağınız içe alma URL 'Leri edinebilirsiniz. Kodlayıcı, RTMP protokolünü kullanarak canlı akış girmek için bu URL 'Leri kullanır

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetIngestURL)]

### <a name="get-the-preview-url"></a>Önizleme URL'sini alma

Kodlayıcıdan gelen girişin gerçekten alındığını önizlemek ve doğrulamak için PreviewEndpoint kullanın.

> [!IMPORTANT]
> Devam etmeden önce videonun önizleme URL 'sine akmasını sağlayın.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetPreviewURL)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Canlı etkinlikler ve canlı çıktılar oluşturma ve yönetme

Akışın canlı olayına akışını tamamladıktan sonra, istemci oynatıcılarınızın kullanması için bir akış Bulucu yayımlayarak akış olayını başlatabilirsiniz. Bu, akış uç noktası aracılığıyla görüntüleyiciler için kullanılabilir hale getirir.

Önce "canlı etkinlik" i oluşturarak sinyali oluşturursunuz.  Bu canlı olayı başlatıp kodlayıcıyı girişe bağlayaana kadar sinyal akmaz.

"Bant Kaydedicisi" ni durdurmak için, LiveOutput üzerinde Delete 'i çağırın. Bu, gerçekten "varlık" bandınızın **içeriğini** silmez, yalnızca "bant kaydedicisini" siler ve arşivlemeyi durdurur. Varlık, doğrudan varlık üzerinde silme işlemi yapana kadar arşivlenmiş video içeriğiyle her zaman tutulur. LiveOutput 'u sildikten hemen sonra, "varlık" kaydedilen içeriği zaten yayımlanmış olan herhangi bir akış Bulucu URL 'Si aracılığıyla kayıttan oynatıma hazırdır. Bir müşterinin Arşivlenmiş içeriği kayıttan yürütmesine olanak sağlamak istiyorsanız, ilk olarak tüm Konumlandırıcı 'yı varlık içinden kaldırmanız ve ayrıca bir CDN 'YI teslim için kullanıyorsanız, URL yolundaki CDN önbelleğini de temizleyebilmeniz gerekir. Aksi halde içerik, CDN 'deki standart yaşam süresi ayarı için CDN önbelleğinde kalır (72 saat kadar sürebilir.)

#### <a name="create-a-streaming-locator-to-publish-hls-and-dash-manifests"></a>HLS ve DASH bildirimlerini yayımlamak için bir akış Bulucu oluşturma

> [!NOTE]
> Media Services hesabınız oluşturulduğunda hesabınıza **durdurulmuş** durumda bir **varsayılan** akış uç noktası eklenir. İçeriğinizi akışa almak ve [dinamik paketleme](encode-dynamic-packaging-concept.md) ile dinamik şifrelemeden yararlanmak için içerik akışı yapmak istediğiniz akış uç noktasının **çalışıyor** durumda olması gerekir.

Bir akış Bulucuyu kullanarak varlığı yayımladığınızda, canlı olay (DVR pencere uzunluğuna kadar), akış bulucunun süre sonu veya silme işlemi ne kadar önce gelirse görüntülenmeye devam eder. Canlı ve isteğe bağlı olarak görüntülemek için sanal "bant" kaydını görüntüleme izleyicileriniz için kullanılabilir hale getirebilirsiniz. Kayıt tamamlandığında (canlı çıktı silindiğinde) canlı etkinlik, DVR penceresi veya isteğe bağlı varlık izlemek için aynı URL de kullanılabilir.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateStreamingLocator)]

#### <a name="build-the-paths-to-the-hls-and-dash-manifests"></a>HLS ve DASH bildirimlerinin yollarını oluşturma

Örnekteki BuildManifestPaths yöntemi, çeşitli istemcilere ve oynatıcı çerçevelerine yönelik DASH veya HLS tesliminde kullanılacak akış yollarının nasıl belirleyici olarak oluşturulduğunu gösterir.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#BuildManifestPaths)]

## <a name="watch-the-event"></a>Olayı izleme

Olayı izlemek için, akış Bulucu oluşturma bölümünde açıklanan kodu çalıştırdığınızda aldığınız akış URL 'sini kopyalayın. Seçtiğiniz bir medya oynatıcı kullanabilirsiniz. [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) , akışınızı test etmek için kullanılabilir https://ampdemo.azureedge.net .

Canlı olay durdurulduğunda olayları otomatik olarak isteğe bağlı içeriğe dönüştürür. Olayı durdurup sildikten sonra bile, kullanıcılar, varlığı silmemiş olduğu sürece arşivlenmiş içeriğinizi isteğe bağlı bir video olarak akışa alabilir. Bir varlık, bir olay tarafından kullanılıyorsa silinemez; önce olayın silinmesi gerekir.

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Media Services hesabınızdaki kaynakları temizleme

Uygulamayı tüm yöntemle çalıştırırsanız, "cleanUpResources" adlı işlevde kullanılan tüm kaynakları otomatik olarak temizler. Uygulamanın veya hata ayıklayıcının tamamlamak için tüm işlemleri çalıştırdığından emin olun veya kaynakları sızdırabilir ve hesabınızda canlı olaylar çalıştırmaya kadar sonlandırın. Tüm kaynakların Media Services hesabınızda temizlendiğinden emin olmak için Azure portal çift işaretleyin.  

Örnek kodda, Ayrıntılar için **Cleanupresources** yöntemine bakın.

> [!IMPORTANT]
> Canlı olaydan tasarruf eden faturalandırma maliyetlerini çalıştırmaya çıkılıyor. Göz önünde bulundurulması, proje/program kilitlenirse veya herhangi bir nedenden dolayı kapatılmışsa, bu durum bir faturalandırma durumunda çalışan canlı olayı bırakabilir.

## <a name="ask-questions-give-feedback-get-updates"></a>Soru sorun, geri bildirimde bulunun, güncelleştirmeleri al

Soru sormak, geri bildirimde bulunmak ve Media Services hakkında güncelleştirmeler almak için [Azure Media Services Community](media-services-community.md) makalesine göz atın.

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Azure 'da Node.js için daha fazla geliştirici belgesi

- [JavaScript & için Azure Node.js geliştiriciler](/azure/developer/javascript/)
- [Git hub deposunda Media Services kaynak kodu @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Node.js geliştiricileri için Azure paket belgeleri](/javascript/api/overview/azure/)


## <a name="next-steps"></a>Sonraki adımlar

[Dosyaları akışla aktarma](stream-files-tutorial-with-api.md)