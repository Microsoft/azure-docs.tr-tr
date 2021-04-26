---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/25/2020
ms.openlocfilehash: d73fef916c2652f1fa4b98fd84173c1ec0abb263
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725185"
---
Python için Özel Görüntü İşleme istemci kitaplığı ile çalışmaya başlayın. Paketi yüklemek için bu adımları izleyin ve bir nesne algılama modeli oluşturmak için örnek kodu deneyin. Bir proje oluşturacak, Etiketler ekleyecek, projeyi eğtireceksiniz ve projenin tahmin uç nokta URL 'sini programlı bir şekilde test etmek üzere kullanacaksınız. Bu örneği kendi görüntü tanıma uygulamanızı oluşturmak için bir şablon olarak kullanın.

> [!NOTE]
> Bir nesne _algılama modelini kod yazmadan derlemek_ ve eğitebilmek istiyorsanız, bunun yerine [tarayıcı tabanlı kılavuza](../../get-started-build-detector.md) bakın.

Python için Özel Görüntü İşleme istemci kitaplığını şu şekilde kullanın:

* Yeni bir Özel Görüntü İşleme projesi oluşturma
* Projeye Etiketler ekleyin
* Görüntüleri karşıya yükleme ve etiketleme
* Projeyi eğitme
* Geçerli yinelemeyi Yayımla
* Tahmin uç noktasını test etme

[Başvuru belgeleri](/python/api/overview/azure/cognitiveservices/customvision)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-customvision/azure/cognitiveservices/vision/customvision)  |  [Paket (Pypı)](https://pypi.org/project/azure-cognitiveservices-vision-customvision/)  |  [Örnekler](/samples/browse/?languages=python&products=azure&term=vision&terms=vision)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * Python yüklemeniz [PIP](https://pip.pypa.io/en/stable/)'yi içermelidir. Komut satırında komutunu çalıştırarak PIP 'nin yüklenip yüklenmediğini kontrol edebilirsiniz `pip --version` . Python 'un en son sürümünü yükleyerek PIP 'yi alın.
* Azure aboneliğiniz olduktan sonra bir Özel Görüntü İşleme kaynak oluşturun ve bir <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title=" "  target="_blank"> </a> eğitim ve tahmin kaynağı oluşturmak ve anahtarlarınızı ve uç noktanızı almak için Azure Portal özel görüntü işleme bir kaynak oluşturun. Dağıtım için bekleyin ve **Kaynağa Git** düğmesine tıklayın.
    * Uygulamanızı Özel Görüntü İşleme bağlamak için oluşturduğunuz kaynaklarda anahtar ve uç nokta gerekir. Anahtarlarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
    * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler

Python için Özel Görüntü İşleme bir görüntü analizi uygulaması yazmak için Özel Görüntü İşleme istemci kitaplığı gerekir. Python yükledikten sonra PowerShell veya konsol penceresinde aşağıdaki komutu çalıştırın:

```powershell
pip install azure-cognitiveservices-vision-customvision
```

### <a name="create-a-new-python-application"></a>Yeni bir Python uygulaması oluşturma

Yeni bir Python dosyası oluşturun ve aşağıdaki kitaplıkları içeri aktarın.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_imports)]

> [!TIP]
> Tüm hızlı başlangıç kodu dosyasını aynı anda görüntülemek mi istiyorsunuz? Bu hızlı başlangıçta kod örneklerini içeren [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py)'da bulabilirsiniz.

Kaynağınızın Azure uç noktası ve abonelik anahtarları için değişkenler oluşturun.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_creds)]


