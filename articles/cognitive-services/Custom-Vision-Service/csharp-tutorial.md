---
title: 'Hızlı Başlangıç: C# için Özel Görüntü İşleme SDK’sı ile görüntü sınıflandırma projesi oluşturma'
titleSuffix: Azure Cognitive Services
description: Bir proje oluşturun, etiketler ekleyin, görüntüleri karşıya yükleyin, projenizi eğitin ve C# ile .NET SDK'yı kullanarak bir tahminde bulunun.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 08/08/2019
ms.author: anroth
ms.openlocfilehash: ca21bbd77b269e3034fd69cc4685311e91295f36
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "73519116"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-net-sdk"></a>Hızlı Başlangıç: Özel Görüntü İşleme .NET SDK'sıyla görüntü sınıflandırma projesi oluşturma

Bu makalede, Özel Görüntü İşleme SDK'sını C# ile kullanarak görüntü sınıflandırma modeli oluşturmaya başlarken size yardımcı olacak bilgiler ve örnek kod sağlanır. Oluşturulduktan sonra etiketler ekleyebilir, görüntüleri karşıya yükleyebilir, projeyi eğitebilir, projenin varsayılan tahmin uç nokta URL’sini alabilir ve bir görüntüyü programlama yoluyla test etmek için uç noktayı kullanabilirsiniz. Kendi .NET uygulamanızı oluştururken bu örneği şablon olarak kullanın. Kod _içermeyen_ bir sınıflandırma modeli oluşturma ve kullama işlemi yapmak istiyorsanız, [tarayıcı tabanlı kılavuz](getting-started-build-a-classifier.md) konusuna bakın.

## <a name="prerequisites"></a>Önkoşullar

- [Visual Studio 2015 veya 2017](https://www.visualstudio.com/downloads/)'nin herhangi bir sürümü
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Özel Görüntü İşleme SDK’sını ve örnek kodu alma

Özel Görüntü İşleme kullanan bir .NET uygulaması yazmak için Özel Görüntü İşleme NuGet paketlerine ihtiyacınız olacaktır. Bu paketler, indirileceği örnek projeye dahildir, ancak bunlara ayrı ayrı erişebilirsiniz.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

[Bilişsel Hizmetler .NET Örnekleri](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples) projesini kopyalayın veya indirin. Visual Studio'da **CustomVision/ImageClassification** klasörüne gidin ve _ImageClassification.csproj_ dosyasını açın.

Bu Visual Studio projesi, __Özel Görüntü İşleme web sitesi__ üzerinden erişilebilen [My New Project](https://customvision.ai/) adlı yeni bir proje oluşturur. Daha sonra bir sınıflandırıcıyı eğitip test etmek için görüntüleri karşıya yükler. Bu projede sınıflandırıcının, bir ağacın __Köknar__ mı yoksa __Japon Kirazı__ mı olduğunu belirlemesi hedeflenmiştir.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Kodu anlama

_Program.cs_ dosyasını açın ve kodu inceleyin. Eğitim ve tahmin anahtarlarınız için sırasıyla `CUSTOM_VISION_TRAINING_KEY` ve `CUSTOM_VISION_PREDICTION_KEY`adlı [ortam değişkenleri oluşturun](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) . Komut dosyası, bunları arayacaktır.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_keys)]

Ayrıca, Özel Görüntü İşleme Web sitesinin ayarlar sayfasından uç nokta URL 'nizi alın. `CUSTOM_VISION_ENDPOINT`adlı bir ortam değişkenine kaydedin. Betik, sınıfınızın kökünde buna bir başvuru kaydeder.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_endpoint)]

Aşağıdaki kod satırları projenin birincil işlevini yürütür.

### <a name="create-a-new-custom-vision-service-project"></a>Yeni bir Özel Görüntü İşleme hizmeti projesi oluşturma

Oluşturulan proje, daha önce ziyaret ettiğiniz [Özel Görüntü İşleme web sitesinde](https://customvision.ai/) gösterilir. Projenizi oluştururken diğer seçenekleri belirtmek için [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) yöntemine bakın (sınıflandırıcı Web portalı [oluşturma](getting-started-build-a-classifier.md) kılavuzunda açıklanmıştır).   

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_create)]

### <a name="create-tags-in-the-project"></a>Projede etiketler oluşturma

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Görüntüleri karşıya yükleme ve etiketleme

Bu projenin görüntüleri dahil edilmiştir. Bunlara, **Program.cs**'de _LoadImagesFromDisk_ yönteminde başvurulur. Tek bir toplu işte en fazla 64 görüntü yükleyebilirsiniz.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]

### <a name="train-the-classifier-and-publish"></a>Sınıflandırıcıyı eğitme ve yayımlama

Bu kod, projedeki ilk yinelemeyi oluşturur ve ardından bu yinelemeyi tahmin uç noktasına yayınlar. Yayımlanan yinelemeye verilen ad, tahmin istekleri göndermek için kullanılabilir. Bir yineleme, yayımlanana kadar tahmin uç noktasında kullanılamaz.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

### <a name="set-the-prediction-endpoint"></a>Tahmin uç noktasını ayarlama

Tahmin uç noktası, bir görüntüyü geçerli modele göndermek ve sınıflandırma tahmini almak için kullanabileceğiniz başvurudur.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction_endpoint)]

### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>Varsayılan tahmin uç noktasına bir görüntü gönderme

Bu betikte, test görüntüsü **LoadImagesFromDisk** yönteminde yüklenir ve modelin tahmin çıkışı konsolda görüntülenir. PublishedModelName değişkeninin değeri, Özel Görüntü İşleme portalının **performans** sekmesinde bulunan "Yayınlanan as" değerine karşılık gelmelidir. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulama çalışırken bir konsol penceresi açmalı ve aşağıdaki çıkışı yazmalıdır:

```console
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

Ardından test görüntüsünün (**Images/Test/** yolunda bulunur) düzgün etiketlendiğini doğrulayabilirsiniz. Uygulamadan çıkmak için herhangi bir tuşa basın. Ayrıca [Özel Görüntü İşleme web sitesine](https://customvision.ai) geri dönebilir ve yeni oluşturulan projenizin geçerli durumunu görebilirsiniz.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Sonraki adımlar

Artık kodda görüntü sınıflandırma işleminin her adımının nasıl uygulanabileceğini gördünüz. Bu örnek tek bir eğitim yinelemesi yürütür ama modelinizin daha doğru olmasını sağlamak için çoğunlukla birden çok kez eğitmeniz ve test etmeniz gerekecektir.

> [!div class="nextstepaction"]
> [Modeli test etme ve yeniden eğitme](test-your-model.md)
