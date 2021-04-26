---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 10/26/2020
ms.custom: devx-track-js
ms.openlocfilehash: 617db5594af682bcdb67101e3317ec184e874d73
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725359"
---
Bu kılavuz, bir görüntü sınıflandırma modeli oluşturmak için Node.js Özel Görüntü İşleme istemci kitaplığını kullanmaya başlamanıza yardımcı olacak yönergeler ve örnek kod sağlar. Bir proje oluşturacak, Etiketler ekleyecek, projeyi eğtireceksiniz ve projenin tahmin uç nokta URL 'sini programlı bir şekilde test etmek üzere kullanacaksınız. Bu örneği kendi görüntü tanıma uygulamanızı oluşturmak için bir şablon olarak kullanın.

> [!NOTE]
> Kod _yazmadan bir sınıflandırma modeli derlemek_ ve eğitemak istiyorsanız, bunun yerine [tarayıcı tabanlı kılavuza](../../getting-started-build-a-classifier.md) bakın.

.NET için Özel Görüntü İşleme istemci kitaplığı 'nı kullanın:

* Yeni bir Özel Görüntü İşleme projesi oluşturma
* Projeye Etiketler ekleyin
* Görüntüleri karşıya yükleme ve etiketleme
* Projeyi eğitme
* Geçerli yinelemeyi Yayımla
* Tahmin uç noktasını test etme

Başvuru belgeleri [(eğitim)](/javascript/api/@azure/cognitiveservices-customvision-training/) [(tahmin)](/javascript/api/@azure/cognitiveservices-customvision-prediction/) | Kitaplık kaynak kodu [(eğitim)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-training) [(tahmin)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-prediction) | Paket (NPM) ( [eğitim)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-training) [(tahmin)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-prediction)  |  [örnekleri](/samples/browse/?products=azure&terms=custom%20vision&languages=javascript)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/)
* [Node.js](https://nodejs.org/) geçerli sürümü
* Azure aboneliğiniz olduktan sonra bir Özel Görüntü İşleme kaynak oluşturun ve bir <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title=" "  target="_blank"> </a> eğitim ve tahmin kaynağı oluşturmak ve anahtarlarınızı ve uç noktanızı almak için Azure Portal özel görüntü işleme bir kaynak oluşturun. Dağıtım için bekleyin ve **Kaynağa Git** düğmesine tıklayın.
    * Uygulamanızı Özel Görüntü İşleme bağlamak için oluşturduğunuz kaynaklarda anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
    * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-nodejs-application"></a>Yeni bir Node.js uygulaması oluşturma

Konsol penceresinde (cmd, PowerShell veya Bash gibi), uygulamanız için yeni bir dizin oluşturun ve bu uygulamaya gidin. 

```console
mkdir myapp && cd myapp
```

`npm init`Bir dosya ile bir düğüm uygulaması oluşturmak için komutunu çalıştırın `package.json` . 

```console
npm init
```

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler

Node.js için Özel Görüntü İşleme bir görüntü analizi uygulaması yazmak için Özel Görüntü İşleme NPM paketlerine ihtiyacınız olacaktır. Bunları yüklemek için PowerShell 'de aşağıdaki komutu çalıştırın:

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

Uygulamanızın `package.json` dosyası bağımlılıklarla güncelleştirilir.

Adlı bir dosya oluşturun `index.js` ve aşağıdaki kitaplıkları içeri aktarın:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_imports)]


> [!TIP]
> Tüm hızlı başlangıç kodu dosyasını aynı anda görüntülemek mi istiyorsunuz? Bu hızlı başlangıçta kod örneklerini içeren [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js)'da bulabilirsiniz.

Kaynağınızın Azure uç noktası ve anahtarları için değişkenler oluşturun. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Azure portala gidin. **Önkoşullar** bölümünde oluşturduğunuz özel görüntü işleme eğitim kaynağı başarıyla dağıtılırsa, **sonraki adımlar** altında **Kaynağa Git** düğmesine tıklayın. Anahtarınızı ve uç noktanızı kaynağın **anahtar ve uç nokta** sayfasında bulabilirsiniz. 
>
>Tahmin kaynak KIMLIĞI değerini, kaynağın **Özellikler** sekmesinde, **abonelik kimliği** olarak listelenmiş şekilde bulabilirsiniz.
>
> İşiniz bittiğinde kodu koddan kaldırmayı unutmayın ve hiçbir zaman herkese açık bir şekilde nakletmeyin. Üretim için, kimlik bilgilerinizi depolamak ve bunlara erişmek için güvenli bir yol kullanmayı düşünün. Daha fazla bilgi için bilişsel Hizmetler [güvenlik](../../../cognitive-services-security.md) makalesine bakın.

Ayrıca, zaman uyumsuz çağrılar için proje adınız ve zaman aşımı parametresi alanları ekleyin.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_vars)]


## <a name="object-model"></a>Nesne modeli

