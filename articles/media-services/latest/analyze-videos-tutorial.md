---
title: Media Services v3 ile videoları analiz etme
description: Azure Media Services kullanarak videoları çözümlemeyi öğrenin.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: 5155ffed26daaf53b991a84971929500057d0007
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275303"
---
# <a name="tutorial-analyze-videos-with-media-services-v3"></a>Öğretici: Media Services v3 ile videoları analiz etme

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bu öğreticide Azure Media Services ile videoları analiz etme işlemi gösterilir. Kayıtlı videolar veya ses içerikleri hakkında derin içgörüler kazanmak isteyebileceğiniz çok sayıda senaryo mevcuttur. Örneğin, daha yüksek müşteri memnuniyeti elde etmek isteyen kuruluşlar, müşteri destek kayıtlarını dizinler ve panolarla aranabilir bir katalog haline getirmek için konuşmayı metne dönüştürme işlemini çalıştırabilir.

Bu öğretici şunların nasıl yapıldığını gösterir:

> [!div class="checklist"]
> * Konusunda açıklanan örnek uygulamayı indirin.
> * Belirtilen videoyu çözümleyen kodu inceleyin.
> * Uygulamayı çalıştırın.
> * Çıkışı inceleyin.
> * Kaynakları temizleyin.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Uyumluluk, gizlilik ve güvenlik
 
Önemli bir anımsatıcı olarak, Video Indexer kullanmanız için geçerli olan tüm yasalara uymanız gerekir. Video Indexer veya başka bir Azure hizmetini, diğer kullanıcıların haklarını ihlal eden bir şekilde kullanmamalıdır. Biyometrik veriler de dahil olmak üzere herhangi bir videoyu, işleme ve depolama için Video Indexer hizmetine yüklemeden önce, videodaki kişilerden gelen tüm uygun haklara sahip olmanız gerekir. Video Indexer Azure bilişsel [Hizmetler koşulları](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)'nda uyumluluk, gizlilik ve güvenlik hakkında bilgi edinmek için. Microsoft 'un gizlilik yükümlülüklerini ve verilerinizi işleme için, Microsoft 'un [Gizlilik bildirimini](https://privacy.microsoft.com/PrivacyStatement), [çevrimiçi hizmet koşulları](https://www.microsoft.com/licensing/product-licensing/products) 'nı (OST) ve [veri Işleme eki](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) 'ni ("DPA") gözden geçirin. Veri saklama, silme/yok etme dahil olmak üzere daha fazla gizlilik bilgisi, OST ve [burada](../video-indexer/faq.md)bulunabilir. Video Indexer kullanarak, bilişsel hizmetler koşulları, OST, DPA ve gizlilik bildirimiyle bağlanmayı kabul etmiş olursunuz.

## <a name="prerequisites"></a>Önkoşullar

