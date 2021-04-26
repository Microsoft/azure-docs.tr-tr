---
title: 'Hızlı başlangıç: Node.js için optik karakter tanıma istemci kitaplığı'
description: Bu hızlı başlangıç ile Node.js için optik karakter tanıma istemci kitaplığıyla çalışmaya başlama
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: cb4679152740b73d6bb9cf7288fcaa811b6d6141
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073390"
---
<a name="HOLTop"></a>

Okuma API 'SI ile yazdırılmış ve el yazısı metin okumak için optik karakter tanıma istemci kitaplığını kullanın.

[Başvuru belgeleri](/javascript/api/@azure/cognitiveservices-computervision/)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision)  |  [Paket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision)  |  [Örnekler](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/)
* [Node.js](https://nodejs.org/) geçerli sürümü
* Azure aboneliğiniz olduktan sonra, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" "  target="_blank"> </a> anahtarınızı ve uç noktanızı almak için Azure Portal bir Görüntü İşleme kaynağı oluşturun görüntü işleme bir kaynak oluşturun. Dağıtıldıktan sonra **Kaynağa Git ' e** tıklayın.
    * Uygulamanızı Görüntü İşleme hizmetine bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
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

`ms-rest-azure`Ve `@azure/cognitiveservices-computervision` NPM paketini yüklerken:

```console
npm install @azure/cognitiveservices-computervision
```

Zaman uyumsuz modülünü de yükler:

```console
npm install async
```

Uygulamanızın `package.json` dosyası bağımlılıklarla güncelleştirilir.

> [!TIP]
> Tüm hızlı başlangıç kodu dosyasını aynı anda görüntülemek mi istiyorsunuz? Bu hızlı başlangıçta kod örneklerini içeren [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js)'da bulabilirsiniz.

Yeni bir dosya oluşturun, *index.js* ve bir metin düzenleyicisinde açın.

### <a name="find-the-subscription-key-and-endpoint"></a>Abonelik anahtarını ve uç noktayı bulma

Azure portala gidin. **Önkoşullar** bölümünde oluşturduğunuz görüntü işleme kaynak başarıyla dağıtılırsa, **sonraki adımlar** altında **Kaynağa Git** düğmesine tıklayın. Kaynak **yönetimi** altında, kaynağın **anahtar ve uç** nokta sayfasında abonelik anahtarınızı ve uç noktanızı bulabilirsiniz. 

Görüntü İşleme abonelik anahtarınız ve uç noktanız için değişkenler oluşturun. Abonelik anahtarınızı ve uç noktanızı, belirtilen yerde aşağıdaki koda yapıştırın. Görüntü İşleme uç noktanız formu vardır `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> İşiniz bittiğinde kodunuzda abonelik anahtarını kaldırmayı unutmayın ve hiçbir zaman herkese açık bir şekilde nakletmeyin. Üretim için, kimlik bilgilerinizi depolamak ve bunlara erişmek için güvenli bir yol kullanmayı düşünün. Örneğin, [Azure Anahtar Kasası](../../../../key-vault/general/overview.md).

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=set-up-client) [istemciyi ayarladım](?success=set-up-client#object-model)

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler, OCR Node.js SDK 'sının bazı önemli özelliklerini işler.

|Ad|Açıklama|
|---|---|
| [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) | Bu sınıf tüm Görüntü İşleme işlevleri için gereklidir. Bunu Abonelik bilgileriniz ile birlikte başlatır ve birçok görüntü işlemini yapmak için kullanırsınız.|

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, Node.js için OCR istemci kitaplığı ile aşağıdaki görevlerin nasıl yapılacağını gösterir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Yazdırılmış ve el yazısı metin oku](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama


Uç noktanız ve anahtarınızla bir istemci örneği oluşturun. Anahtar ve uç noktanızla bir [Apikeycredentials](/python/api/msrest/msrest.authentication.apikeycredentials) nesnesi oluşturun ve bunu bir [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) nesnesi oluşturmak için kullanın.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

Ardından, bir işlev tanımlayın `computerVision` ve birincil işlev ve geri çağırma işlevi ile zaman uyumsuz bir seri bildirin. Hızlı başlangıç kodunuzu birincil işleve ekleyecek ve `computerVision` betiğin en altında çağıracaksınız. Bu hızlı başlangıçta kodun geri kalanı işlevin içinde gider `computerVision` .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=authenticate-client) [istemciyi doğrulıyorum](?success=authenticate-client#read-printed-and-handwritten-text)



## <a name="read-printed-and-handwritten-text"></a>Yazdırılmış ve el yazısı metin oku

OCR hizmeti görüntüdeki görünür metni ayıklayabilir ve bir karakter akışına dönüştürebilir. Bu örnek, okuma işlemlerini kullanır.

### <a name="set-up-test-images"></a>Test görüntülerini ayarlama

Metin ayıklamak istediğiniz görüntülerin URL 'SI başvurusunu kaydedin.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

> [!NOTE]
> Ayrıca, yerel görüntüden metin okuyabilirsiniz. **ReadInStream** gibi [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) yöntemlerine bakın. Ya da, yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) 'daki örnek koda bakın.

### <a name="call-the-read-api"></a>Okuma API 'sini çağırma

Okuma çağrısı durum değerlerini göstermek için işlevinizde aşağıdaki alanları tanımlayın.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_statuses)]

Aşağıdaki kodu ekleyerek `readTextFromURL` verilen görüntüler için işlevini çağırır.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

İşlevi tanımlayın `readTextFromURL` . Bu, bir işlem KIMLIĞI döndüren ve görüntünün içeriğini okumak için zaman uyumsuz bir işlem Başlatan istemci nesnesindeki **Read** yöntemini çağırır. Ardından, sonuçlar döndürülünceye kadar işlem durumunu denetlemek için işlem KIMLIğINI kullanır. Bunlar ayıklanan sonuçları döndürür.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

Ardından, `printRecText` okuma işlemlerinin sonuçlarını konsola yazdıran yardımcı işlevini tanımlayın.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=read-printed-handwritten-text) [metni okudum](?success=read-printed-handwritten-text#run-the-application)

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı `node` hızlı başlangıç dosyanızdaki komutla çalıştırın.

```console
node index.js
```

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=run-the-application) [uygulamayı çalıştırdım](?success=run-the-application#clean-up-resources)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=clean-up-resources) [kaynakları temizm](?success=clean-up-resources#next-steps)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, OCR istemci kitaplığını yüklemeyi ve okuma API 'sini kullanmayı öğrendiniz. Ardından, okuma API 'SI özellikleri hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
>[Okuma API 'sini çağırma](../../Vision-API-How-to-Topics/call-read-api.md)

* [OCR genel bakış](../../overview-ocr.md)
* Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js)' da bulunabilir.
