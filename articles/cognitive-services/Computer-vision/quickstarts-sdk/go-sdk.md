---
title: 'Hızlı başlangıç: go için istemci kitaplığı Görüntü İşleme | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Go için Görüntü İşleme istemci kitaplığı ile çalışmaya başlayın.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 10/22/2019
ms.author: pafarley
ms.openlocfilehash: f05ef80d34d1c0635bdcdca244aacba6185565fb
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72958416"
---
# <a name="quickstart-computer-vision-client-library-for-go"></a>Hızlı başlangıç: go için istemci kitaplığı Görüntü İşleme

Go için Görüntü İşleme istemci kitaplığı ile çalışmaya başlayın. Paketi yüklemek için bu adımları izleyin ve temel görevler için örnek kodu deneyin. Görüntü İşleme, görüntüleri işlemeye ve bilgi döndürmeye yönelik gelişmiş algoritmalara erişmenizi sağlar.

Git için Görüntü İşleme istemci kitaplığını kullanın:

* Etiketler, metin açıklaması, yüzeyler, yetişkinlere yönelik içerik ve daha fazlası için bir görüntüyü çözümleyin.
* Toplu okuma API 'SI ile yazdırılmış ve el yazısı metinleri tanıyın.

[Başvuru belgeleri](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision) | [kitaplığı kaynak kodu](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision) | [paketi](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
* En son [Go](https://golang.org/dl/) sürümü

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-computer-vision-azure-resource"></a>Görüntü İşleme Azure kaynağı oluşturma

Azure bilişsel hizmetler, abone olduğunuz Azure kaynakları tarafından temsil edilir. Yerel makinenizde [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) veya [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) kullanarak görüntü işleme için bir kaynak oluşturun. Aşağıdakileri de yapabilirsiniz:

* Ücretsiz olarak yedi gün boyunca geçerli bir [deneme anahtarı](https://azure.microsoft.com/try/cognitive-services/#decision) alın. Kaydolduktan sonra [Azure Web sitesinde](https://azure.microsoft.com/try/cognitive-services/my-apis/)mevcut olacaktır.  
* [Azure Portal](https://portal.azure.com/)kaynağı görüntüleyin.

Deneme aboneliğinizden veya kaynağından bir anahtar aldıktan sonra, sırasıyla `COMPUTER_VISION_SUBSCRIPTION_KEY` ve `COMPUTER_VISION_ENDPOINT`adlı anahtar ve uç nokta URL 'SI için [ortam değişkenleri oluşturun](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) .

### <a name="create-a-go-project-directory"></a>Go proje dizini oluştur

Konsol penceresinde (cmd, PowerShell, Terminal, Bash), go projeniz için `my-app`adlı yeni bir çalışma alanı oluşturun ve bu sayfaya gidin.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Çalışma alanınız üç klasör içerir:

* **src** -bu dizin, kaynak kodu ve paketleri içerir. `go get` komutuyla yüklenen tüm paketler bu dizine gider.
* **pkg** -bu dizin, derlenmiş go paketi nesnelerini içerir. Bu dosyaların hepsi `.a` uzantısına sahiptir.
* **bin** -bu dizin, `go install`çalıştırdığınızda oluşturulan ikili yürütülebilir dosyaları içerir.

> [!TIP]
> Go çalışma alanının yapısı hakkında daha fazla bilgi edinmek için [Go Language belgelerine](https://golang.org/doc/code.html#Workspaces)bakın. Bu kılavuz `$GOPATH` ve `$GOROOT`ayarlamaya yönelik bilgiler içerir.

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

Sonra, **src** dizininde `sample-app.go`adlı bir dosya oluşturun:

```bash
cd src
touch sample-app.go
```

`sample-app.go` tercih ettiğiniz IDE veya metin düzenleyicisinde açın. Ardından, paket adını ekleyin ve aşağıdaki kitaplıkları içeri aktarın:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports)]

Ayrıca, betiğinizin kökünde bir bağlam bildirin. Görüntü İşleme işlev çağrılarının çoğunu yürütmek için bu nesne gerekir:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_context)]

Daha sonra, farklı Görüntü İşleme işlemleri gerçekleştirmek için kod eklemeye başlayacaksınız.

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler Görüntü İşleme go SDK 'sının bazı önemli özelliklerini işler.

|Adı|Açıklama|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Bu sınıf, görüntü analizi ve metin okuma gibi tüm Görüntü İşleme işlevleri için gereklidir. Bunu Abonelik bilgileriniz ile birlikte başlatır ve birçok görüntü işlemini yapmak için kullanırsınız.|
|[Imageanalysis](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| Bu tür, bir **analiz Zeımage** işlev çağrısının sonuçlarını içerir. Kategoriye özgü işlevlerin her biri için benzer türler vardır.|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Bu tür, toplu okuma işleminin sonuçlarını içerir. |
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| Bu tür, standart bir çözümle işleminde yapılabilecek farklı görüntü analizi türlerini tanımlar. İhtiyaçlarınıza bağlı olarak bir VisualFeatureTypes değeri kümesi belirtirsiniz. |

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, Go için Görüntü İşleme istemci kitaplığı ile aşağıdaki görevlerin nasıl yapılacağını gösterir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Resim çözümleme](#analyze-an-image)
* [Yazdırılmış ve el yazısı metin oku](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

> [!NOTE]
> Bu adım, Görüntü İşleme anahtarınız ve uç noktanız için sırasıyla `COMPUTER_VISION_SUBSCRIPTION_KEY` ve `COMPUTER_VISION_ENDPOINT` adlı [ortam değişkenleri oluşturduğunuzu](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) varsayar.

Bir `main` işlevi oluşturun ve uç noktanıza ve anahtarınıza sahip bir istemci örneği oluşturmak için aşağıdaki kodu ekleyin.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

## <a name="analyze-an-image"></a>Bir resmi çözümleme

Aşağıdaki kod, uzak bir görüntüyü çözümlemek ve sonuçları konsola yazdırmak için istemci nesnesini kullanır. Metin açıklaması, kategori, etiket listesi, algılanan nesneler, algılanan markalar, algılanan yüzeyler, yetişkinlere yönelik içerik bayrakları, ana renkler ve görüntü türü alabilirsiniz.

### <a name="set-up-test-image"></a>Test görüntüsünü ayarla

Önce, çözümlemek istediğiniz görüntünün URL 'sine bir başvuru kaydedin. Bunu `main` işlevinizin içine koyun.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!NOTE]
> Yerel bir görüntüyü de analiz edebilirsiniz. Yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) 'daki örnek koda bakın.

### <a name="specify-visual-features"></a>Görsel özellikleri belirtin

Aşağıdaki işlev, örnek görüntüden farklı görsel özellikleri ayıklamayı çağırır. Aşağıdaki bölümlerde bu işlevleri tanımlayacaksınız.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>Görüntü açıklamasını al

Aşağıdaki işlev, görüntü için oluşturulan açıklamalı alt yazıların listesini alır. Görüntü açıklaması hakkında daha fazla bilgi için bkz. [görüntüleri](../concept-describing-images.md)açıklama.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>Görüntü kategorisini al

Aşağıdaki işlev görüntünün algılanan kategorisini alır. Daha fazla bilgi için bkz. [görüntüleri kategorilere ayırma](../concept-categorizing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>Görüntü etiketlerini al

Aşağıdaki işlev görüntüde algılanan etiketlerin kümesini alır. Daha fazla bilgi için bkz. [içerik etiketleri](../concept-tagging-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>Nesneleri Algıla

Aşağıdaki işlev görüntüdeki ortak nesneleri algılar ve konsola yazdırır. Daha fazla bilgi için bkz. [nesne algılama](../concept-object-detection.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>Markalar Algıla

Aşağıdaki kod görüntüde kurumsal markaların ve logoları algılar ve bunları konsola yazdırır. Daha fazla bilgi için [marka algılama](../concept-brand-detection.md).

İlk olarak, `main` işlevinizin içindeki yeni bir görüntüye bir başvuru bildirin.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

Aşağıdaki kod, marka algılama işlevini tanımlar.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>Yüz algılama

Aşağıdaki işlev, görüntüde dikdörtgen koordinatlarını ve belirli yüz özniteliklerini içeren algılanan yüzeyleri döndürür. Daha fazla bilgi için bkz. [yüz algılama](../concept-detecting-faces.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Yetişkin, kcy veya Gori içeriğini algılama

Aşağıdaki işlev görüntüde yetişkinlere yönelik içeriğin algılanan varlığını yazdırır. Daha fazla bilgi için bkz. [yetişkin, korcy, Gori içeriği](../concept-detecting-adult-content.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Görüntü renk düzenini al

Aşağıdaki işlev görüntüde, baskın renkler ve vurgu rengi gibi algılanan renk özniteliklerini yazdırır. Daha fazla bilgi için bkz. [renk şemaları](../concept-detecting-color-schemes.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Etki alanına özgü içerik al

Görüntü İşleme, görüntülerde daha fazla analiz yapmak için özel modeller kullanabilir. Daha fazla bilgi için bkz. [etki alanına özgü içerik](../concept-detecting-domain-content.md). 

Aşağıdaki kod görüntüde algılanan ünlüler hakkında verileri ayrıştırır.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

Aşağıdaki kod görüntüde algılanan yer işaretleriyle ilgili verileri ayrıştırır.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Görüntü türünü al

Aşağıdaki işlev, resim türü hakkındaki bilgileri, küçük resim veya çizgi çizimi olup olmadığını&mdash;yazdırır.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Yazdırılmış ve el yazısı metin oku

Görüntü İşleme görüntüdeki görünür metni okuyabilir ve bunu bir karakter akışına dönüştürebilir. Bu bölümdeki kod, görüntüde yazdırılmış veya el yazısı metinleri algılamak ve ayıklamak için istemci nesnesini kullanan `RecognizeTextReadAPIRemoteImage`bir işlevi tanımlar.

Örnek görüntü başvurusunu ve işlev çağrısını `main` işlevinizde ekleyin.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!NOTE]
> Ayrıca, yerel görüntüden metin de ayıklayabilirsiniz. Yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) 'daki örnek koda bakın.

### <a name="call-the-read-api"></a>Okuma API 'sini çağırma

`RecognizeTextReadAPIRemoteImage`metin okumak için yeni işlevi tanımlayın. Belirtilen görüntü için **Batchreadfile** yöntemini çağıran aşağıdaki kodu ekleyin. Bu yöntem bir işlem KIMLIĞI döndürür ve görüntünün içeriğini okumak için zaman uyumsuz bir işlem başlatır.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>Okuma sonuçları al

Sonra, **Batchreadfile** çağrısından döndürülen işlem kimliğini alın ve bunu işlem sonuçları için hizmeti sorgulamak üzere **Getreadoperationresult** yöntemiyle birlikte kullanın. Aşağıdaki kod, sonuçlar döndürülünceye kadar işlemi tek saniyelik aralıklarla denetler. Daha sonra ayıklanan metin verilerini konsola yazdırır.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>Okuma sonuçlarını görüntüle

Alınan metin verilerini ayrıştırmak ve göstermek için aşağıdaki kodu ekleyin ve işlev tanımını sona erdirin.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı `go run` komutuyla uygulama dizininizden çalıştırın.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Görüntü İşleme API'si Başvurusu (git)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)

* [Görüntü İşleme API'si nedir?](../Home.md)
* Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go)' da bulunabilir.
