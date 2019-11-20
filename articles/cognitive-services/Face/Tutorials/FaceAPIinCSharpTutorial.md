---
title: 'Öğretici: .NET SDK kullanarak görüntüdeki yüz verileri algılama ve görüntüleme'
titleSuffix: Azure Cognitive Services
description: Bu öğreticide, bir görüntüdeki yüzeyleri algılamak ve çerçeveye eklemek için Yüz Tanıma API'si kullanan bir Windows uygulaması oluşturacaksınız.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: pafarley
ms.openlocfilehash: 93932fac9a5e5d4c21adc99bd31e9366a9709cc2
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859106"
---
# <a name="tutorial-create-a-wpf-app-to-display-face-data-in-an-image"></a>Öğretici: Görüntüde yüz verileri göstermek için bir WPF uygulaması oluşturma

Bu öğreticide, bir görüntüdeki yüzeyleri tespit etmek ve ardından bu verileri Kullanıcı arabiriminde sunmak için .NET istemci SDK 'Sı aracılığıyla Azure Yüz Tanıma API'si kullanmayı öğreneceksiniz. Yüzleri algılayan bir Windows Presentation Framework (WPF) uygulaması oluşturacaksınız, her yüz etrafında bir çerçeve çizer ve durum çubuğunda yüzün bir açıklamasını görüntüler. 

Bu öğretici şunların nasıl yapıldığını gösterir:

> [!div class="checklist"]
> - WPF uygulaması oluşturma
> - Yüz Tanıma API'si istemci kitaplığını yükler
> - İstemci kitaplığını kullanarak resimdeki yüzleri algılama
> - Algılanan her yüzün çevresine bir çerçeve çizme
> - Durum çubuğunda vurgulanan yüzün açıklamasını görüntüle

![Algılanan yüzlerin dikdörtgenlerle çerçeve içine alındığını gösteren ekran görüntüsü](../Images/getting-started-cs-detected.png)

Örnek kodun tamamı GitHub 'daki bilişsel [yüz CSharp örnek](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample) deposunda mevcuttur.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun. 


## <a name="prerequisites"></a>Önkoşullar

- Yüz tanıma API'si abonelik anahtarı. Ücretsiz deneme aboneliği anahtarından alabilirsiniz [Bilişsel Hizmetler'i deneyin](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Veya yönergeleri [Bilişsel Hizmetler hesabı oluşturma](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) yüz tanıma API'si hizmete abone ve anahtarınızı alın. Ardından, sırasıyla ve `FACE_ENDPOINT`olarak adlandırılan `FACE_SUBSCRIPTION_KEY` anahtar ve hizmet uç noktası dizesi için [ortam değişkenleri oluşturun](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) .
- [Visual Studio 2015 veya 2017](https://www.visualstudio.com/downloads/)'nin herhangi bir sürümü.

## <a name="create-the-visual-studio-project"></a>Visual Studio projesini oluşturma

Yeni bir WPF uygulaması projesi oluşturmak için aşağıdaki adımları izleyin.

1. Visual Studio 'da yeni proje iletişim kutusunu açın. **Yüklü**ve ardından **görsel C#** ' i genişletin ve ardından **WPF uygulaması (.NET Framework)** seçeneğini belirleyin.
1. Uygulamaya **FaceTutorial** adını verin ve **Tamam**'a tıklayın.
1. Gereken NuGet paketlerini alın. Çözüm Gezgini projenize sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin. ardından, aşağıdaki paketi bulun ve yükledikten sonra:
    - [Microsoft. Azure. Biliveservices. Vision. Face 2.2.0-Önizleme](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="add-the-initial-code"></a>Başlangıç kodunu ekleme

Bu bölümde, uygulamanın temel çerçevesini, yüz özgü özellikleri olmadan ekleyeceksiniz.

### <a name="create-the-ui"></a>Kullanıcı arabirimini oluşturma

*MainWindow. xaml* ' i açın ve içeriği aşağıdaki kodla&mdash;değiştirin. Bu kod, UI penceresini oluşturur. `FacePhoto_MouseMove` Ve`BrowseButton_Click` yöntemleri, daha sonra tanımlayacaksınız olay işleyicileridir.

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?name=snippet_xaml)]

### <a name="create-the-main-class"></a>Ana sınıfı oluşturma

*MainWindow.xaml.cs* 'i açın ve diğer gerekli ad alanlarıyla birlikte istemci kitaplığı ad alanlarını ekleyin. 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_using)]

Sonra, **MainWindow** sınıfına aşağıdaki kodu ekleyin. Bu kod, abonelik anahtarını ve uç noktayı kullanarak bir **Faceclient** örneği oluşturur.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_fields)]