|Ad|Açıklama|
|---|---|
|[TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) | Bu sınıf, modellerinizin oluşturulmasını, eğitimini ve yayımlanmasını işler. |
|[PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient)| Bu sınıf, görüntü sınıflandırması tahminleri için modellerinizin sorgulanmasını işler.|
|[Hızlı](/javascript/api/@azure/cognitiveservices-customvision-prediction/prediction)| Bu arabirim tek bir görüntüde tek bir tahmin tanımlar. Nesne KIMLIĞI ve adı ve Güvenirlik puanı özelliklerini içerir.|

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, JavaScript için Özel Görüntü İşleme istemci kitaplığı ile aşağıdaki görevlerin nasıl yapılacağını gösterir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Yeni bir Özel Görüntü İşleme projesi oluşturma](#create-a-new-custom-vision-project)
* [Projeye Etiketler ekleyin](#add-tags-to-the-project)
* [Görüntüleri karşıya yükleme ve etiketleme](#upload-and-tag-images)
* [Projeyi eğitme](#train-the-project)
* [Geçerli yinelemeyi Yayımla](#publish-the-current-iteration)
* [Tahmin uç noktasını test etme](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

Uç noktanız ve anahtarınızla istemci nesneleri oluşturun. Anahtarınızla bir **Apikeycredentials** nesnesi oluşturun ve bir [Trainingapiclient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) ve [PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient) nesnesi oluşturmak için bunu uç noktanızla birlikte kullanın.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_auth)]


## <a name="create-a-new-custom-vision-project"></a>Yeni bir Özel Görüntü İşleme projesi oluşturma

Tüm Özel Görüntü İşleme işlev çağrılarınızı içeren yeni bir işlev başlatın. Yeni bir Özel Görüntü İşleme Hizmeti projesi oluşturmak için aşağıdaki kodu ekleyin.


[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>Projeye Etiketler ekleyin

Projenize sınıflandırma etiketleri oluşturmak için aşağıdaki kodu işlevinizin içine ekleyin:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_tags)]


## <a name="upload-and-tag-images"></a>Görüntüleri karşıya yükleme ve etiketleme

İlk olarak, bu proje için örnek görüntüleri indirin. [Örnek görüntüler klasörünün](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) içeriğini yerel cihazınıza kaydedin.

> [!NOTE]
> Öğreticinizi tamamlamaya yönelik daha geniş bir görüntü kümesine mi ihtiyacınız var? Microsoft garaj projesi olan Trove, eğitim amaçlarıyla görüntü kümeleri toplamanıza ve satın almanıza olanak tanır. Görüntülerinizi topladıktan sonra bunları indirebilir ve ardından Özel Görüntü İşleme projenize her zamanki şekilde aktarabilirsiniz. Daha fazla bilgi edinmek için [Trove sayfasını](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) ziyaret edin.

Projeye örnek görüntüleri eklemek için etiket oluşturduktan sonra aşağıdaki kodu ekleyin. Bu kod, her görüntüyü ilgili etiketiyle birlikte karşıya yükler.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_upload)]

> [!IMPORTANT]
> Bilişsel `sampleDataRoot` Hizmetler Python SDK 'Sı örnekleri deposunu indirdiğiniz yere bağlı olarak, görüntülerin yolunu () değiştirmeniz gerekir.

## <a name="train-the-project"></a>Projeyi eğitme

Bu kod tahmin modelinin ilk yinelemesini oluşturur. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_train)]

## <a name="publish-the-current-iteration"></a>Geçerli yinelemeyi Yayımla

Bu kod, eğitilen yinelemeyi tahmin uç noktasına yayınlar. Yayımlanan yinelemeye verilen ad, tahmin istekleri göndermek için kullanılabilir. Bir yineleme, yayımlanana kadar tahmin uç noktasında kullanılamaz.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Tahmin uç noktasını test etme

Tahmin uç noktasına bir görüntü göndermek ve tahmini almak için aşağıdaki kodu işlevinizin içine ekleyin. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_test)]

Sonra Özel Görüntü İşleme işlevinizi kapatın ve çağırın.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_function_close)]


## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı `node` hızlı başlangıç dosyanızdaki komutla çalıştırın.

```console
node index.js
```

Uygulamanın çıkışı aşağıdaki metne benzer olmalıdır:

```console
Creating project...
Adding images...
Training...
Training started...
Training status: Training
Training status: Training
Training status: Training
Training status: Completed
Results:
         Hemlock: 94.97%
         Japanese Cherry: 0.01%
```

Daha sonra test görüntüsünün ( **<sampleDataRoot> /Test/** içinde bulunur) uygun şekilde etiketlendiğini doğrulayabilirsiniz. Ayrıca [Özel Görüntü İşleme web sitesine](https://customvision.ai) geri dönebilir ve yeni oluşturulan projenizin geçerli durumunu görebilirsiniz.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Sonraki adımlar

Artık, nesne algılama işleminin her adımının kodda nasıl yapılabileceğini gördünüz. Bu örnek tek bir eğitim yinelemesi yürütür, ancak genellikle modelinizi daha doğru hale getirmek için birden çok kez eğitmeniz ve test etmeniz gerekir.

> [!div class="nextstepaction"]
> [Modeli test etme ve yeniden eğitme](../../test-your-model.md)

* [Özel Görüntü İşleme nedir?](../../overview.md)
* Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js) 'da bulunabilir
* [SDK başvuru belgeleri (eğitim)](/javascript/api/@azure/cognitiveservices-customvision-training/)
* [SDK başvuru belgeleri (tahmin)](/javascript/api/@azure/cognitiveservices-customvision-prediction/)