> [!IMPORTANT]
> Azure portala gidin. **Önkoşullar** bölümünde oluşturduğunuz özel görüntü işleme kaynakları başarıyla dağıtılırsa, **sonraki adımlar** altında **Kaynağa Git** düğmesine tıklayın. Anahtarlarınızın ve uç noktanın **kaynak yönetimi** altında kaynakların **anahtar ve uç nokta** sayfalarında bulabilirsiniz. Eğitim kaynağınız için API uç noktasıyla birlikte hem eğitim hem de tahmin kaynaklarınızın anahtarlarını almanız gerekir.
>
> Tahmini kaynak KIMLIĞI değerini kaynağın **genel bakış** sekmesinde, **abonelik kimliği** olarak listelenmiş şekilde bulabilirsiniz.
>
> İşiniz bittiğinde kodınızdan anahtarları kaldırmayı ve hiçbir zaman herkese açık bir şekilde göndermemeyi unutmayın. Üretim için, kimlik bilgilerinizi depolamak ve bunlara erişmek için güvenli bir yol kullanmayı düşünün. Daha fazla bilgi için bilişsel Hizmetler [güvenlik](../../../cognitive-services-security.md) makalesine bakın.

## <a name="object-model"></a>Nesne modeli

|Ad|Açıklama|
|---|---|
|[CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Bu sınıf, modellerinizin oluşturulmasını, eğitimini ve yayımlanmasını işler. |
|[CustomVisionPredictionClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Bu sınıf, nesne algılama tahminlerinin modellerinizin sorgulanmasını işler.|
|[Imagetahmin](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.models.imageprediction)| Bu sınıf tek bir görüntüde tek bir nesne tahminini tanımlar. Nesne KIMLIĞI ve adı, nesnenin sınırlayıcı kutusu konumu ve Güvenirlik puanı özelliklerini içerir.|

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, Python için Özel Görüntü İşleme istemci kitaplığı ile aşağıdakilerin nasıl yapılacağını gösterir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Yeni bir Özel Görüntü İşleme projesi oluşturma](#create-a-new-custom-vision-project)
* [Projeye Etiketler ekleyin](#add-tags-to-the-project)
* [Görüntüleri karşıya yükleme ve etiketleme](#upload-and-tag-images)
* [Projeyi eğitme](#train-the-project)
* [Geçerli yinelemeyi Yayımla](#publish-the-current-iteration)
* [Tahmin uç noktasını test etme](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

Uç noktanız ve anahtarlarınız ile bir eğitim ve tahmin istemcisi oluşturun. Anahtarlarınız ile **ApiKeyServiceClientCredentials** nesneleri oluşturun ve bir [CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) ve [CustomVisionPredictionClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient) nesnesi oluşturmak için bunları uç noktanızla birlikte kullanın.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_auth)]


## <a name="create-a-new-custom-vision-project"></a>Yeni bir Özel Görüntü İşleme projesi oluşturma

Yeni bir Özel Görüntü İşleme hizmeti projesi oluşturmak için betiğinize aşağıdaki kodu ekleyin. 

Projenizi oluştururken diğer seçenekleri belirtmek için [create_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config-&preserve-view=true) yöntemine bakın ( [bir algılayıcı derleme](../../get-started-build-detector.md) Web portalı kılavuzunda açıklanmıştır).  

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>Projeye Etiketler ekleyin

Projenizde nesne etiketleri oluşturmak için aşağıdaki kodu ekleyin:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_tags)]


## <a name="upload-and-tag-images"></a>Görüntüleri karşıya yükleme ve etiketleme

İlk olarak, bu proje için örnek görüntüleri indirin. [Örnek görüntüler klasörünün](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) içeriğini yerel cihazınıza kaydedin.

> [!NOTE]
> Öğreticinizi tamamlamaya yönelik daha geniş bir görüntü kümesine mi ihtiyacınız var? Microsoft garaj projesi olan Trove, eğitim amaçlarıyla görüntü kümeleri toplamanıza ve satın almanıza olanak tanır. Görüntülerinizi topladıktan sonra bunları indirebilir ve ardından Özel Görüntü İşleme projenize her zamanki şekilde aktarabilirsiniz. Daha fazla bilgi edinmek için [Trove sayfasını](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) ziyaret edin.

Nesne algılama projelerinde resimleri etiketlediğinizde, her etiketlenmiş nesnenin bölgesini normalleştirilmiş koordinatları kullanarak belirtmeniz gerekir. Aşağıdaki kod, örnek görüntülerin her birini etiketli bölgesiyle ilişkilendirir. Bölgeler, sınırlayıcı kutuyu normalleştirilmiş koordinatlarıyla belirtir ve koordinatlar şu sırayla verilir: sol, üst, genişlik, yükseklik.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_tagging)]

> [!NOTE]
> Bölgelerin koordinatlarını işaretlemek için bir tıklama ve sürükleme yardımcı programına sahip değilseniz, [Customvision.ai](https://www.customvision.ai/)adresindeki Web Kullanıcı arabirimini kullanabilirsiniz. Bu örnekte, koordinatlar zaten sağlanmış.

Ardından, her bir örnek görüntüsünü bölge koordinatları ile karşıya yüklemek için bu ilişkilerin haritasını kullanın (tek bir toplu işte en fazla 64 görüntü yükleyebilirsiniz). Aşağıdaki kodu ekleyin.


[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_upload)]

> [!NOTE]
> Bilişsel hizmetler Python SDK örnek deposunu indirdiğiniz yere bağlı olarak görüntülerin yolunu değiştirmeniz gerekir.

## <a name="train-the-project"></a>Projeyi eğitme

Bu kod tahmin modelinin ilk yinelemesini oluşturur. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_train)]

