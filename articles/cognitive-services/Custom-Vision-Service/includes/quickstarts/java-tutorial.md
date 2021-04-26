---
author: PatrickFarley
ms.custom: devx-track-java
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/13/2020
ms.openlocfilehash: 50596ac331ff47876af3fd0243f7ce8725135b6f
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725348"
---
Bir görüntü sınıflandırma modeli oluşturmak için Java için Özel Görüntü İşleme istemci kitaplığı 'nı kullanmaya başlayın. Paketi yüklemek için bu adımları izleyin ve temel görevler için örnek kodu deneyin. Bu örneği kendi görüntü tanıma uygulamanızı oluşturmak için bir şablon olarak kullanın.

> [!NOTE]
> Kod _yazmadan bir sınıflandırma modeli derlemek_ ve eğitemak istiyorsanız, bunun yerine [tarayıcı tabanlı kılavuza](../../getting-started-build-a-classifier.md) bakın.

Java için Özel Görüntü İşleme istemci kitaplığını kullanarak şunları yapın:

* Yeni bir Özel Görüntü İşleme projesi oluşturma
* Projeye Etiketler ekleyin
* Görüntüleri karşıya yükleme ve etiketleme
* Projeyi eğitme
* Geçerli yinelemeyi Yayımla
* Tahmin uç noktasını test etme

[Başvuru belgeleri](/java/api/overview/azure/cognitiveservices/client/customvision) | Kitaplık kaynak kodu [(eğitim)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-training) [(tahmin)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-prediction)| Yapıt (Maven) [(eğitim)](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-training/1.1.0-preview.2/jar) ( [tahmin)](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-prediction/1.1.0-preview.2/jar)  | 
 [örnekleri](/samples/browse/?products=azure&terms=custom%20vision)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/)