- Visual Studio yüklü değilse, [Visual Studio Community 2019](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)' i alın.
- [Media Services hesabı oluşturun](./account-create-how-to.md).<br/>Kaynak grubu adı ve Media Services hesap adı için kullandığınız değerleri anımsadığınızdan emin olun.
- [Azure CLI Ile Access Azure Media Services API 'sindeki](./access-api-howto.md) adımları izleyin ve kimlik bilgilerini kaydedin. API 'ye erişmek için bunları kullanmanız gerekir.

## <a name="download-and-configure-the-sample"></a>Örneği indirin ve yapılandırın

Aşağıdaki komutu kullanarak, .NET örneğini içeren bir GitHub havuzunu makinenize kopyalayın:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Örnek, [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/AnalyzeVideos) klasöründe yer alır.

İndirilen projenizde [appsettings.js](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/appsettings.json) açın. Değerleri, [API 'lere erişirken](./access-api-howto.md)aldığınız kimlik bilgileriyle değiştirin.

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>Belirtilen videoyu analiz eden kodu inceleme

Bu bölümde, *AnalyzeVideos* projesinin [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) dosyasında tanımlı işlevler incelenmektedir.

Örnek aşağıdaki eylemleri tamamlar:

1. Görüntünüzü çözümleyen bir **dönüşüm** ve **iş** oluşturur.
2. Bir giriş **varlığı** oluşturur ve videoyu buna yükler. Varlık, işin girişi olarak kullanılır.
3. İşin çıkışını depolayan bir çıkış varlığı oluşturur.
4. İşi gönderir.
5. İşin durumunu denetler.
6. İşin çalıştırılması sonucu oluşan dosyaları indirir.

### <a name="start-using-media-services-apis-with-net-sdk"></a>.NET SDK ile Media Services API’sini kullanmaya başlama

.NET ile Media Services API’lerini kullanmaya başlamak için bir **AzureMediaServicesClient** nesnesi oluşturmanız gerekir. Nesneyi oluşturmak için, Azure AD kullanarak Azure’a bağlanmak üzere istemcinin ihtiyaç duyduğu kimlik bilgilerini sağlamanız gerekir. Makalenin başlangıcında kopyaladığınız kodda, **GetCredentialsAsync** işlevi, yerel yapılandırma dosyasında sağlanan kimlik bilgilerini temel alarak ServiceClientCredentials nesnesi oluşturur. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Bir giriş varlığı oluşturma ve içine yerel dosya yükleme 

**CreateInputAsset** işlevi yeni bir giriş [Varlığı](/rest/api/media/assets) oluşturur ve içine belirtilen yerel video dosyasını yükler. Bu Varlık, kodlama işinizde giriş olarak kullanılır. Media Services v3’te bir İşe yapılan giriş, Varlık olabilir veya HTTPS URL’leri üzerinden Media Services hesabınızın kullanımına açtığınız bir içerik olabilir. HTTPS URL 'sinden kodlama hakkında bilgi edinmek için [Bu](job-input-from-http-how-to.md) makaleye bakın.  

Media Services v3’te dosyaları karşıya yüklemek için Azure Depolama API’lerini kullanırsınız. Aşağıdaki .NET kod parçacığı bunun nasıl yapıldığını gösterir.

Aşağıdaki işlev bu eylemleri tamamlar:

* Bir varlık oluşturur.
* [Depolama alanındaki varlığın kapsayıcısına](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container)yazılabilir bir [SAS URL 'si](../../storage/common/storage-sas-overview.md) alır.

    SAS URL 'Lerini almak için varlığın [Listcontainersas](/rest/api/media/assets/listcontainersas) işlevini kullanıyorsanız, her depolama hesabı için iki depolama hesabı anahtarı olduğu için işlevin bırden çok SAS URL 'si döndürdüğünü unutmayın. Depolama hesabı anahtarlarının sorunsuz bir şekilde değiştirilmesini sağladığından bir depolama hesabının iki anahtarı vardır (örneğin, diğerini kullanırken bir tane değiştirin, yeni anahtarı kullanmaya başlayın ve diğer anahtarı döndürün). 1. SAS URL 'SI, Storage KEY1 ve ikinci bir Storage key2 öğesini temsil eder.
* SAS URL 'sini kullanarak dosyayı depolama alanındaki kapsayıcıya yükler.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>İşin sonucunu depolamak için çıkış varlığı oluşturma

[Çıktı](/rest/api/media/assets) varlığı, işinizin sonucunu depolar. Proje, bu çıktı varlığının sonuçlarını "output" klasörüne indiren **DownloadResults** işlevini tanımlar, böylece elinizde neyin olduğunu görebilirsiniz.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Bir dönüşüm ve videoları analiz eden bir iş oluşturma

Media Services içeriği kodlarken veya işlerken, kodlama ayarlarını tarif olarak ayarlamak için ortak bir modeldir. Daha sonra bu tarifi bir videoya uygulamak üzere bir **İş** gönderirsiniz. Her yeni video için yeni Işler göndererek, bu tarifi kitaplığınızdaki tüm videolara uygulayacağız. Media Services bir tarif, **dönüşüm** olarak adlandırılır. Daha fazla bilgi için bkz. [dönüşümler ve işler](./transform-jobs-concept.md). Bu öğreticide açıklanan örnek, belirtilen videoyu analiz eden bir tarifi tanımlar.

#### <a name="transform"></a>Dönüşüm

Yeni bir [Dönüşüm](/rest/api/media/transforms) örneği oluştururken çıktı olarak neyi üretmesi istediğinizi belirtmeniz gerekir. **Transformoutput**  gerekli bir parametredir. Her **TransformOutput** bir **Ön ayar** içerir. **Ön ayar**, video ve/veya ses işleme işlemlerinin istenen **TransformOutput** nesnesini oluşturmak üzere kullanılacak adım adım yönergelerini açıklar. Bu örnekte, **videoanalizin önceden belirlenmiş** ayarı kullanılır ve dil ("en-US") oluşturucuya ( `new VideoAnalyzerPreset("en-US")` ) geçirilir. Bu ön ayar, bir videodan birden fazla ses ve video içgörüsü elde etmenizi sağlar. Bir videodan birden fazla ses içgörüsü elde etmeniz gerekiyorsa **AudioAnalyzerPreset** ön ayarını kullanın.

Bir **dönüşüm** oluştururken, aşağıdaki kodda gösterildiği gibi **Get** metodunu kullanarak zaten bir tane varsa işaretleyin. Media Services v3’te varlıklar üzerindeki **Get** yöntemleri, varlığın mevcut olmaması durumunda **null** değerini döndürür (büyük/küçük harfe duyarlı ad denetimi).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>İş

Yukarıda bahsedildiği gibi [Transform](/rest/api/media/transforms) nesnesi tarif, [Job](/rest/api/media/jobs) ise bu **Transform** nesnesini belirli bir giriş videosu veya ses içeriğine uygulamak için Media Services’e gönderilen gerçek istektir. **İş** , giriş videonun konumu ve çıktının konumu gibi bilgileri belirtir. Videonuzun konumunu, Media Services hesabınızda bulunan HTTPS URL’lerini, SAS URL’lerini veya Varlıkları kullanarak belirtebilirsiniz.

Bu örnekte, iş girdisi yerel bir videodur.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>İşin tamamlanmasını bekleyin

İşin tamamlaması biraz zaman alır. Ne zaman sorulduğunda bildirim almak istersiniz. [İşin](/rest/api/media/jobs) tamamlanması hakkında bildirim almaya ilişkin farklı seçenekler mevcuttur. En basit seçenek (burada gösterilen) yoklamayı kullanmaktır.

Yoklama, olası gecikme nedeniyle üretim uygulamaları için önerilen en iyi uygulamadır. Yoklama, bir hesap üzerinde gereğinden fazla kullanılırsa kısıtlanabilir. Geliştiricilerin onun yerine Event Grid kullanmalıdır.

Event Grid yüksek kullanılabilirlik, tutarlı performans ve dinamik ölçek için tasarlanmıştır. Event Grid ile uygulamalarınız neredeyse tüm Azure hizmetleri ve özel kaynaklardan gelen olayları takip edip bu olaylara yanıt verebilir. Basit, HTTP tabanlı reaktif olay işleme özelliği, olayların akıllı filtrelenmesi ve yönlendirilmesi sayesinde etkili çözümler oluşturmanıza yardımcı olur. Daha fazla bilgi için bkz. [olayları özel bir Web uç noktasına yönlendirme](monitoring/job-state-events-cli-how-to.md).

**İş** genellik şu aşamalardan geçer: **Zamanlandı**, **Kuyruğa Alındı**, **İşleniyor**, **Tamamlandı** (son aşama). İş bir hatayla karşılaşmışsa, **hata** durumunu alırsınız. İşin iptal edilme süreciiyorsa, **iptal** edip tamamlandığında **iptal etmiş** olursunuz.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>İş hata kodları

Bkz. [hata kodları](/rest/api/media/jobs/get#joberrorcode).

### <a name="download-the-result-of-the-job"></a>İşin sonucunu indirme

Aşağıdaki işlev, iş sonuçlarını incelemenize olanak sağlamak için [Çıkış varlıkındaki sonuçları "çıkış"](/rest/api/media/assets) klasörüne indirir.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Media Services hesabınızdaki kaynakları temizleme

[!INCLUDE [clean-up-warning](includes/clean-up-warning.md)]

Genellikle, yeniden kullanmayı planladığınız nesneler hariç her şeyi temizlemeniz gerekir (genellikle dönüştürmeleri yeniden kullanacaksınız ve Streamingbulleyicileri kalıcı hale getiriyorsunuz). Deneme sonrasında hesabınızın temiz olmasını istiyorsanız, kullanmayı planlamadığınız kaynakları silin. Örneğin, aşağıdaki kod işi ve çıkış varlığını siler:

### <a name="delete-resources-with-code"></a>Kodu kullanarak kaynakları silme

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

CLı 'yi de kullanabilirsiniz.

[!INCLUDE [clean-up-resources-cli](includes/clean-up-resources-cli.md)]

## <a name="run-the-sample-app"></a>Örnek uygulamayı çalıştırma

*Analiz Zevideolarını* uygulamasını çalıştırmak için CTRL + F5 tuşlarına basın.

Programı çalıştırdığınızda iş, videoda bulduğu her yüz için küçük resimler üretir. Ayrıca, insights.json dosyasını oluşturur.

## <a name="examine-the-output"></a>Çıktıyı inceleme

Analiz edilen videoların çıktı dosyası insights.json olarak adlandırılır. Bu dosya, videonuz hakkında içgörüler içerir. Json dosyasındaki öğelerin açıklamalarını [Medya zekası](./analyze-video-audio-files-concept.md) makalesinde bulabilirsiniz.

## <a name="multithreading"></a>Çoklu iş parçacığı kullanımı

> [!WARNING]
> Azure Media Services v3 SDK 'Ları iş parçacığı açısından güvenli değildir. Çok iş parçacıklı bir uygulamayla çalışırken, iş parçacığı başına yeni bir Azudüzeltici Istemci nesnesi oluşturmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: dosyaları karşıya yükleme, kodlama ve akışa alma](stream-files-tutorial-with-api.md)
