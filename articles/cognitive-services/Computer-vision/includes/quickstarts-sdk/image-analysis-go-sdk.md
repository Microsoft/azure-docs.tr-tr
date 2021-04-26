---
title: 'Hızlı başlangıç: go için görüntü analizi istemci kitaplığı'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıç ile Go için görüntü analizi istemci kitaplığı ile çalışmaya başlayın.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 6a0d13dbc0beb90e0e276c5f594ff09c721f06f8
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073382"
---
<a name="HOLTop"></a>

Etiketler, metin açıklaması, yüzeyler, yetişkinlere yönelik içerik ve daha fazlası için bir görüntüyü çözümlemek üzere görüntü analizi istemci kitaplığını kullanın.

[Başvuru belgeleri](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision)  |  [Paket](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/)
* En son [Go](https://golang.org/dl/) sürümü
* Azure aboneliğiniz olduktan sonra, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" "  target="_blank"> </a> anahtarınızı ve uç noktanızı almak için Azure Portal bir Görüntü İşleme kaynağı oluşturun görüntü işleme bir kaynak oluşturun. Dağıtıldıktan sonra **Kaynağa Git ' e** tıklayın.
    * Uygulamanızı Görüntü İşleme hizmetine bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
    * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-go-project-directory"></a>Go proje dizini oluştur

Konsol penceresinde (cmd, PowerShell, Terminal, Bash), go projeniz için adlı yeni bir çalışma alanı oluşturun `my-app` ve buna gidin.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Çalışma alanınız üç klasör içerir:

* **src** -bu dizin, kaynak kodu ve paketleri içerir. Komutuyla yüklenen tüm paketler `go get` Bu dizine gider.
* **pkg** -bu dizin, derlenmiş go paketi nesnelerini içerir. Bu dosyaların hepsi bir `.a` uzantısı vardır.
* **bin** -bu dizin, çalıştırdığınızda oluşturulan ikili yürütülebilir dosyaları içerir `go install` .

> [!TIP]
> Go çalışma alanının yapısı hakkında daha fazla bilgi edinmek için [Go Language belgelerine](https://golang.org/doc/code.html#Workspaces)bakın. Bu kılavuz, ve ayarları hakkında bilgi içerir `$GOPATH` `$GOROOT` .

### <a name="install-the-client-library-for-go"></a>Go için istemci kitaplığını yükler

Ardından, Go için istemci kitaplığını yükleyeceksiniz:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

ya da DEP kullanıyorsanız, deponuzda çalıştırın:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Go uygulaması oluşturma 

Sonra, **src** dizininde adlı bir dosya oluşturun `sample-app.go` :

```bash
cd src
touch sample-app.go
```

`sample-app.go`Tercih ETTIĞINIZ IDE veya metin düzenleyicide açın. Ardından, paket adını ekleyin ve aşağıdaki kitaplıkları içeri aktarın:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports)]

Ayrıca, betiğinizin kökünde bir bağlam bildirin. Çoğu görüntü çözümleme işlevi çağrısının yürütülmesi için bu nesne gerekir:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_context)]

