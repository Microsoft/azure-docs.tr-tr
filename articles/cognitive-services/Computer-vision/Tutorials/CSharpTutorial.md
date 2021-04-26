---
title: "Örnek: C 'de görüntü işleme uygulamasını araştırma #"
titleSuffix: Azure Cognitive Services
description: Azure bilişsel hizmetler 'de Görüntü İşleme API'si kullanan temel bir Windows uygulamasını keşfedebilir. OCR gerçekleştirin, küçük resimler oluşturun ve bir görüntüdeki görsel özelliklerle çalışın.
services: cognitive-services
author: PatrickFarley
manager: nolachar
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 0a1878268beb135151a23b87d03839f3a51f4a96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102432596"
---
# <a name="sample-explore-an-image-processing-app-with-c"></a>Örnek: C ile görüntü işleme uygulamasını araştırma #

Optik karakter tanıma (OCR) gerçekleştirmek, akıllı kırpılan küçük resimler oluşturmak, ayrıca bir görüntüdeki yüzler dahil olmak üzere görsel özellikleri algılamak, sınıflandırmak, etiketlemek ve açıklama eklemek için Görüntü İşleme kullanan temel bir Windows uygulamasını keşfedebilir. Aşağıdaki örnek bir görüntü URL'si veya yerel ortamda depolanan dosya göndermenizi sağlar. Bu açık kaynak örneğini, .NET Framework bir parçası olan Görüntü İşleme API'si ve Windows Presentation Foundation (WPF) kullanarak Windows için kendi uygulamanızı oluşturmak üzere bir şablon olarak kullanabilirsiniz.

> [!div class="checklist"]
> * GitHub 'dan örnek uygulamayı edinme
> * Visual Studio 'da örnek uygulamayı açın ve oluşturun
> * Örnek uygulamayı çalıştırın ve çeşitli senaryolar gerçekleştirmek için onunla etkileşime geçin
> * Örnek uygulamayla birlikte sunulan çeşitli senaryoları inceleyin

## <a name="prerequisites"></a>Önkoşullar

Örnek uygulamayı araştırmadan önce, aşağıdaki önkoşulları karşılatığınızdan emin olun:

* [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) veya üzerine sahip olmanız gerekir.
* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/)
* Azure aboneliğiniz olduktan sonra, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" "  target="_blank"> </a> anahtarınızı ve uç noktanızı almak için Azure Portal bir Görüntü İşleme kaynağı oluşturun görüntü işleme bir kaynak oluşturun. Dağıtıldıktan sonra **Kaynağa Git ' e** tıklayın.
    * Uygulamanızı Görüntü İşleme hizmetine bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
    * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.

## <a name="get-the-sample-app"></a>Örnek uygulamayı alma

Görüntü İşleme örnek uygulama, depodan GitHub 'da kullanılabilir `Microsoft/Cognitive-Vision-Windows` . Bu depo Ayrıca `Microsoft/Cognitive-Common-Windows` depoyu bir git alt modülü olarak içerir. Bu depoyu, alt modül dahil, komut `git clone --recurse-submodules` satırından komutunu kullanarak veya GitHub Desktop kullanarak yinelemeli olarak kopyalayabilirsiniz.

Örneğin, Görüntü İşleme örnek uygulaması için depoyu bir komut isteminden yinelemeli olarak kopyalamak için aşağıdaki komutu çalıştırın:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> Bu depoyu bir ZIP olarak indirmeyin. Git, ZIP olarak bir depoyu indirirken alt modüller içermez.

### <a name="get-optional-sample-images"></a>İsteğe bağlı örnek görüntüleri al

İsteğe bağlı olarak, depoda GitHub 'da bulunan [yüz](../../Face/Overview.md) örnek uygulamasıyla birlikte bulunan örnek görüntüleri kullanabilirsiniz `Microsoft/Cognitive-Face-Windows` . Bu örnek uygulama, `/Data` birden çok kişinin görüntüsünü içeren bir klasör içerir. Bu depoyu Ayrıca, Görüntü İşleme örnek uygulama için açıklanan yöntemlerle yinelemeli olarak kopyalayabilirsiniz.

Örneğin, yüz örnek uygulaması için depoyu bir komut isteminden yinelemeli olarak kopyalamak için aşağıdaki komutu çalıştırın:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>Visual Studio 'da örnek uygulamayı açın ve oluşturun

Örnek uygulamayı çalıştırmadan veya araştırmadan önce, Visual Studio 'Nun bağımlılıkları çözebilmesi için öncelikle örnek uygulamayı oluşturmanız gerekir. Örnek uygulamayı açmak ve derlemek için aşağıdaki adımları uygulayın:

1. Visual Studio çözüm dosyasını `/Sample-WPF/VisionAPI-WPF-Samples.sln` Visual Studio 'da açın.
1. Visual Studio çözümünün iki proje içerdiğinden emin olun:  

   * SampleUserControlLibrary
   * VisionAPI-WPF-örnekler  

   SampleUserControlLibrary projesi kullanılamıyorsa, depoyu özyinelemeli olarak Klonladığınız onaylayın `Microsoft/Cognitive-Vision-Windows` .
1. Visual Studio 'da, CTRL + SHIFT + B tuşlarına basın ya da şerit menüsünden **Oluştur** ' u seçin ve çözümü derlemek Için **çözüm oluştur** ' u seçin.

## <a name="run-and-interact-with-the-sample-app"></a>Çalıştırma ve örnek uygulamayla etkileşim kurma

Örnek uygulamayı, küçük resim oluşturma veya görüntü etiketleme gibi çeşitli görevleri gerçekleştirirken Görüntü İşleme istemci kitaplığıyla nasıl etkileşime gireceğini görmek için çalıştırabilirsiniz. Çalıştırmak ve örnek uygulamayla etkileşim kurmak için aşağıdaki adımları uygulayın:

1. Derleme tamamlandıktan sonra, **F5** tuşuna basın veya şerit menüsünde **Hata Ayıkla** ' yı seçin ve ardından örnek uygulamayı çalıştırmak için **hata ayıklamayı Başlat** ' ı seçin.
1. Örnek uygulama görüntülenirken, abonelik anahtar yönetimi sayfasını göstermek için gezinti bölmesinden **abonelik anahtar yönetimi** ' ni seçin.
   ![Abonelik anahtarı yönetim sayfası](../Images/Vision_UI_Subscription.PNG)  