> [!TIP]
> Seçili etiketlerle eğitme
>
> İsteğe bağlı olarak, uygulanan etiketlerin yalnızca bir alt kümesini eğitebilirsiniz. Yalnızca belirli etiketlerden yeterince birini uygulamadıysanız bunu yapmak isteyebilirsiniz, ancak bu, başkalarının yeterli olması durumunda. **[Train_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#train-project-project-id--training-type-none--reserved-budget-in-hours-0--force-train-false--notification-email-address-none--selected-tags-none--custom-headers-none--raw-false----operation-config-&preserve-view=true)** çağrısında, *selected_tags* isteğe bağlı parametre ÖĞESINI kullanmak istediğiniz etiketlerin kimlik dizeleri listesine ayarlayın. Model yalnızca listedeki etiketleri tanımak için eğitecektir.

## <a name="publish-the-current-iteration"></a>Geçerli yinelemeyi Yayımla

Bir yineleme, yayımlanana kadar tahmin uç noktasında kullanılamaz. Aşağıdaki kod, modelin geçerli yinelemesini sorgulama için kullanılabilir hale getirir. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_publish)]

## <a name="test-the-prediction-endpoint"></a>Tahmin uç noktasını test etme

Tahmin uç noktasına bir görüntü göndermek ve tahmini almak için dosyanın sonuna aşağıdaki kodu ekleyin:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_test)]


## <a name="run-the-application"></a>Uygulamayı çalıştırma

*CustomVisionQuickstart.py* çalıştırın.

```powershell
python CustomVisionQuickstart.py
```

Uygulamanın çıkışı konsolda görüntülenmelidir. Daha sonra ( **<base_image_location>/images/Test**) üzerinde bulunan test görüntüsünün uygun şekilde etiketlendiğini ve algılama bölgesinin doğru olduğunu doğrulayabilirsiniz. Ayrıca [Özel Görüntü İşleme web sitesine](https://customvision.ai) geri dönebilir ve yeni oluşturulan projenizin geçerli durumunu görebilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Sonraki adımlar

Artık koddaki nesne algılama işleminin her adımını tamamladınız. Bu örnek tek bir eğitim yinelemesi yürütür, ancak genellikle modelinizi daha doğru hale getirmek için birden çok kez eğitmeniz ve test etmeniz gerekir. Sonraki kılavuzda görüntü sınıflandırma konusu üstünde durulur ancak temel ilkeleri nesne algılamaya benzer.

> [!div class="nextstepaction"]
> [Modeli test etme ve yeniden eğitme](../../test-your-model.md)

* [Özel Görüntü İşleme nedir?](../../overview.md)
* Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py) 'da bulunabilir
* [SDK başvuru belgeleri](/python/api/overview/azure/cognitiveservices/customvision)
