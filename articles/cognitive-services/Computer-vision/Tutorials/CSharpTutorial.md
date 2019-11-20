---
title: 'Örnek: İçindeki bir görüntü işleme uygulamasını keşfetC#'
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
ms.custom: seodec18
ms.openlocfilehash: 01e932caf5edc91d5556b60d33e4d100574f93f5
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170040"
---
# <a name="sample-explore-an-image-processing-app-with-c"></a>Örnek: İle bir görüntü işleme uygulamasını keşfetC#

Optik karakter tanıma (OCR) gerçekleştirmek, akıllı kırpılan küçük resimler oluşturmak, ayrıca bir görüntüdeki yüzler dahil olmak üzere görsel özellikleri algılamak, sınıflandırmak, etiketlemek ve açıklama eklemek için Görüntü İşleme kullanan temel bir Windows uygulamasını keşfedebilir. Aşağıdaki örnek bir görüntü URL'si veya yerel ortamda depolanan dosya göndermenizi sağlar. Bu açık kaynak örneğini, .NET Framework bir parçası olan Görüntü İşleme API'si ve Windows Presentation Foundation (WPF) kullanarak Windows için kendi uygulamanızı oluşturmak üzere bir şablon olarak kullanabilirsiniz.

> [!div class="checklist"]
> * GitHub 'dan örnek uygulamayı edinme
> * Visual Studio 'da örnek uygulamayı açın ve oluşturun
> * Örnek uygulamayı çalıştırın ve çeşitli senaryolar gerçekleştirmek için onunla etkileşime geçin
> * Örnek uygulamayla birlikte sunulan çeşitli senaryoları inceleyin

## <a name="prerequisites"></a>Önkoşullar

Örnek uygulamayı araştırmadan önce, aşağıdaki önkoşulları karşılatığınızdan emin olun:

* [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) veya üzerine sahip olmanız gerekir.
* Görüntü İşleme için bir abonelik anahtarınız olması gerekir. Deneme bilişsel [Hizmetler](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)'den ücretsiz bir deneme anahtarı edinebilirsiniz. Ya da Görüntü İşleme abone olmak ve anahtarınızı almak için bilişsel [Hizmetler oluşturma](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ' daki yönergeleri izleyin. Hizmet uç noktası URL 'sini de göz atın.

## <a name="get-the-sample-app"></a>Örnek uygulamayı alma

Görüntü işleme örnek uygulama, `Microsoft/Cognitive-Vision-Windows` depodan GitHub 'da kullanılabilir. Bu depo Ayrıca `Microsoft/Cognitive-Common-Windows` depoyu bir git alt modülü olarak içerir. Bu depoyu, alt modül dahil, komut satırından `git clone --recurse-submodules` komutunu kullanarak veya GitHub Desktop kullanarak yinelemeli olarak kopyalayabilirsiniz.

Örneğin, Görüntü İşleme örnek uygulaması için depoyu bir komut isteminden yinelemeli olarak kopyalamak için aşağıdaki komutu çalıştırın:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> Bu depoyu bir ZIP olarak indirmeyin. Git, ZIP olarak bir depoyu indirirken alt modüller içermez.

### <a name="get-optional-sample-images"></a>İsteğe bağlı örnek görüntüleri al

İsteğe bağlı olarak, `Microsoft/Cognitive-Face-Windows` depoda GitHub 'da bulunan [yüz](../../Face/Overview.md) örnek uygulamasıyla birlikte bulunan örnek görüntüleri kullanabilirsiniz. Bu örnek uygulama, birden çok kişinin `/Data`görüntüsünü içeren bir klasör içerir. Bu depoyu Ayrıca, Görüntü İşleme örnek uygulama için açıklanan yöntemlerle yinelemeli olarak kopyalayabilirsiniz.

Örneğin, yüz örnek uygulaması için depoyu bir komut isteminden yinelemeli olarak kopyalamak için aşağıdaki komutu çalıştırın:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>Visual Studio 'da örnek uygulamayı açın ve oluşturun