1. Abonelik anahtarınızı **abonelik anahtarı**' na girin.
1. Uç noktada Endpoint URL 'sini **girin.**  
   [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Örnek uygulamayı bir sonraki çalıştırışınızda abonelik anahtarınızı ve uç nokta URL 'sini girmek istemiyorsanız, abonelik anahtarını ve uç nokta URL 'sini bilgisayarınıza kaydetmek için **ayarı kaydet** ' i seçin. Önceden kaydedilmiş abonelik anahtarınızı ve uç nokta URL 'sini silmek istiyorsanız, **ayarı Sil**' i seçin.

   > [!NOTE]
   > Örnek uygulama, `System.IO.IsolatedStorage` abonelik anahtarınızı ve uç nokta URL 'nizi depolamak için yalıtılmış depolamayı kullanır.

1. Gezinti bölmesinde **bir senaryo seçin** ' in altında şu anda örnek uygulamaya dahil olan senaryolardan birini seçin:  

   | Senaryo | Açıklama |
   |----------|-------------|
   |Görüntüyü çözümle | Yerel veya uzak bir görüntüyü çözümlemek için [görüntüyü çözümle](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) işlemini kullanır. Analiz için görsel özellikleri ve dili seçebilir ve hem görüntüyü hem de sonuçları görebilirsiniz.  |
   |Görüntüyü etki alanı modeliyle çözümle | , Seçilen etki alanı modelini kullanarak yerel veya uzak bir görüntüyü çözümlemek için, seçebileceğiniz etki alanı modellerini listelemek için etki alanına özgü [modelleri](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) listeleme Işlemini ve [etki alanına özgü içeriği tanı](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) işlemini kullanır. Analizin dilini de seçebilirsiniz. |
   |Görüntüyü açıkla | Yerel veya uzak görüntünün okunabilir bir açıklamasını oluşturmak için [görüntüyü açıkla](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fe) işlemini kullanır. Ayrıca, açıklama dilini de seçebilirsiniz. |
   |Etiketler oluştur | , Yerel veya uzak bir görüntünün görsel özelliklerini etiketlemek için [resim etiketi](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1ff) işlemini kullanır. Etiketler için kullanılan dili de seçebilirsiniz. |
   |Metin Tanıma (OCR) | Bir görüntüden yazdırılmış metni tanımak ve ayıklamak için [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) işlemini kullanır. Kullanılacak dili seçebilir ya da dili otomatik olarak algılamaya Görüntü İşleme sağlayabilirsiniz. |
   |Metin Tanıma v2 (Ingilizce) | [Metin tanıma](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) kullanır ve bir görüntüden yazdırılmış veya el ile yazılmış metinleri zaman uyumsuz olarak tanımak ve ayıklamak Için [metin tanıma Işlem sonucu işlemlerini alır](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) . |
   |Küçük resim al | Yerel veya uzak görüntü için küçük resim oluşturmak üzere [küçük resim al](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) işlemini kullanır. |

   Aşağıdaki ekran görüntüsünde, örnek bir görüntüyü analiz ettikten sonra görüntüyü çözümle senaryosu için sunulan sayfa gösterilmektedir.
   ![Görüntüyü çözümle sayfasının ekran görüntüsü](../Images/Analyze_Image_Example.PNG)

## <a name="explore-the-sample-app"></a>Örnek uygulamayı keşfet

Görüntü İşleme örnek uygulaması için Visual Studio çözümü iki proje içerir:

* SampleUserControlLibrary  
  SampleUserControlLibrary projesi, birden çok bilişsel hizmet örneği tarafından paylaşılan işlevler sağlar. Proje şunları içerir:
  * Samplesenaryolar  
    Örnekler için başlık çubuğu, gezinti bölmesi ve İçerik bölmesi gibi standartlaştırılmış bir sunum sağlayan UserControl. Görüntü İşleme örnek uygulaması, senaryo sayfalarını göstermek ve abonelik anahtarı ve uç nokta URL 'SI gibi senaryolar genelinde paylaşılan bilgilere erişmek için MainWindow. xaml penceresinde bu denetimi kullanır.
  * SubscriptionKeyPage  
    Örnek uygulama için bir abonelik anahtarı ve uç nokta URL 'SI girmek üzere standartlaştırılmış bir düzen sağlayan bir sayfa. Görüntü İşleme örnek uygulama, senaryo sayfaları tarafından kullanılan abonelik anahtarını ve uç nokta URL 'sini yönetmek için bu sayfayı kullanır.
  * VideoResultControl  
    Video bilgileri için standartlaştırılmış bir sunum sağlayan bir UserControl. Görüntü İşleme örnek uygulama bu denetimi kullanmaz.
* VisionAPI-WPF-örnekler  
  Görüntü İşleme örnek uygulaması için ana proje, bu proje Görüntü İşleme yönelik tüm ilginç işlevleri içerir. Proje şunları içerir:
  * Analiz Zeindomainpage. xaml  
    Görüntüyü etki alanı modeliyle çözümle senaryosu için senaryo sayfası.
  * Analiz Zeımage. xaml  
    Görüntüyü çözümle senaryosunun senaryo sayfası.
  * DescribePage. xaml  
    Görüntüyü açıkla senaryosunun senaryo sayfası.
  * Imasanariopage. cs  
    Örnek uygulamadaki tüm senaryo sayfalarından türetilen ımasanariopage sınıfı. Bu sınıf, tüm senaryo sayfaları tarafından paylaşılan kimlik bilgileri ve biçimlendirme çıktısı sağlama gibi işlevleri yönetir.
  * MainWindow.xaml  
    Örnek uygulamanın ana penceresi, SubscriptionKeyPage ve senaryo sayfalarını sunmak için Samplesenaryolar denetimini kullanır.
  * OCRPage. xaml  
    Metin Tanıma (OCR) senaryosuna yönelik senaryo sayfası.
  * RecognizeLanguage. cs  
    Örnek uygulamadaki çeşitli yöntemler tarafından desteklenen diller hakkında bilgi sağlayan RecognizeLanguage sınıfı.
  * TagsPage. xaml  
    Etiketler oluştur senaryosuna yönelik senaryo sayfası.
  * TextRecognitionPage. xaml  
    Metin Tanıma v2 (Ingilizce) senaryosuna yönelik senaryo sayfası.
  * ThumbnailPage. xaml  
    Küçük resim al senaryosunun senaryo sayfası.

### <a name="explore-the-sample-code"></a>Örnek kodu keşfet

Örnek kodun önemli kısımları `KEY SAMPLE CODE STARTS HERE` `KEY SAMPLE CODE ENDS HERE` , örnek uygulamayı keşfetmenizi kolaylaştırmak için ile başlayan ve ile biten açıklama bloklarıyla çerçeveedilir. Örnek kodun bu temel bölümleri, çeşitli görevleri yapmak için Görüntü İşleme API'si istemci kitaplığını kullanmayı öğrenmeyle ilgili en önemli kodu içerir. `KEY SAMPLE CODE STARTS HERE`Görüntü işleme örnek uygulamasındaki kodun en ilgili bölümleri arasında gezinmek Için Visual Studio 'da arama yapabilirsiniz. 

Örneğin, `UploadAndAnalyzeImageAsync` aşağıdaki ve analiz Zepage. xaml ' de yer alan yöntemi, yöntemini çağırarak bir yerel görüntüyü çözümlemek için istemci kitaplığının nasıl kullanılacağını gösterir `ComputerVisionClient.AnalyzeImageInStreamAsync` .

```csharp
private async Task<ImageAnalysis> UploadAndAnalyzeImageAsync(string imageFilePath)
{
    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE STARTS HERE
    // -----------------------------------------------------------------------

    //
    // Create Cognitive Services Vision API Service client.
    //
    using (var client = new ComputerVisionClient(Credentials) { Endpoint = Endpoint })
    {
        Log("ComputerVisionClient is created");

        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features.
            //
            Log("Calling ComputerVisionClient.AnalyzeImageInStreamAsync()...");
            VisualFeatureTypes[] visualFeatures = GetSelectedVisualFeatures();
            string language = (_language.SelectedItem as RecognizeLanguage).ShortCode;
            ImageAnalysis analysisResult = await client.AnalyzeImageInStreamAsync(imageFileStream, visualFeatures, null, language);
            return analysisResult;
        }
    }

    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE ENDS HERE
    // -----------------------------------------------------------------------
}
```

### <a name="explore-the-client-library"></a>İstemci kitaplığını keşfet

Bu örnek uygulama, Azure bilişsel hizmetler 'de Görüntü İşleme API'si için bir ince C# istemci sarmalayıcısı olan Görüntü İşleme API'si istemci kitaplığını kullanır. İstemci kitaplığı, [Microsoft. Azure. Biliveservices. Vision. ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) paketindeki NuGet 'ten kullanılabilir. Visual Studio uygulamasını yapılandırdığınızda, istemci kitaplığını ilgili NuGet paketinden alırsınız. Ayrıca, istemci kitaplığının kaynak kodunu `/ClientLibrary` deponun klasöründe görüntüleyebilirsiniz `Microsoft/Cognitive-Vision-Windows` .

İstemci kitaplığının işlevselliği, ad alanında, sınıf `ComputerVisionClient` `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` tarafından kullanılan modellerden, ad alanında `ComputerVisionClient` görüntü işleme etkileşimde bulunduğunda sınıf tarafından kullanılan modeller bulunur `Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models` . Örnek uygulamayla birlikte bulunan çeşitli XAML senaryo sayfalarında, `using` Bu ad alanları için aşağıdaki yönergeleri bulacaksınız:

```csharp
// -----------------------------------------------------------------------
// KEY SAMPLE CODE STARTS HERE
// Use the following namespace for ComputerVisionClient.
// -----------------------------------------------------------------------
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
// -----------------------------------------------------------------------
// KEY SAMPLE CODE ENDS HERE
// -----------------------------------------------------------------------
```

`ComputerVisionClient`Görüntü işleme örnek uygulamasına dahil olan senaryoları keşfederken, sınıfa dahil edilen çeşitli yöntemler hakkında daha fazla bilgi edineceksiniz.

## <a name="explore-the-analyze-image-scenario"></a>Görüntüyü çözümle senaryosunu keşfet

Bu senaryo, analiz Zepage. xaml sayfası tarafından yönetilir. Analiz için görsel özellikleri ve dili seçebilir ve hem görüntüyü hem de sonuçları görebilirsiniz. Senaryo sayfası, görüntünün kaynağına bağlı olarak aşağıdaki yöntemlerden birini kullanarak bunu yapar:

* Uploadandanaliz Zeımageasync  
  Bu yöntem, görüntünün bir olarak kodlanması `Stream` ve yöntemi çağırarak görüntü işleme gönderilmesi gereken yerel görüntüler için kullanılır `ComputerVisionClient.AnalyzeImageInStreamAsync` .
* AnalyzeUrlAsync  
  Bu yöntem, bir yöntemi çağırarak görüntünün URL 'sinin Görüntü İşleme gönderildiği uzak görüntüler için kullanılır `ComputerVisionClient.AnalyzeImageAsync` .

`UploadAndAnalyzeImageAsync`Yöntemi `ComputerVisionClient` , belirtilen abonelik anahtarını ve uç nokta URL 'sini kullanarak yeni bir örnek oluşturur. Örnek uygulama yerel bir görüntüyü analiz ettiğinden, bu görüntünün içeriğini Görüntü İşleme göndermelidir. `imageFilePath`Bir olarak okumak için belirtilen yerel dosyayı açar `Stream` , sonra senaryo sayfasında seçilen görsel özellikleri ve dili alır. `ComputerVisionClient.AnalyzeImageInStreamAsync`Yöntemini çağırır, `Stream` dosyayı, görsel özellikleri ve dili geçirerek bir örnek olarak sonucunu döndürür `ImageAnalysis` . Sınıfından devralınan Yöntemler, `ImageScenarioPage` döndürülen sonuçları senaryo sayfasında sunar.

`AnalyzeUrlAsync`Yöntemi `ComputerVisionClient` , belirtilen abonelik anahtarını ve uç nokta URL 'sini kullanarak yeni bir örnek oluşturur. Senaryo sayfasında seçilen görsel özellikleri ve dili alır. `ComputerVisionClient.AnalyzeImageInStreamAsync`Yöntemi çağırır, görüntü URL 'si, görsel özellikler ve dil geçirerek bir örnek olarak sonucunu döndürür `ImageAnalysis` . Sınıfından devralınan Yöntemler, `ImageScenarioPage` döndürülen sonuçları senaryo sayfasında sunar.

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>Görüntüyü etki alanı modeliyle çözümle senaryosunu keşfet

Bu senaryo, analiz Zeindomainpage. xaml sayfası tarafından yönetilir. `celebrities` `landmarks` Görüntünün etki alanına özgü analizini gerçekleştirmek için veya gibi bir etki alanı modeli seçebilir ve hem görüntüyü hem de sonuçları görebilirsiniz. Senaryo sayfası, görüntünün kaynağına bağlı olarak aşağıdaki yöntemleri kullanır:

* GetAvailableDomainModelsAsync  
  Bu yöntem, Görüntü İşleme ' dan kullanılabilir etki alanı modellerinin listesini alır ve `_domainModelComboBox` yöntemi kullanarak sayfadaki ComboBox denetimini doldurur `ComputerVisionClient.ListModelsAsync` .
* Uploadandanalzeındomainımageasync  
  Bu yöntem, görüntünün bir olarak kodlanması `Stream` ve yöntemi çağırarak görüntü işleme gönderilmesi gereken yerel görüntüler için kullanılır `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` .
* AnalyzeInDomainUrlAsync  
  Bu yöntem, bir yöntemi çağırarak görüntünün URL 'sinin Görüntü İşleme gönderildiği uzak görüntüler için kullanılır `ComputerVisionClient.AnalyzeImageByDomainAsync` .

`UploadAndAnalyzeInDomainImageAsync`Yöntemi `ComputerVisionClient` , belirtilen abonelik anahtarını ve uç nokta URL 'sini kullanarak yeni bir örnek oluşturur. Örnek uygulama yerel bir görüntüyü analiz ettiğinden, bu görüntünün içeriğini Görüntü İşleme göndermelidir. `imageFilePath`Bir olarak okumak için belirtilen yerel dosyayı açar `Stream` , sonra senaryo sayfasında seçilen dili alır. `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync`Yöntemini çağırır, dosyayı için geçirerek, `Stream` etki alanı modelinin adı ve dili, sonra da sonucu bir örnek olarak döndürür `DomainModelResults` . Sınıfından devralınan Yöntemler, `ImageScenarioPage` döndürülen sonuçları senaryo sayfasında sunar.

`AnalyzeInDomainUrlAsync`Yöntemi `ComputerVisionClient` , belirtilen abonelik anahtarını ve uç nokta URL 'sini kullanarak yeni bir örnek oluşturur. Senaryo sayfasında seçilen dili alır. `ComputerVisionClient.AnalyzeImageByDomainAsync`Yöntemi çağırır, görüntü URL 'si, görsel özellikler ve dil geçirerek bir örnek olarak sonucunu döndürür `DomainModelResults` . Sınıfından devralınan Yöntemler, `ImageScenarioPage` döndürülen sonuçları senaryo sayfasında sunar.

## <a name="explore-the-describe-image-scenario"></a>Görüntüyü açıkla senaryosunu keşfet

Bu senaryo, DescribePage. xaml sayfası tarafından yönetilir. Görüntünün okunabilir bir açıklamasını oluşturmak için bir dil seçebilirsiniz ve hem görüntüyü hem de sonuçları görebilirsiniz. Senaryo sayfası, görüntünün kaynağına bağlı olarak aşağıdaki yöntemleri kullanır:

* Uploadanddescribeımageasync  
  Bu yöntem, görüntünün bir olarak kodlanması `Stream` ve yöntemi çağırarak görüntü işleme gönderilmesi gereken yerel görüntüler için kullanılır `ComputerVisionClient.DescribeImageInStreamAsync` .
* DescribeUrlAsync  
  Bu yöntem, bir yöntemi çağırarak görüntünün URL 'sinin Görüntü İşleme gönderildiği uzak görüntüler için kullanılır `ComputerVisionClient.DescribeImageAsync` .

`UploadAndDescribeImageAsync`Yöntemi `ComputerVisionClient` , belirtilen abonelik anahtarını ve uç nokta URL 'sini kullanarak yeni bir örnek oluşturur. Örnek uygulama yerel bir görüntüyü analiz ettiğinden, bu görüntünün içeriğini Görüntü İşleme göndermelidir. `imageFilePath`Bir olarak okumak için belirtilen yerel dosyayı açar `Stream` , sonra senaryo sayfasında seçilen dili alır. `ComputerVisionClient.DescribeImageInStreamAsync`Yöntemini çağırır, `Stream` dosyayı için, en fazla aday sayısı (Bu durumda 3) ve dili geçirerek bir örnek olarak sonucunu döndürür `ImageDescription` . Sınıfından devralınan Yöntemler, `ImageScenarioPage` döndürülen sonuçları senaryo sayfasında sunar.

`DescribeUrlAsync`Yöntemi `ComputerVisionClient` , belirtilen abonelik anahtarını ve uç nokta URL 'sini kullanarak yeni bir örnek oluşturur. Senaryo sayfasında seçilen dili alır. `ComputerVisionClient.DescribeImageAsync`Yöntemi çağırır, görüntü URL 'sini, en fazla aday sayısını (Bu durumda 3) ve dili geçirerek bir örnek olarak sonucunu döndürür `ImageDescription` . Sınıfından devralınan Yöntemler, `ImageScenarioPage` döndürülen sonuçları senaryo sayfasında sunar.

## <a name="explore-the-generate-tags-scenario"></a>Etiket oluştur senaryosunu keşfet

Bu senaryo, TagsPage. xaml sayfası tarafından yönetilir. Bir görüntünün görsel özelliklerini etiketlemek için bir dil seçebilirsiniz ve hem görüntüyü hem de sonuçları görebilirsiniz. Senaryo sayfası, görüntünün kaynağına bağlı olarak aşağıdaki yöntemleri kullanır:

* Uploadandgettagsforımageasync  
  Bu yöntem, görüntünün bir olarak kodlanması `Stream` ve yöntemi çağırarak görüntü işleme gönderilmesi gereken yerel görüntüler için kullanılır `ComputerVisionClient.TagImageInStreamAsync` .
* GenerateTagsForUrlAsync  
  Bu yöntem, bir yöntemi çağırarak görüntünün URL 'sinin Görüntü İşleme gönderildiği uzak görüntüler için kullanılır `ComputerVisionClient.TagImageAsync` .

`UploadAndGetTagsForImageAsync`Yöntemi `ComputerVisionClient` , belirtilen abonelik anahtarını ve uç nokta URL 'sini kullanarak yeni bir örnek oluşturur. Örnek uygulama yerel bir görüntüyü analiz ettiğinden, bu görüntünün içeriğini Görüntü İşleme göndermelidir. `imageFilePath`Bir olarak okumak için belirtilen yerel dosyayı açar `Stream` , sonra senaryo sayfasında seçilen dili alır. `ComputerVisionClient.TagImageInStreamAsync`Yöntemini çağırır, `Stream` dosyayı ve dili için geçirerek bir örnek olarak sonucunu döndürür `TagResult` . Sınıfından devralınan Yöntemler, `ImageScenarioPage` döndürülen sonuçları senaryo sayfasında sunar.

`GenerateTagsForUrlAsync`Yöntemi `ComputerVisionClient` , belirtilen abonelik anahtarını ve uç nokta URL 'sini kullanarak yeni bir örnek oluşturur. Senaryo sayfasında seçilen dili alır. `ComputerVisionClient.TagImageAsync`Yöntemi çağırır, görüntü URL 'sini ve dilini geçirerek bir örnek olarak sonucunu döndürür `TagResult` . Sınıfından devralınan Yöntemler, `ImageScenarioPage` döndürülen sonuçları senaryo sayfasında sunar.

## <a name="explore-the-recognize-text-ocr-scenario"></a>Metin Tanıma (OCR) senaryosunu keşfet

Bu senaryo, OCRPage. xaml sayfası tarafından yönetilir. Bir görüntüden yazdırılmış metni tanımak ve ayıklamak için bir dil seçebilirsiniz ve hem görüntüyü hem de sonuçları görebilirsiniz. Senaryo sayfası, görüntünün kaynağına bağlı olarak aşağıdaki yöntemleri kullanır:

* Uploadandrecognizeımageasync  
  Bu yöntem, görüntünün bir olarak kodlanması `Stream` ve yöntemi çağırarak görüntü işleme gönderilmesi gereken yerel görüntüler için kullanılır `ComputerVisionClient.RecognizePrintedTextInStreamAsync` .
* RecognizeUrlAsync  
  Bu yöntem, bir yöntemi çağırarak görüntünün URL 'sinin Görüntü İşleme gönderildiği uzak görüntüler için kullanılır `ComputerVisionClient.RecognizePrintedTextAsync` .

`UploadAndRecognizeImageAsync`Yöntemi `ComputerVisionClient` , belirtilen abonelik anahtarını ve uç nokta URL 'sini kullanarak yeni bir örnek oluşturur. Örnek uygulama yerel bir görüntüyü analiz ettiğinden, bu görüntünün içeriğini Görüntü İşleme göndermelidir. `imageFilePath`Bir olarak okumak için belirtilen yerel dosyayı açar `Stream` , sonra senaryo sayfasında seçilen dili alır. `ComputerVisionClient.RecognizePrintedTextInStreamAsync`Yönlendirmenin algılanmadığını ve `Stream` dosyayı ve dili için geçtiğini ve sonra bir örnek olarak sonucunu döndürdüğünü belirten yöntemini çağırır `OcrResult` . Sınıfından devralınan Yöntemler, `ImageScenarioPage` döndürülen sonuçları senaryo sayfasında sunar.

`RecognizeUrlAsync`Yöntemi `ComputerVisionClient` , belirtilen abonelik anahtarını ve uç nokta URL 'sini kullanarak yeni bir örnek oluşturur. Senaryo sayfasında seçilen dili alır. `ComputerVisionClient.RecognizePrintedTextAsync`Yönlendirmenin algılanmadığını ve görüntü URL 'sini ve dilini geçirmediğini ve sonra sonucu bir örnek olarak döndürdüğünü belirten yöntemini çağırır `OcrResult` . Sınıfından devralınan Yöntemler, `ImageScenarioPage` döndürülen sonuçları senaryo sayfasında sunar.

## <a name="explore-the-recognize-text-v2-english-scenario"></a>Metin Tanıma v2 (Ingilizce) senaryosunu keşfet

Bu senaryo, TextRecognitionPage. xaml sayfası tarafından yönetilir. Bir görüntüden yazılı veya el yazısı metinleri zaman uyumsuz olarak tanıyacak ve ayıklayabileceğiniz tanıma modunu ve dilini seçebilir ve hem görüntüyü hem de sonuçları görebilirsiniz. Senaryo sayfası, görüntünün kaynağına bağlı olarak aşağıdaki yöntemleri kullanır:

* Uploadandrecognizeımageasync  
  Bu yöntem, bir, yöntemi `Stream` çağırarak `RecognizeAsync` ve yöntemi için parametreli bir temsilci geçirerek, görüntünün bir olarak kodlanması ve görüntü işleme gönderilmesi gereken yerel görüntüler için kullanılır `ComputerVisionClient.RecognizeTextInStreamAsync` .
* RecognizeUrlAsync  
  Bu yöntem, yöntemi çağırarak `RecognizeAsync` ve yöntemi için parametreli bir temsilci geçirerek, görüntünün URL 'sinin görüntü işleme gönderildiği uzak görüntüler için kullanılır `ComputerVisionClient.RecognizeTextAsync` .
* RecognizeAsync bu yöntem hem hem de yöntemlerinin zaman uyumsuz çağrılmasını `UploadAndRecognizeImageAsync` ve `RecognizeUrlAsync` yöntemi çağırarak sonuçları yoklamayı işler `ComputerVisionClient.GetTextOperationResultAsync` .

Görüntü İşleme örnek uygulamasına dahil edilen diğer senaryolardan farklı olarak, bu senaryo zaman uyumsuzdur, ancak bu yöntem, işlemi başlatmak için çağrılır, ancak durumu denetlemek ve bu işlemin sonuçlarını döndürmek için farklı bir yöntem çağırılır. Bu senaryodaki mantıksal akış, diğer senaryolardan biraz farklıdır.

`UploadAndRecognizeImageAsync`Yöntemi, olarak okumak için belirtilen yerel dosyayı açar `imageFilePath` `Stream` , sonra `RecognizeAsync` yöntemi çağırır, geçirme:

* Yönteminin parametreli zaman uyumsuz temsilcisi için bir lambda ifadesi; `ComputerVisionClient.RecognizeTextInStreamAsync` `Stream` dosya ve tanıma modu için, içinde parametresi olarak `GetHeadersAsyncFunc` .
* `Operation-Location`İçinde yanıt üst bilgisi değerini almak için bir temsilci için lambda ifadesi `GetOperationUrlFunc` .

`RecognizeUrlAsync`Yöntemi `RecognizeAsync` yöntemini çağırır, geçirme:

* Yönteminin parametreli zaman uyumsuz temsilcisi için `ComputerVisionClient.RecognizeTextAsync` , uzak görüntünün URL 'si ve içindeki parametreler olarak tanıma modu ile ilgili lambda ifadesi `GetHeadersAsyncFunc` .
* `Operation-Location`İçinde yanıt üst bilgisi değerini almak için bir temsilci için lambda ifadesi `GetOperationUrlFunc` .

`RecognizeAsync`Yöntem tamamlandığında, hem hem de `UploadAndRecognizeImageAsync` `RecognizeUrlAsync` yöntemleri sonucu bir örnek olarak döndürür `TextOperationResult` . Sınıfından devralınan Yöntemler, `ImageScenarioPage` döndürülen sonuçları senaryo sayfasında sunar.

`RecognizeAsync`Yöntemi, geçirilen ya da yöntemi için parametreli temsilciyi `ComputerVisionClient.RecognizeTextInStreamAsync` çağırır `ComputerVisionClient.RecognizeTextAsync` `GetHeadersAsyncFunc` ve yanıt bekler. Yöntemi sonra, yanıt `GetOperationUrlFunc` `Operation-Location` üst bilgisi değerini yanıttan almak için geçirilen temsilciyi çağırır. Bu değer, Görüntü İşleme geçirilen yöntemin sonuçlarını almak için kullanılan URL 'dir `GetHeadersAsyncFunc` .

`RecognizeAsync`Daha sonra yöntemi, `ComputerVisionClient.GetTextOperationResultAsync` `Operation-Location` geçirilen yöntemin durumunu ve sonucunu almak için yanıt üst bilgisinden alınan URL 'yi geçirerek yöntemini çağırır `GetHeadersAsyncFunc` . Durum, yöntemin tamamlandığını, başarıyla veya başarısız olduğunu belirtmezse, `RecognizeAsync` `ComputerVisionClient.GetTextOperationResultAsync` çağrılar arasında 3 saniye bekledikten sonra Yöntem 3 kez daha fazla çağrı yapmaz. `RecognizeAsync`Yöntemi, sonuçları çağıran metoda döndürür.

## <a name="explore-the-get-thumbnail-scenario"></a>Küçük resim al senaryosunu keşfet

Bu senaryo ThumbnailPage. xaml sayfası tarafından yönetilir. Akıllı kırpma kullanıp kullanmayacağınızı belirtebilir, bir görüntüden küçük resim oluşturmak için istenen yükseklik ve genişlik belirtmenize ve hem görüntüyü hem de sonuçları görmenizi sağlayabilirsiniz. Senaryo sayfası, görüntünün kaynağına bağlı olarak aşağıdaki yöntemleri kullanır:

* UploadAndThumbnailImageAsync  
  Bu yöntem, görüntünün bir olarak kodlanması `Stream` ve yöntemi çağırarak görüntü işleme gönderilmesi gereken yerel görüntüler için kullanılır `ComputerVisionClient.GenerateThumbnailInStreamAsync` .
* ThumbnailUrlAsync  
  Bu yöntem, bir yöntemi çağırarak görüntünün URL 'sinin Görüntü İşleme gönderildiği uzak görüntüler için kullanılır `ComputerVisionClient.GenerateThumbnailAsync` .

`UploadAndThumbnailImageAsync`Yöntemi `ComputerVisionClient` , belirtilen abonelik anahtarını ve uç nokta URL 'sini kullanarak yeni bir örnek oluşturur. Örnek uygulama yerel bir görüntüyü analiz ettiğinden, bu görüntünün içeriğini Görüntü İşleme göndermelidir. Olarak okumak için belirtilen yerel dosyayı açar `imageFilePath` `Stream` . `ComputerVisionClient.GenerateThumbnailInStreamAsync`Yöntemini çağırır, genişliği, yüksekliği, `Stream` dosyası için ve akıllı kırpma kullanılıp kullanılmayacağını, sonra da sonucunu döndürür `Stream` . Sınıfından devralınan Yöntemler, `ImageScenarioPage` döndürülen sonuçları senaryo sayfasında sunar.

`RecognizeUrlAsync`Yöntemi `ComputerVisionClient` , belirtilen abonelik anahtarını ve uç nokta URL 'sini kullanarak yeni bir örnek oluşturur. `ComputerVisionClient.GenerateThumbnailAsync`Yöntemi çağırır, genişliği, yüksekliği, resmin URL 'sini geçirerek ve akıllı kırpma kullanılıp kullanılmayacağını, sonra sonucunu bir olarak döndürür `Stream` . Sınıfından devralınan Yöntemler, `ImageScenarioPage` döndürülen sonuçları senaryo sayfasında sunar.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, depoyu Klonladığınız klasörü silin `Microsoft/Cognitive-Vision-Windows` . Örnek görüntüleri kullanmayı tercih ettiyseniz, depoyu Klonladığınız klasörü de silin `Microsoft/Cognitive-Face-Windows` .

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Yüz hizmeti 'ni kullanmaya başlama](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