* [Java Development Kit 'in (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html) geçerli sürümü
* [Gradle yapı aracı](https://gradle.org/install/)veya başka bir bağımlılık Yöneticisi.
* Azure aboneliğiniz olduktan sonra bir Özel Görüntü İşleme kaynak oluşturun ve bir <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title=" "  target="_blank"> </a> eğitim ve tahmin kaynağı oluşturmak ve anahtarlarınızı ve uç noktanızı almak için Azure Portal özel görüntü işleme bir kaynak oluşturun. Dağıtım için bekleyin ve **Kaynağa Git** düğmesine tıklayın.
    * Uygulamanızı Özel Görüntü İşleme bağlamak için oluşturduğunuz kaynaklarda anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
    * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-gradle-project"></a>Yeni bir Gradle projesi oluşturma

Konsol penceresinde (cmd, PowerShell veya Bash gibi), uygulamanız için yeni bir dizin oluşturun ve bu uygulamaya gidin. 

```console
mkdir myapp && cd myapp
```

`gradle init`Çalışma dizininizden komutunu çalıştırın. Bu komut, uygulamanızı oluşturmak ve yapılandırmak için çalışma zamanında kullanılan *Build. Gradle. kts* de dahil olmak üzere Gradle için temel derleme dosyaları oluşturur.

```console
gradle init --type basic
```

Bir **DSL** seçmeniz Istendiğinde, **Kotlin**' ı seçin.

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler

*Build. Gradle. kts* ' i bulun ve tercıh ettiğiniz IDE veya metin düzenleyicinizle açın. Ardından aşağıdaki derleme yapılandırması içine kopyalayın. Bu yapılandırma projeyi, giriş noktası **CustomVisionQuickstart** sınıfı olan bir Java uygulaması olarak tanımlar. Özel Görüntü İşleme kitaplıklarını içeri aktarır.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "CustomVisionQuickstart"
}
repositories {
    mavenCentral()
}
dependencies {
    compile(group = "com.azure", name = "azure-cognitiveservices-customvision-training", version = "1.1.0-preview.2")
    compile(group = "com.azure", name = "azure-cognitiveservices-customvision-prediction", version = "1.1.0-preview.2")
}
```

### <a name="create-a-java-file"></a>Java dosyası oluşturma


Çalışma dizininizden bir proje kaynak klasörü oluşturmak için aşağıdaki komutu çalıştırın:

```console
mkdir -p src/main/java
```

Yeni klasöre gidin ve *CustomVisionQuickstart. Java* adlı bir dosya oluşturun. Bunu tercih ettiğiniz düzenleyicide veya IDE 'de açın ve aşağıdaki deyimleri ekleyin `import` :

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_imports)]

> [!TIP]
> Tüm hızlı başlangıç kodu dosyasını aynı anda görüntülemek mi istiyorsunuz? Bu hızlı başlangıçta kod örneklerini içeren [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java)'da bulabilirsiniz.


Uygulamanın **CustomVisionQuickstart** sınıfında, kaynağınızın anahtarları ve uç noktası için değişkenler oluşturun.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_creds)]


> [!IMPORTANT]
> Azure portala gidin. **Önkoşullar** bölümünde oluşturduğunuz özel görüntü işleme kaynakları başarıyla dağıtılırsa, **sonraki adımlar** altında **Kaynağa Git** düğmesine tıklayın. Anahtarlarınızı ve uç noktanızı kaynakların **anahtar ve uç nokta** sayfalarında bulabilirsiniz. Eğitim kaynağınız için API uç noktasıyla birlikte hem eğitim hem de tahmin kaynaklarınızın anahtarlarını almanız gerekir.
>
> Tahmin kaynak KIMLIĞI değerini, kaynağın **Özellikler** sekmesinde, **abonelik kimliği** olarak listelenmiş şekilde bulabilirsiniz.
> 
> İşiniz bittiğinde kodu koddan kaldırmayı unutmayın ve hiçbir zaman herkese açık bir şekilde nakletmeyin. Üretim için, kimlik bilgilerinizi depolamak ve bunlara erişmek için güvenli bir yol kullanmayı düşünün. Daha fazla bilgi için bilişsel Hizmetler [güvenlik](../../../cognitive-services-security.md) makalesine bakın.

Uygulamanın **Main** yönteminde, bu hızlı başlangıçta kullanılan yöntemlere çağrılar ekleyin. Bunları daha sonra tanımlayacaksınız.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_maincalls)]

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler Özel Görüntü İşleme Java istemci kitaplığı 'nın bazı önemli özelliklerini işler.

|Ad|Açıklama|
|---|---|
|[CustomVisionTrainingClient](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Bu sınıf, modellerinizin oluşturulmasını, eğitimini ve yayımlanmasını işler. |
|[CustomVisionPredictionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Bu sınıf, görüntü sınıflandırması tahminleri için modellerinizin sorgulanmasını işler.|
|[Imagetahmin](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.models.imageprediction)| Bu sınıf tek bir görüntüde tek bir tahmin tanımlar. Nesne KIMLIĞI ve adı ve Güvenirlik puanı özelliklerini içerir.|

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, Java için Özel Görüntü İşleme istemci kitaplığı ile aşağıdaki görevlerin nasıl yapılacağını gösterir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Yeni bir Özel Görüntü İşleme projesi oluşturma](#create-a-new-custom-vision-project)
* [Projeye Etiketler ekleyin](#add-tags-to-the-project)
* [Görüntüleri karşıya yükleme ve etiketleme](#upload-and-tag-images)
* [Projeyi eğitme](#train-the-project)
* [Geçerli yinelemeyi Yayımla](#publish-the-current-iteration)
* [Tahmin uç noktasını test etme](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

**Ana** yönteinizde, uç noktanızı ve anahtarlarınızı kullanarak eğitim ve tahmin istemcileri örneğini oluşturun.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_auth)]


## <a name="create-a-custom-vision-project"></a>Özel Görüntü İşleme projesi oluşturma

T # # yeni bir Özel Görüntü İşleme projesi oluşturma

Bu sonraki Yöntem bir görüntü sınıflandırma projesi oluşturur. Oluşturulan proje, daha önce ziyaret ettiğiniz [Özel Görüntü İşleme web sitesinde](https://customvision.ai/) gösterilir. Projenizi oluştururken diğer seçenekleri belirtmek için bkz. [CreateProject](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_&preserve-view=true) yöntemi aşırı yüklemeleri ( [bir algılayıcı derleme](../../get-started-build-detector.md) Web portalı kılavuzunda açıklanmıştır).

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create)]

## <a name="add-tags-to-your-project"></a>Projenize etiketler ekleme

Bu yöntem, modelin eğiteolacağı etiketleri tanımlar.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags)]

## <a name="upload-and-tag-images"></a>Görüntüleri karşıya yükleme ve etiketleme

İlk olarak, bu proje için örnek görüntüleri indirin. [Örnek görüntüler klasörünün](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) içeriğini yerel cihazınıza kaydedin.

> [!NOTE]
> Öğreticinizi tamamlamaya yönelik daha geniş bir görüntü kümesine mi ihtiyacınız var? Microsoft garaj projesi olan Trove, eğitim amaçlarıyla görüntü kümeleri toplamanıza ve satın almanıza olanak tanır. Görüntülerinizi topladıktan sonra bunları indirebilir ve ardından Özel Görüntü İşleme projenize her zamanki şekilde aktarabilirsiniz. Daha fazla bilgi edinmek için [Trove sayfasını](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) ziyaret edin.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload)]

Önceki kod parçacığı, görüntüleri kaynak akışları olarak alan ve hizmete yükleyen iki yardımcı işlevden yararlanmasına neden olur (tek bir toplu işte en fazla 64 görüntü yükleyebilirsiniz).

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

## <a name="train-the-project"></a>Projeyi eğitme

Bu yöntem, projede ilk eğitim yinelemesini oluşturur. Eğitim tamamlanana kadar hizmeti sorgular.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train)]


## <a name="publish-the-current-iteration"></a>Geçerli yinelemeyi Yayımla

Bu yöntem, modelin geçerli yinelemesini sorgulama için kullanılabilir hale getirir. Tahmin istekleri göndermek için model adını bir başvuru olarak kullanabilirsiniz. İçin kendi değerini girmeniz gerekir `predictionResourceId` . Tahmin kaynak KIMLIĞINI, kaynağın **genel bakış** sekmesinde, **abonelik kimliği** olarak listelenen Azure Portal bulabilirsiniz.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_publish)]

## <a name="test-the-prediction-endpoint"></a>Tahmin uç noktasını test etme

Bu yöntem, test görüntüsünü yükler, model uç noktasını sorgular ve tahmin verilerinin konsola çıktısını verir.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict)]

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı ile oluşturabilirsiniz:

```console
gradle build
```

Uygulamayı `gradle run` komutla çalıştırın:

```console
gradle run
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Sonraki adımlar

Artık görüntü sınıflandırma işleminin her adımından kodda nasıl yapılabileceğini gördünüz. Bu örnek tek bir eğitim yinelemesi yürütür, ancak genellikle modelinizi daha doğru hale getirmek için birden çok kez eğitmeniz ve test etmeniz gerekir.

> [!div class="nextstepaction"]
> [Modeli test etme ve yeniden eğitme](../../test-your-model.md)

* [Özel Görüntü İşleme nedir?](../../overview.md)
* Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java) 'da bulunabilir