Örnek uygulamayı çalıştırmadan veya araştırmadan önce, Visual Studio 'Nun bağımlılıkları çözebilmesi için öncelikle örnek uygulamayı oluşturmanız gerekir. Örnek uygulamayı açmak ve derlemek için aşağıdaki adımları uygulayın:

1. Visual Studio çözüm dosyasını `/Sample-WPF/VisionAPI-WPF-Samples.sln`Visual Studio 'da açın.
1. Visual Studio çözümünün iki proje içerdiğinden emin olun:  

   * SampleUserControlLibrary
   * VisionAPI-WPF-örnekler  

   Sampleusercontrollibrary projesi kullanılamıyorsa, `Microsoft/Cognitive-Vision-Windows` depoyu özyinelemeli olarak Klonladığınız onaylayın.
1. Visual Studio 'da, CTRL + SHIFT + B tuşlarına basın ya da şerit menüsünden **Oluştur** ' u seçin ve çözümü derlemek Için **çözüm oluştur** ' u seçin.

## <a name="run-and-interact-with-the-sample-app"></a>Çalıştırma ve örnek uygulamayla etkileşim kurma

Örnek uygulamayı, küçük resim oluşturma veya görüntü etiketleme gibi çeşitli görevleri gerçekleştirirken Görüntü İşleme istemci kitaplığıyla nasıl etkileşime gireceğini görmek için çalıştırabilirsiniz. Çalıştırmak ve örnek uygulamayla etkileşim kurmak için aşağıdaki adımları uygulayın:

1. Derleme tamamlandıktan sonra, **F5** tuşuna basın veya şerit menüsünde **Hata Ayıkla** ' yı seçin ve ardından örnek uygulamayı çalıştırmak için **hata ayıklamayı Başlat** ' ı seçin.
1. Örnek uygulama görüntülenirken, abonelik anahtar yönetimi sayfasını göstermek için gezinti bölmesinden **abonelik anahtar yönetimi** ' ni seçin.
   ![Abonelik anahtarı yönetim sayfası](../Images/Vision_UI_Subscription.PNG)  