Ardından **MainWindow** oluşturucuyu ekleyin. Uç nokta URL dizenizi denetleyip istemci nesnesiyle ilişkilendirir.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_constructor)]

Son olarak, **BrowseButton_Click** ve **FacePhoto_MouseMove** yöntemlerini sınıfına ekleyin. Bu yöntemler, *MainWindow. xaml*içinde belirtilen olay işleyicilerine karşılık gelir. **BrowseButton_Click** yöntemi, kullanıcının bir. jpg görüntüsü seçmesini sağlayan bir **OpenFileDialog**oluşturur. Sonra, görüntüyü ana pencerede görüntüler. Sonraki adımlarda **BrowseButton_Click** ve **FacePhoto_MouseMove** için kalan kodu eklersiniz. Ayrıca, `faceList` **başvuruya yönelik bir dizi nesnenin listesine** başvurun.&mdash; Bu başvuru, uygulamanızın gerçek yüz verilerini depolayacağı ve çağıracağı yerdir.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_end)] -->

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_end)] -->

### <a name="try-the-app"></a>Uygulamayı deneyin

Uygulamanızı test etmek için menüde **Başlat**'a basın. Uygulama penceresi açıldığında sol alt köşedeki **Araştır** ' a tıklayın. **Dosya açma** iletişim kutusu görünmelidir. Dosya sisteminden bir görüntü seçin ve pencerede görüntülendiğini doğrulayın. Ardından, uygulamayı kapatın ve sonraki adıma ilerleyin.

![Yüzlerin değiştirilmemiş resmini gösteren ekran görüntüsü](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>Görüntüyü karşıya yükleme ve yüzeyleri algılama

Uygulamanız, yerel bir görüntünün karşıya yüklenmesi için [algılama](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) REST API sarmalayan **Faceclient. Face. DetectWithStreamAsync** yöntemini çağırarak yüzeyleri algılar.

Aşağıdaki yöntemi **MainWindow** sınıfına, **FacePhoto_MouseMove** yönteminin altına ekleyin. Bu yöntem, gönderilen resim dosyasını almak ve bir **akışa**göndermek için yüz özniteliklerinin bir listesini tanımlar. Daha sonra bu nesnelerin her ikisini de **Detectwithstreamasync** yöntem çağrısına geçirir.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_uploaddetect)]

## <a name="draw-rectangles-around-faces"></a>Yüzler etrafında dikdörtgenler çizme

Sonra, görüntüde algılanan her bir yüzey etrafında bir dikdörtgen çizmek için kodu ekleyeceksiniz. **MainWindow** sınıfında, **BrowseButton_Click** yönteminin sonuna `FacePhoto.Source = bitmapSource` , satırdan sonra aşağıdaki kodu ekleyin. Bu kod, algılanan yüzlerin bir listesini **UploadAndDetectFaces**çağrısından doldurur. Ardından, her bir yüzey etrafında bir dikdörtgen çizer ve değiştirilen görüntüyü ana pencerede görüntüler.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_mid)]

## <a name="describe-the-faces"></a>Yüzeyleri açıkla

Aşağıdaki yöntemi **MainWindow** sınıfına, **UploadAndDetectFaces** yönteminin altına ekleyin. Bu yöntem, alınan yüz özniteliklerini, yüzü açıklayan bir dizeye dönüştürür.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_facedesc)]

## <a name="display-the-face-description"></a>Yüz açıklamasını görüntüleme

**FacePhoto_MouseMove** yöntemine aşağıdaki kodu ekleyin. Bu olay işleyicisi, imleç algılanan bir yüz dikdörtgeninin `faceDescriptionStatusBar` üzerine geldiğinde içindeki yüz açıklaması dizesini görüntüler.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_mid)]

## <a name="run-the-app"></a>Uygulamayı çalıştırma

Uygulamayı çalıştırın ve içinde yüz içeren bir resim bulun. Yüz Tanıma hizmetinin yanıt vermesi için birkaç saniye bekleyin. Görüntüdeki her yüzey üzerinde kırmızı bir dikdörtgen görmeniz gerekir. Fareyi bir yüz dikdörtgeninin üzerine getirdiğinizde, bu yüzün açıklaması durum çubuğunda görünmelidir.

![Algılanan yüzlerin dikdörtgenlerle çerçeve içine alındığını gösteren ekran görüntüsü](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, yüz hizmeti .NET SDK 'sını kullanmaya yönelik temel süreci öğrenmiş ve bir görüntüdeki yüzeyleri algılayıp çerçeveli bir uygulama oluşturdunuz. Sonra, yüz algılama 'nın ayrıntıları hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Resimdeki Yüzleri Tanıma](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
