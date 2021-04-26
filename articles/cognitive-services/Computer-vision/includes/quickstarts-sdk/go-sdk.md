---
title: 'Hızlı başlangıç: go için optik karakter tanıma istemci kitaplığı'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıç ile Go için optik karakter tanıma istemci kitaplığı ile çalışmaya başlayın.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 0ae059054c68da662e05342525987f6925184906
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073730"
---
<a name="HOLTop"></a>

Görüntülerden yazdırılmış ve el yazısı metinleri okumak için OCR istemci kitaplığını kullanın.

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

> [!TIP]
> Tüm hızlı başlangıç kodu dosyasını aynı anda görüntülemek mi istiyorsunuz? Bu hızlı başlangıçta kod örneklerini içeren [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go)'da bulabilirsiniz.

`sample-app.go`Tercih ETTIĞINIZ IDE veya metin düzenleyicide açın.

Betiğinizin kökünde bir bağlam bildirin. Görüntü İşleme işlev çağrılarının çoğunu yürütmek için bu nesne gerekir.

### <a name="find-the-subscription-key-and-endpoint"></a>Abonelik anahtarını ve uç noktayı bulma

Azure portala gidin. **Önkoşullar** bölümünde oluşturduğunuz görüntü işleme kaynak başarıyla dağıtılırsa, **sonraki adımlar** altında **Kaynağa Git** düğmesine tıklayın. Kaynak **yönetimi** altında, kaynağın **anahtar ve uç** nokta sayfasında abonelik anahtarınızı ve uç noktanızı bulabilirsiniz. 

Görüntü İşleme abonelik anahtarınız ve uç noktanız için değişkenler oluşturun. Abonelik anahtarınızı ve uç noktanızı, belirtilen yerde aşağıdaki koda yapıştırın. Görüntü İşleme uç noktanız formu vardır `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> İşiniz bittiğinde kodunuzda abonelik anahtarını kaldırmayı unutmayın ve hiçbir zaman herkese açık bir şekilde nakletmeyin. Üretim için, kimlik bilgilerinizi depolamak ve bunlara erişmek için güvenli bir yol kullanmayı düşünün. Örneğin, [Azure Anahtar Kasası](../../../../key-vault/general/overview.md).

Daha sonra, farklı OCR işlemlerini gerçekleştirmek için kod eklemeye başlayacaksınız.

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=set-up-client) [istemciyi ayarladım](?success=set-up-client#object-model)

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler, OCR git SDK 'sının bazı önemli özelliklerini işler.

|Ad|Açıklama|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Bu sınıf, görüntü analizi ve metin okuma gibi tüm Görüntü İşleme işlevleri için gereklidir. Bunu Abonelik bilgileriniz ile birlikte başlatır ve birçok görüntü işlemini yapmak için kullanırsınız.|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Bu tür, toplu okuma işleminin sonuçlarını içerir. |

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, Go için OCR istemci kitaplığı ile aşağıdaki görevlerin nasıl yapılacağını gösterir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Yazdırılmış ve el yazısı metin oku](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

> [!NOTE]
> Bu adım, ve sırasıyla adlı Görüntü İşleme anahtarınız ve uç noktanız için [ortam değişkenleri oluşturduğunuzu](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) varsayar `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT` .

Bir `main` işlev oluşturun ve uç noktanıza ve anahtarınıza sahip bir istemci örneği oluşturmak için aşağıdaki kodu ekleyin.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=authenticate-client) [istemciyi doğrulıyorum](?success=authenticate-client#read-printed-and-handwritten-text)



## <a name="read-printed-and-handwritten-text"></a>Yazdırılmış ve el yazısı metin oku

OCR hizmeti görüntüdeki görünür metni okuyabilir ve bunu bir karakter akışına dönüştürebilir. Bu bölümdeki kod, `RecognizeTextReadAPIRemoteImage` görüntüde yazdırılmış veya el yazısı metinleri algılamak ve ayıklamak için istemci nesnesini kullanan bir işlevi tanımlar.

İşlevinizdeki örnek resim başvurusunu ve işlev çağrısını ekleyin `main` .

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!TIP]
> Ayrıca, yerel görüntüden metin de ayıklayabilirsiniz. **BatchReadFileInStream** gibi [baseclient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) yöntemlerine bakın. Ya da, yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) 'daki örnek koda bakın.

### <a name="call-the-read-api"></a>Okuma API 'sini çağırma

Metni okumak için yeni işlevi tanımlayın, `RecognizeTextReadAPIRemoteImage` . Belirtilen görüntü için **Batchreadfile** yöntemini çağıran aşağıdaki kodu ekleyin. Bu yöntem bir işlem KIMLIĞI döndürür ve görüntünün içeriğini okumak için zaman uyumsuz bir işlem başlatır.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>Okuma sonuçları al

Sonra, **Batchreadfile** çağrısından döndürülen işlem kimliğini alın ve bunu işlem sonuçları için hizmeti sorgulamak üzere **Getreadoperationresult** yöntemiyle birlikte kullanın. Aşağıdaki kod, sonuçlar döndürülünceye kadar işlemi tek saniyelik aralıklarla denetler. Daha sonra ayıklanan metin verilerini konsola yazdırır.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>Okuma sonuçlarını görüntüle

Alınan metin verilerini ayrıştırmak ve göstermek için aşağıdaki kodu ekleyin ve işlev tanımını sona erdirin.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=read-printed-handwritten-text) [metni okudum](?success=read-printed-handwritten-text#run-the-application)

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

Bu hızlı başlangıçta, OCR istemci kitaplığını yüklemeyi ve okuma API 'sini kullanmayı öğrendiniz. Ardından, okuma API 'SI özellikleri hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
>[Okuma API 'sini çağırma](../../Vision-API-How-to-Topics/call-read-api.md)

* [OCR genel bakış](../../overview-ocr.md)
* Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go)' da bulunabilir.