Daha sonra, farklı Görüntü İşleme işlemleri gerçekleştirmek için kod eklemeye başlayacaksınız.

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=set-up-client) [istemciyi ayarladım](?success=set-up-client#object-model)

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler, görüntü analizi git SDK 'sının önemli özelliklerinden bazılarını işler.

|Ad|Açıklama|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Bu sınıf, görüntü analizi ve metin okuma gibi tüm Görüntü İşleme işlevleri için gereklidir. Bunu Abonelik bilgileriniz ile birlikte başlatır ve birçok görüntü işlemini yapmak için kullanırsınız.|
|[Imageanalysis](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| Bu tür, bir **analiz Zeımage** işlev çağrısının sonuçlarını içerir. Kategoriye özgü işlevlerin her biri için benzer türler vardır.|
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| Bu tür, standart bir çözümle işleminde yapılabilecek farklı görüntü analizi türlerini tanımlar. İhtiyaçlarınıza bağlı olarak bir VisualFeatureTypes değeri kümesi belirtirsiniz. |

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, Go için görüntü analizi istemci kitaplığı ile aşağıdaki görevlerin nasıl yapılacağını gösterir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Resim çözümleme](#analyze-an-image)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

> [!NOTE]
> Bu adım, ve sırasıyla adlı Görüntü İşleme anahtarınız ve uç noktanız için [ortam değişkenleri oluşturduğunuzu](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) varsayar `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT` .

Bir `main` işlev oluşturun ve uç noktanıza ve anahtarınıza sahip bir istemci örneği oluşturmak için aşağıdaki kodu ekleyin.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=authenticate-client) [istemciyi doğrulıyorum](?success=authenticate-client#analyze-an-image)

## <a name="analyze-an-image"></a>Resim çözümleme

Aşağıdaki kod, uzak bir görüntüyü çözümlemek ve sonuçları konsola yazdırmak için istemci nesnesini kullanır. Metin açıklaması, kategori, etiket listesi, algılanan nesneler, algılanan markalar, algılanan yüzeyler, yetişkinlere yönelik içerik bayrakları, ana renkler ve görüntü türü alabilirsiniz.

### <a name="set-up-test-image"></a>Test görüntüsünü ayarla

Önce, çözümlemek istediğiniz görüntünün URL 'sine bir başvuru kaydedin. Bunu `main` işlevinizin içine koyun.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!TIP]
> Yerel bir görüntüyü de analiz edebilirsiniz. **DescribeImageInStream** gibi [baseclient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) yöntemlerine bakın. Ya da, yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) 'daki örnek koda bakın.

### <a name="specify-visual-features"></a>Görsel özellikleri belirtin

Aşağıdaki işlev, örnek görüntüden farklı görsel özellikleri ayıklamayı çağırır. Aşağıdaki bölümlerde bu işlevleri tanımlayacaksınız.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>Görüntü açıklamasını al

Aşağıdaki işlev, görüntü için oluşturulan açıklamalı alt yazıların listesini alır. Görüntü açıklaması hakkında daha fazla bilgi için bkz. [görüntüleri](../../concept-describing-images.md)açıklama.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>Görüntü kategorisini al

Aşağıdaki işlev görüntünün algılanan kategorisini alır. Daha fazla bilgi için bkz. [görüntüleri kategorilere ayırma](../../concept-categorizing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>Görüntü etiketlerini al

Aşağıdaki işlev görüntüde algılanan etiketlerin kümesini alır. Daha fazla bilgi için bkz. [içerik etiketleri](../../concept-tagging-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>Nesneleri Algıla

Aşağıdaki işlev görüntüdeki ortak nesneleri algılar ve konsola yazdırır. Daha fazla bilgi için bkz. [nesne algılama](../../concept-object-detection.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>Markalar Algıla

Aşağıdaki kod görüntüde kurumsal markaların ve logoları algılar ve bunları konsola yazdırır. Daha fazla bilgi için [marka algılama](../../concept-brand-detection.md).

İlk olarak, işlevinizin içindeki yeni bir görüntüye bir başvuru bildirin `main` .

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

Aşağıdaki kod, marka algılama işlevini tanımlar.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>Yüz algılama

Aşağıdaki işlev, görüntüde dikdörtgen koordinatlarını ve belirli yüz özniteliklerini içeren algılanan yüzeyleri döndürür. Daha fazla bilgi için bkz. [yüz algılama](../../concept-detecting-faces.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Yetişkin, kcy veya Gori içeriğini algılama

Aşağıdaki işlev görüntüde yetişkinlere yönelik içeriğin algılanan varlığını yazdırır. Daha fazla bilgi için bkz. [yetişkin, korcy, Gori içeriği](../../concept-detecting-adult-content.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Görüntü renk düzenini al

Aşağıdaki işlev görüntüde, baskın renkler ve vurgu rengi gibi algılanan renk özniteliklerini yazdırır. Daha fazla bilgi için bkz. [renk şemaları](../../concept-detecting-color-schemes.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Etki alanına özgü içerik al

Görüntü analizi, görüntülerde daha fazla analiz yapmak için özel modeller kullanabilir. Daha fazla bilgi için bkz. [etki alanına özgü içerik](../../concept-detecting-domain-content.md). 

Aşağıdaki kod görüntüde algılanan ünlüler hakkında verileri ayrıştırır.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

Aşağıdaki kod görüntüde algılanan yer işaretleriyle ilgili verileri ayrıştırır.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Görüntü türünü al

Aşağıdaki işlev, &mdash; küçük resim veya çizgi çizimi gibi görüntü türü hakkında bilgi yazdırır.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=analyze-image) [bir görüntüyü çözümleytim](?success=analyze-image#run-the-application)


## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı komut ile uygulama dizininizden çalıştırın `go run` .

```bash
go run sample-app.go
```

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=run-the-application) [uygulamayı çalıştırdım](?success=run-the-application#clean-up-resources)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=clean-up-resources) [kaynakları temizm](?success=clean-up-resources#next-steps)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, görüntü analizi istemci kitaplığının nasıl yükleneceğini ve temel görüntü çözümleme çağrılarının nasıl yapılacağını öğrendiniz. Sonra, API özelliklerini çözümle hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
>[Çözümle API 'sini çağırma](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Görüntü analizine genel bakış](../../overview-image-analysis.md)
* Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go)' da bulunabilir.

