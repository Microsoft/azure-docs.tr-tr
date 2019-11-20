---
title: 'Hızlı Başlangıç: Java için Özel Görüntü İşleme SDK’sı ile nesne algılama projesi oluşturma'
titleSuffix: Azure Cognitive Services
description: Java SDK'sını kullanarak bir proje oluşturun, etiketler ekleyin, görüntüleri karşıya yükleyin, projenizi eğitin ve nesneleri algılayın.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 08/08/2019
ms.author: areddish
ms.openlocfilehash: 65bf9a88b86bc0e27d848c941f104be0b237d054
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "73519014"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-sdk-for-java"></a>Hızlı Başlangıç: Java için Özel Görüntü İşleme SDK’sı ile nesne algılama projesi oluşturma

Bu makalede, Özel Görüntü İşleme SDK'sını Java ile kullanarak nesne algılama modeli oluşturmaya başlarken size yardımcı olacak bilgiler ve örnek kod sağlanır. Oluşturulduktan sonra etiketlenmiş bölgeler ekleyebilir, görüntüleri karşıya yükleyebilir, projeyi eğitebilir, projenin varsayılan tahmin uç nokta URL’sini alabilir ve bir görüntüyü programlama yoluyla test etmek için uç noktayı kullanabilirsiniz. Kendi Java uygulamanızı oluştururken bu örneği şablon olarak kullanın.

## <a name="prerequisites"></a>Önkoşullar

- Kendi seçtiğiniz bir Java IDE
- [JDK 7 veya 8](https://aka.ms/azure-jdks) yüklenmiş olmalıdır.
- Maven yüklenmiş olmalıdır
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Özel Görüntü İşleme SDK’sını ve örnek kodu alma

Özel Görüntü İşleme kullanan bir Java uygulaması yazmak için Özel Görüntü İşleme maven paketlerine ihtiyacınız olacaktır. Bu paketler, indirileceği örnek projeye dahildir, ancak bunlara ayrı ayrı erişebilirsiniz.

Maven merkezi deposundan Özel Görüntü İşleme SDK’sını yükleyebilirsiniz:
- [Eğitim SDK’sı](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [Tahmin SDK’sı](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

[Bilişsel Hizmetler Java SDK'sı Örnekleri](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master) projesini kopyalayın veya indirin. **Vision/CustomVision/** klasörüne gidin.

Bu Java projesi, __Özel Görüntü İşleme web sitesi__ üzerinden erişilebilen [Sample Java OD Project](https://customvision.ai/) adlı yeni bir Özel Görüntü İşleme nesne algılama projesi oluşturur. Daha sonra bir sınıflandırıcıyı eğitip test etmek için görüntüleri karşıya yükler. Bu projede sınıflandırıcının, bir ağacın __Köknar__ mı yoksa __Japon Kirazı__ mı olduğunu belirlemesi hedeflenmiştir.

[!INCLUDE [get-keys](includes/get-keys.md)]

Program, önemli verilerinizi ortam değişkeni olarak depolayacak şekilde yapılandırılır. PowerShell'de **Vision/CustomVision** klasörüne giderek bu ortam değişkenlerini ayarlayın. Ardından komutları girin:

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>Kodu anlama

Java IDE'nize `Vision/CustomVision` projesini yükleyin ve _CustomVisionSamples.java_ dosyasını açın. **RunSample** metodunu bulun ve **ImageClassification_Sample** yöntemi çağrısını not edin&mdash;bu yöntem, bu kılavuzda kapsanmayan görüntü sınıflandırması senaryosunu yürütür. **ObjectDetection_Sample** yöntemi bu hızlı başlangıcın birincil işlevini gerçekleştirir; yöntemin tanımına gidin ve kodu inceleyin. 

### <a name="create-a-new-custom-vision-service-project"></a>Yeni bir Özel Görüntü İşleme Hizmeti projesi oluşturma

Eğitim istemcisi ve nesne algılama projesini oluşturan kod bloğuna gidin. Oluşturulan proje, daha önce ziyaret ettiğiniz [Özel Görüntü İşleme web sitesinde](https://customvision.ai/) gösterilir. Projenizi oluştururken diğer seçenekleri belirtmek için bkz. [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) yöntemi aşırı yüklemeleri ( [bir algılayıcı derleme](get-started-build-detector.md) Web portalı kılavuzunda açıklanmıştır).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create_od)]

### <a name="add-tags-to-your-project"></a>Projenize etiketler ekleme

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags_od)]

### <a name="upload-and-tag-images"></a>Görüntüleri karşıya yükleme ve etiketleme

Nesne algılama projelerinde görüntüleri etiketlediğinizde etiketli her nesnenin bölgesini normalleştirilmiş koordinatları kullanarak belirtmeniz gerekir. `regionMap` Haritasının tanımına gidin. Bu kod, örnek görüntülerin her birini etiketlendikleri bölgeyle ilişkilendirir.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_od_mapping)]

Ardından projeye görüntüleri ekleyen kod bloğuna atlayın. Görüntüler projenin **src/main/resources** klasöründen okunur ve uygun etiketleri ve bölge koordinatlarıyla hizmete yüklenir.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload_od)]

Önceki kod parçacığı, görüntüleri kaynak akışları olarak alan ve hizmete yükleyen iki yardımcı işlevden yararlanmasına neden olur (tek bir toplu işte en fazla 64 görüntü yükleyebilirsiniz).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

### <a name="train-the-project-and-publish"></a>Projeyi eğitme ve yayımlama

Bu kod, projedeki ilk yinelemeyi oluşturur ve ardından bu yinelemeyi tahmin uç noktasına yayınlar. Yayımlanan yinelemeye verilen ad, tahmin istekleri göndermek için kullanılabilir. Bir yineleme, yayımlanana kadar tahmin uç noktasında kullanılamaz.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train_od)]

### <a name="use-the-prediction-endpoint"></a>Tahmin uç noktasını kullanma

Burada `predictor` nesnesiyle gösterilen tahmin uç noktası, bir görüntüyü geçerli modele göndermek ve sınıflandırma tahmini almak için kullandığınız başvurudur. Bu örnekte `predictor`, tahmin anahtarı ortam değişkeni kullanılarak başka bir yerde tanımlanır.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_prediction_od)]

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Maven kullanarak çözümü derlemek ve çalıştırmak için, PowerShell'de proje dizininde aşağıdaki komutu çalıştırın:

```powershell
mvn compile exec:java
```

Günlüğe kaydetme ve tahmin sonuçları için konsol çıkışını görüntüleyin. Ardından test görüntüsünün etiketlendiğini ve algılama bölgesinin doğru olduğunu onaylayabilirsiniz.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Sonraki adımlar

Artık kodda nesne algılama işleminin her adımının nasıl uygulanabileceğini gördünüz. Bu örnek tek bir eğitim yinelemesi yürütür ama modelinizin daha doğru olmasını sağlamak için çoğunlukla birden çok kez eğitmeniz ve test etmeniz gerekecektir. Sonraki kılavuzda görüntü sınıflandırma konusu üstünde durulur ancak temel ilkeleri nesne algılamaya benzer.

> [!div class="nextstepaction"]
> [Modeli test etme ve yeniden eğitme](test-your-model.md)