1. Abonelik anahtarınızı **abonelik anahtarı**' na girin.
1. Uç noktada Endpoint URL 'sini **girin.**  
   Örneğin, Görüntü İşleme ücretsiz deneme sürümünden abonelik anahtarını kullanıyorsanız, aşağıdaki uç nokta URL 'sini girin:`https://westcentralus.api.cognitive.microsoft.com`  
   [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Örnek uygulamayı bir sonraki çalıştırışınızda abonelik anahtarınızı ve uç nokta URL 'sini girmek istemiyorsanız, abonelik anahtarını ve uç nokta URL 'sini bilgisayarınıza kaydetmek için **ayarı kaydet** ' i seçin. Önceden kaydedilmiş abonelik anahtarınızı ve uç nokta URL 'sini silmek istiyorsanız, **ayarı Sil**' i seçin.

   > [!NOTE]
   > Örnek uygulama, abonelik anahtarınızı ve uç nokta `System.IO.IsolatedStorage`URL 'nizi depolamak için yalıtılmış depolamayı kullanır.

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
  * ImageScenarioPage.cs  
    Örnek uygulamadaki tüm senaryo sayfalarından türetilen ımasanariopage sınıfı. Bu sınıf, tüm senaryo sayfaları tarafından paylaşılan kimlik bilgileri ve biçimlendirme çıktısı sağlama gibi işlevleri yönetir.
  * MainWindow.xaml  
    Örnek uygulamanın ana penceresi, SubscriptionKeyPage ve senaryo sayfalarını sunmak için Samplesenaryolar denetimini kullanır.
  * OCRPage. xaml  
    Metin Tanıma (OCR) senaryosuna yönelik senaryo sayfası.
  * RecognizeLanguage.cs  
    Örnek uygulamadaki çeşitli yöntemler tarafından desteklenen diller hakkında bilgi sağlayan RecognizeLanguage sınıfı.
  * TagsPage. xaml  
    Etiketler oluştur senaryosuna yönelik senaryo sayfası.
  * TextRecognitionPage. xaml  
    Metin Tanıma v2 (Ingilizce) senaryosuna yönelik senaryo sayfası.
  * ThumbnailPage. xaml  
    Küçük resim al senaryosunun senaryo sayfası.

### <a name="explore-the-sample-code"></a>Örnek kodu keşfet

Örnek kodun önemli kısımları, örnek uygulamayı keşfetmenizi kolaylaştırmak için ile başlayan `KEY SAMPLE CODE STARTS HERE` ve ile `KEY SAMPLE CODE ENDS HERE`biten açıklama bloklarıyla çerçeveedilir. Örnek kodun bu temel bölümleri, çeşitli görevleri yapmak için Görüntü İşleme API'si istemci kitaplığını kullanmayı öğrenmeyle ilgili en önemli kodu içerir. Görüntü işleme örnek uygulamasındaki kodun `KEY SAMPLE CODE STARTS HERE` en ilgili bölümleri arasında gezinmek için Visual Studio 'da arama yapabilirsiniz. 

Örneğin, `UploadAndAnalyzeImageAsync` aşağıdaki ve analiz zepage. xaml ' de yer alan yöntemi, `ComputerVisionClient.AnalyzeImageInStreamAsync` yöntemini çağırarak bir yerel görüntüyü çözümlemek için istemci kitaplığının nasıl kullanılacağını gösterir.

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

Bu örnek uygulama, Azure bilişsel hizmetler 'de Görüntü İşleme API'si C# için bir ince istemci sarmalayıcısı olan görüntü işleme API'si istemci kitaplığını kullanır. İstemci kitaplığı, [Microsoft. Azure. Biliveservices. Vision. ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) paketindeki NuGet 'ten kullanılabilir. Visual Studio uygulamasını yapılandırdığınızda, istemci kitaplığını ilgili NuGet paketinden alırsınız. Ayrıca, istemci kitaplığının `/ClientLibrary` kaynak kodunu `Microsoft/Cognitive-Vision-Windows` deponun klasöründe görüntüleyebilirsiniz.

İstemci kitaplığının işlevselliği `ComputerVisionClient` , `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` ad alanında, sınıf tarafından kullanılan modellerden, ad alanında görüntü işleme etkileşimde `Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models` bulunduğunda `ComputerVisionClient` sınıf tarafından kullanılan modeller bulunur. Örnek uygulamayla birlikte bulunan çeşitli xaml senaryo sayfalarında, bu ad alanları için aşağıdaki `using` yönergeleri bulacaksınız:

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

Görüntü işleme örnek uygulamasına dahil olan senaryoları keşfederken, `ComputerVisionClient` sınıfa dahil edilen çeşitli yöntemler hakkında daha fazla bilgi edineceksiniz.

## <a name="explore-the-analyze-image-scenario"></a>Görüntüyü çözümle senaryosunu keşfet

Bu senaryo, analiz Zepage. xaml sayfası tarafından yönetilir. Analiz için görsel özellikleri ve dili seçebilir ve hem görüntüyü hem de sonuçları görebilirsiniz. Senaryo sayfası, görüntünün kaynağına bağlı olarak aşağıdaki yöntemlerden birini kullanarak bunu yapar:

* Uploadandanaliz Zeımageasync  
  Bu yöntem, görüntünün bir `Stream` olarak kodlanması ve `ComputerVisionClient.AnalyzeImageInStreamAsync` yöntemi çağırarak görüntü işleme gönderilmesi gereken yerel görüntüler için kullanılır.
* AnalyzeUrlAsync  
  Bu yöntem, bir `ComputerVisionClient.AnalyzeImageAsync` yöntemi çağırarak görüntünün URL 'sinin görüntü işleme gönderildiği uzak görüntüler için kullanılır.

Yöntemi, belirtilen abonelik anahtarını `ComputerVisionClient` ve uç nokta URL 'sini kullanarak yeni bir örnek oluşturur. `UploadAndAnalyzeImageAsync` Örnek uygulama yerel bir görüntüyü analiz ettiğinden, bu görüntünün içeriğini Görüntü İşleme göndermelidir. `imageFilePath` Bir`Stream`olarak okumak için belirtilen yerel dosyayı açar, sonra senaryo sayfasında seçilen görsel özellikleri ve dili alır. `ComputerVisionClient.AnalyzeImageInStreamAsync` Yöntemini çağırır, dosyayı, görsel özellikleri `Stream` ve dili geçirerek bir `ImageAnalysis` örnek olarak sonucunu döndürür. `ImageScenarioPage` Sınıfından devralınan Yöntemler, döndürülen sonuçları senaryo sayfasında sunar.

Yöntemi, belirtilen abonelik anahtarını `ComputerVisionClient` ve uç nokta URL 'sini kullanarak yeni bir örnek oluşturur. `AnalyzeUrlAsync` Senaryo sayfasında seçilen görsel özellikleri ve dili alır. `ComputerVisionClient.AnalyzeImageInStreamAsync` Yöntemi çağırır, görüntü URL 'si, görsel özellikler ve dil geçirerek bir `ImageAnalysis` örnek olarak sonucunu döndürür. `ImageScenarioPage` Sınıfından devralınan Yöntemler, döndürülen sonuçları senaryo sayfasında sunar.

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>Görüntüyü etki alanı modeliyle çözümle senaryosunu keşfet

Bu senaryo, analiz Zeindomainpage. xaml sayfası tarafından yönetilir. Görüntünün etki alanına özgü analizini gerçekleştirmek için `celebrities` veya `landmarks`gibi bir etki alanı modeli seçebilir ve hem görüntüyü hem de sonuçları görebilirsiniz. Senaryo sayfası, görüntünün kaynağına bağlı olarak aşağıdaki yöntemleri kullanır:

* GetAvailableDomainModelsAsync  
  Bu yöntem, görüntü işleme ' dan kullanılabilir etki alanı modellerinin listesini alır ve `_domainModelComboBox` `ComputerVisionClient.ListModelsAsync` yöntemi kullanarak sayfadaki ComboBox denetimini doldurur.
* Uploadandanalzeındomainımageasync  
  Bu yöntem, görüntünün bir `Stream` olarak kodlanması ve `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` yöntemi çağırarak görüntü işleme gönderilmesi gereken yerel görüntüler için kullanılır.
* AnalyzeInDomainUrlAsync  
  Bu yöntem, bir `ComputerVisionClient.AnalyzeImageByDomainAsync` yöntemi çağırarak görüntünün URL 'sinin görüntü işleme gönderildiği uzak görüntüler için kullanılır.

Yöntemi, belirtilen abonelik anahtarını `ComputerVisionClient` ve uç nokta URL 'sini kullanarak yeni bir örnek oluşturur. `UploadAndAnalyzeInDomainImageAsync` Örnek uygulama yerel bir görüntüyü analiz ettiğinden, bu görüntünün içeriğini Görüntü İşleme göndermelidir. `imageFilePath` Bir`Stream`olarak okumak için belirtilen yerel dosyayı açar, sonra senaryo sayfasında seçilen dili alır. `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` Yöntemini çağırır, dosyayı `Stream` için geçirerek, etki alanı modelinin adı ve dili, sonra da sonucu bir `DomainModelResults` örnek olarak döndürür. `ImageScenarioPage` Sınıfından devralınan Yöntemler, döndürülen sonuçları senaryo sayfasında sunar.

Yöntemi, belirtilen abonelik anahtarını `ComputerVisionClient` ve uç nokta URL 'sini kullanarak yeni bir örnek oluşturur. `AnalyzeInDomainUrlAsync` Senaryo sayfasında seçilen dili alır. `ComputerVisionClient.AnalyzeImageByDomainAsync` Yöntemi çağırır, görüntü URL 'si, görsel özellikler ve dil geçirerek bir `DomainModelResults` örnek olarak sonucunu döndürür. `ImageScenarioPage` Sınıfından devralınan Yöntemler, döndürülen sonuçları senaryo sayfasında sunar.

## <a name="explore-the-describe-image-scenario"></a>Görüntüyü açıkla senaryosunu keşfet

Bu senaryo, DescribePage. xaml sayfası tarafından yönetilir. Görüntünün okunabilir bir açıklamasını oluşturmak için bir dil seçebilirsiniz ve hem görüntüyü hem de sonuçları görebilirsiniz. Senaryo sayfası, görüntünün kaynağına bağlı olarak aşağıdaki yöntemleri kullanır:

* Uploadanddescribeımageasync  
  Bu yöntem, görüntünün bir `Stream` olarak kodlanması ve `ComputerVisionClient.DescribeImageInStreamAsync` yöntemi çağırarak görüntü işleme gönderilmesi gereken yerel görüntüler için kullanılır.
* DescribeUrlAsync  
  Bu yöntem, bir `ComputerVisionClient.DescribeImageAsync` yöntemi çağırarak görüntünün URL 'sinin görüntü işleme gönderildiği uzak görüntüler için kullanılır.

Yöntemi, belirtilen abonelik anahtarını `ComputerVisionClient` ve uç nokta URL 'sini kullanarak yeni bir örnek oluşturur. `UploadAndDescribeImageAsync` Örnek uygulama yerel bir görüntüyü analiz ettiğinden, bu görüntünün içeriğini Görüntü İşleme göndermelidir. `imageFilePath` Bir`Stream`olarak okumak için belirtilen yerel dosyayı açar, sonra senaryo sayfasında seçilen dili alır. `ComputerVisionClient.DescribeImageInStreamAsync` Yöntemini çağırır, dosyayı `Stream` için, en fazla aday sayısı (Bu durumda 3) ve dili geçirerek bir `ImageDescription` örnek olarak sonucunu döndürür. `ImageScenarioPage` Sınıfından devralınan Yöntemler, döndürülen sonuçları senaryo sayfasında sunar.

Yöntemi, belirtilen abonelik anahtarını `ComputerVisionClient` ve uç nokta URL 'sini kullanarak yeni bir örnek oluşturur. `DescribeUrlAsync` Senaryo sayfasında seçilen dili alır. `ComputerVisionClient.DescribeImageAsync` Yöntemi çağırır, görüntü URL 'sini, en fazla aday sayısını (Bu durumda 3) ve dili geçirerek bir `ImageDescription` örnek olarak sonucunu döndürür. `ImageScenarioPage` Sınıfından devralınan Yöntemler, döndürülen sonuçları senaryo sayfasında sunar.

## <a name="explore-the-generate-tags-scenario"></a>Etiket oluştur senaryosunu keşfet

Bu senaryo, TagsPage. xaml sayfası tarafından yönetilir. Bir görüntünün görsel özelliklerini etiketlemek için bir dil seçebilirsiniz ve hem görüntüyü hem de sonuçları görebilirsiniz. Senaryo sayfası, görüntünün kaynağına bağlı olarak aşağıdaki yöntemleri kullanır:

* Uploadandgettagsforımageasync  
  Bu yöntem, görüntünün bir `Stream` olarak kodlanması ve `ComputerVisionClient.TagImageInStreamAsync` yöntemi çağırarak görüntü işleme gönderilmesi gereken yerel görüntüler için kullanılır.
* GenerateTagsForUrlAsync  
  Bu yöntem, bir `ComputerVisionClient.TagImageAsync` yöntemi çağırarak görüntünün URL 'sinin görüntü işleme gönderildiği uzak görüntüler için kullanılır.

Yöntemi, belirtilen abonelik anahtarını `ComputerVisionClient` ve uç nokta URL 'sini kullanarak yeni bir örnek oluşturur. `UploadAndGetTagsForImageAsync` Örnek uygulama yerel bir görüntüyü analiz ettiğinden, bu görüntünün içeriğini Görüntü İşleme göndermelidir. `imageFilePath` Bir`Stream`olarak okumak için belirtilen yerel dosyayı açar, sonra senaryo sayfasında seçilen dili alır. `ComputerVisionClient.TagImageInStreamAsync` Yöntemini çağırır, dosyayı ve dili `Stream` için geçirerek bir `TagResult` örnek olarak sonucunu döndürür. `ImageScenarioPage` Sınıfından devralınan Yöntemler, döndürülen sonuçları senaryo sayfasında sunar.

Yöntemi, belirtilen abonelik anahtarını `ComputerVisionClient` ve uç nokta URL 'sini kullanarak yeni bir örnek oluşturur. `GenerateTagsForUrlAsync` Senaryo sayfasında seçilen dili alır. `ComputerVisionClient.TagImageAsync` Yöntemi çağırır, görüntü URL 'sini ve dilini geçirerek bir `TagResult` örnek olarak sonucunu döndürür. `ImageScenarioPage` Sınıfından devralınan Yöntemler, döndürülen sonuçları senaryo sayfasında sunar.

## <a name="explore-the-recognize-text-ocr-scenario"></a>Metin Tanıma (OCR) senaryosunu keşfet

Bu senaryo, OCRPage. xaml sayfası tarafından yönetilir. Bir görüntüden yazdırılmış metni tanımak ve ayıklamak için bir dil seçebilirsiniz ve hem görüntüyü hem de sonuçları görebilirsiniz. Senaryo sayfası, görüntünün kaynağına bağlı olarak aşağıdaki yöntemleri kullanır:

* Uploadandrecognizeımageasync  
  Bu yöntem, görüntünün bir `Stream` olarak kodlanması ve `ComputerVisionClient.RecognizePrintedTextInStreamAsync` yöntemi çağırarak görüntü işleme gönderilmesi gereken yerel görüntüler için kullanılır.
* RecognizeUrlAsync  
  Bu yöntem, bir `ComputerVisionClient.RecognizePrintedTextAsync` yöntemi çağırarak görüntünün URL 'sinin görüntü işleme gönderildiği uzak görüntüler için kullanılır.

Yöntemi, belirtilen abonelik anahtarını `ComputerVisionClient` ve uç nokta URL 'sini kullanarak yeni bir örnek oluşturur. `UploadAndRecognizeImageAsync` Örnek uygulama yerel bir görüntüyü analiz ettiğinden, bu görüntünün içeriğini Görüntü İşleme göndermelidir. `imageFilePath` Bir`Stream`olarak okumak için belirtilen yerel dosyayı açar, sonra senaryo sayfasında seçilen dili alır. Yönlendirmenin algılanmadığını `ComputerVisionClient.RecognizePrintedTextInStreamAsync` ve dosyayı ve dili `Stream` için geçtiğini ve sonra bir `OcrResult` örnek olarak sonucunu döndürdüğünü belirten yöntemini çağırır. `ImageScenarioPage` Sınıfından devralınan Yöntemler, döndürülen sonuçları senaryo sayfasında sunar.

Yöntemi, belirtilen abonelik anahtarını `ComputerVisionClient` ve uç nokta URL 'sini kullanarak yeni bir örnek oluşturur. `RecognizeUrlAsync` Senaryo sayfasında seçilen dili alır. Yönlendirmenin algılanmadığını `ComputerVisionClient.RecognizePrintedTextAsync` ve görüntü URL 'sini ve dilini geçirmediğini ve sonra sonucu bir `OcrResult` örnek olarak döndürdüğünü belirten yöntemini çağırır. `ImageScenarioPage` Sınıfından devralınan Yöntemler, döndürülen sonuçları senaryo sayfasında sunar.

## <a name="explore-the-recognize-text-v2-english-scenario"></a>Metin Tanıma v2 (Ingilizce) senaryosunu keşfet

Bu senaryo, TextRecognitionPage. xaml sayfası tarafından yönetilir. Bir görüntüden yazılı veya el yazısı metinleri zaman uyumsuz olarak tanıyacak ve ayıklayabileceğiniz tanıma modunu ve dilini seçebilir ve hem görüntüyü hem de sonuçları görebilirsiniz. Senaryo sayfası, görüntünün kaynağına bağlı olarak aşağıdaki yöntemleri kullanır:

* Uploadandrecognizeımageasync  
  Bu yöntem, bir, yöntemi `Stream` `RecognizeAsync` çağırarak ve `ComputerVisionClient.RecognizeTextInStreamAsync` yöntemi için parametreli bir temsilci geçirerek, görüntünün bir olarak kodlanması ve görüntü işleme gönderilmesi gereken yerel görüntüler için kullanılır.
* RecognizeUrlAsync  
  Bu yöntem, yöntemi çağırarak `RecognizeAsync` ve `ComputerVisionClient.RecognizeTextAsync` yöntemi için parametreli bir temsilci geçirerek, görüntünün URL 'sinin görüntü işleme gönderildiği uzak görüntüler için kullanılır.
* RecognizeAsync bu yöntem hem `UploadAndRecognizeImageAsync` hem de yöntemlerinin zaman uyumsuz çağrılmasını ve `RecognizeUrlAsync` `ComputerVisionClient.GetTextOperationResultAsync` yöntemi çağırarak sonuçları yoklamayı işler.

Görüntü İşleme örnek uygulamasına dahil edilen diğer senaryolardan farklı olarak, bu senaryo zaman uyumsuzdur, ancak bu yöntem, işlemi başlatmak için çağrılır, ancak durumu denetlemek ve bu işlemin sonuçlarını döndürmek için farklı bir yöntem çağırılır. Bu senaryodaki mantıksal akış, diğer senaryolardan biraz farklıdır.

Yöntemi, olarak `imageFilePath` `RecognizeAsync`okumakiçin belirtilen yerel dosyayı açar ,sonrayöntemiçağırır,geçirme:`Stream` `UploadAndRecognizeImageAsync`

* Yönteminin parametreli zaman uyumsuz temsilcisi `GetHeadersAsyncFunc` içinbirlambdaifadesi;dosyavetanımamoduiçin,içindeparametresiolarak.`Stream` `ComputerVisionClient.RecognizeTextInStreamAsync`
* `Operation-Location` İçinde`GetOperationUrlFunc`yanıt üst bilgisi değerini almak için bir temsilci için lambda ifadesi.

`RecognizeUrlAsync` Yöntemi yöntemini`RecognizeAsync` çağırır, geçirme:

* `ComputerVisionClient.RecognizeTextAsync` Yönteminin parametreli zaman uyumsuz temsilcisi için, uzak görüntünün URL 'si ve içindeki `GetHeadersAsyncFunc`parametreler olarak tanıma modu ile ilgili lambda ifadesi.
* `Operation-Location` İçinde`GetOperationUrlFunc`yanıt üst bilgisi değerini almak için bir temsilci için lambda ifadesi.

Yöntem tamamlandığında, `UploadAndRecognizeImageAsync` `TextOperationResult` hem hem de yöntemlerisonucubirörnekolarakdöndürür.`RecognizeUrlAsync` `RecognizeAsync` `ImageScenarioPage` Sınıfından devralınan Yöntemler, döndürülen sonuçları senaryo sayfasında sunar.

Yöntemi, geçirilen `ComputerVisionClient.RecognizeTextInStreamAsync` ya`ComputerVisionClient.RecognizeTextAsync`da yöntemi için parametreli temsilciyi çağırır veyanıtbekler.`GetHeadersAsyncFunc` `RecognizeAsync` Yöntemi sonra, yanıt üst bilgisi değerini yanıttan `GetOperationUrlFunc` `Operation-Location` almak için geçirilen temsilciyi çağırır. Bu değer, görüntü işleme geçirilen `GetHeadersAsyncFunc` yöntemin sonuçlarını almak için kullanılan URL 'dir.

Daha sonra `ComputerVisionClient.GetTextOperationResultAsync` yöntemi,`Operation-Location` geçirilen yöntemin`GetHeadersAsyncFunc`durumunu ve sonucunu almak için yanıt üst bilgisinden alınan URL 'yi geçirerek yöntemini çağırır. `RecognizeAsync` Durum, yöntemin tamamlandığını, başarıyla veya başarısız `RecognizeAsync` olduğunu belirtmezse, çağrılar arasında 3 saniye bekledikten sonra Yöntem 3 kez daha fazla çağrı `ComputerVisionClient.GetTextOperationResultAsync` yapmaz. `RecognizeAsync` Yöntemi, sonuçları çağıran metoda döndürür.

## <a name="explore-the-get-thumbnail-scenario"></a>Küçük resim al senaryosunu keşfet

Bu senaryo ThumbnailPage. xaml sayfası tarafından yönetilir. Akıllı kırpma kullanıp kullanmayacağınızı belirtebilir, bir görüntüden küçük resim oluşturmak için istenen yükseklik ve genişlik belirtmenize ve hem görüntüyü hem de sonuçları görmenizi sağlayabilirsiniz. Senaryo sayfası, görüntünün kaynağına bağlı olarak aşağıdaki yöntemleri kullanır:

* UploadAndThumbnailImageAsync  
  Bu yöntem, görüntünün bir `Stream` olarak kodlanması ve `ComputerVisionClient.GenerateThumbnailInStreamAsync` yöntemi çağırarak görüntü işleme gönderilmesi gereken yerel görüntüler için kullanılır.
* ThumbnailUrlAsync  
  Bu yöntem, bir `ComputerVisionClient.GenerateThumbnailAsync` yöntemi çağırarak görüntünün URL 'sinin görüntü işleme gönderildiği uzak görüntüler için kullanılır.

Yöntemi, belirtilen abonelik anahtarını `ComputerVisionClient` ve uç nokta URL 'sini kullanarak yeni bir örnek oluşturur. `UploadAndThumbnailImageAsync` Örnek uygulama yerel bir görüntüyü analiz ettiğinden, bu görüntünün içeriğini Görüntü İşleme göndermelidir. Olarak okumak `imageFilePath` için belirtilen yerel dosyayı açar. `Stream` `ComputerVisionClient.GenerateThumbnailInStreamAsync` Yöntemini çağırır, genişliği, yüksekliği `Stream` , dosyası için ve akıllı kırpma kullanılıp kullanılmayacağını, sonra da sonucunu `Stream`döndürür. `ImageScenarioPage` Sınıfından devralınan Yöntemler, döndürülen sonuçları senaryo sayfasında sunar.

Yöntemi, belirtilen abonelik anahtarını `ComputerVisionClient` ve uç nokta URL 'sini kullanarak yeni bir örnek oluşturur. `RecognizeUrlAsync` `ComputerVisionClient.GenerateThumbnailAsync` Yöntemi çağırır, genişliği, yüksekliği, resmin URL 'sini geçirerek ve akıllı kırpma kullanılıp kullanılmayacağını, sonra sonucunu bir `Stream`olarak döndürür. `ImageScenarioPage` Sınıfından devralınan Yöntemler, döndürülen sonuçları senaryo sayfasında sunar.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, `Microsoft/Cognitive-Vision-Windows` depoyu Klonladığınız klasörü silin. Örnek görüntüleri kullanmayı tercih ettiyseniz, `Microsoft/Cognitive-Face-Windows` depoyu Klonladığınız klasörü de silin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Yüz Tanıma API'si kullanmaya başlayın](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
