---
title: 'Hızlı başlangıç: .NET için optik karakter tanıma istemci kitaplığı'
description: Bu hızlı başlangıçta, .NET için optik karakter tanıma istemci kitaplığı ile çalışmaya başlayın.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 538b3ce5a268464b9f014dd00b924875824cab3b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327265"
---
<a name="HOLTop"></a>

Bir görüntüden yazdırılmış ve el yazısı metin okumak için OCR istemci kitaplığını kullanın.

[Başvuru belgeleri](/dotnet/api/overview/azure/cognitiveservices/client/computervision)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision)  |  [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/)  |  [Örnekler](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/)
* [Visual STUDIO IDE](https://visualstudio.microsoft.com/vs/) veya [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)'un geçerli sürümü.
* Azure aboneliğiniz olduktan sonra, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" "  target="_blank"> </a> anahtarınızı ve uç noktanızı almak için Azure Portal bir Görüntü İşleme kaynağı oluşturun görüntü işleme bir kaynak oluşturun. Dağıtıldıktan sonra **Kaynağa Git ' e** tıklayın.
    * Uygulamanızı Görüntü İşleme hizmetine bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
    * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-c-application"></a>Yeni bir C# uygulaması oluşturma

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Visual Studio 'yu kullanarak yeni bir .NET Core uygulaması oluşturun. 

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler 

Yeni bir proje oluşturduktan sonra, **Çözüm Gezgini** proje çözümüne sağ tıklayıp **NuGet Paketlerini Yönet**' i seçerek istemci kitaplığını yükleyebilirsiniz. Açılan paket yöneticisinde, Seç ' i seçin, **ön sürümü dahil** **et ' i** işaretleyin ve arama yapın `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` . Sürüm `7.0.0` ' ü ve ardından **öğesini seçin**. 

#### <a name="cli"></a>[CLI](#tab/cli)

Konsol penceresinde (cmd, PowerShell veya Bash gibi), `dotnet new` adıyla yeni bir konsol uygulaması oluşturmak için komutunu kullanın `computer-vision-quickstart` . Bu komut, tek bir kaynak dosyası olan basit bir "Merhaba Dünya" C# projesi oluşturur: *program. cs*.

```console
dotnet new console -n computer-vision-quickstart
```

Dizininizi yeni oluşturulan uygulama klasörüyle değiştirin. Uygulamayı ile oluşturabilirsiniz:

```console
dotnet build
```

Derleme çıktısı hiçbir uyarı veya hata içermemelidir. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler

Uygulama dizini içinde, aşağıdaki komutla .NET için Görüntü İşleme istemci Kitaplığı ' nı yüklemelisiniz:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 7.0.0
```

---

> [!TIP]
> Tüm hızlı başlangıç kodu dosyasını aynı anda görüntülemek mi istiyorsunuz? Bu hızlı başlangıçta kod örneklerini içeren [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs)'da bulabilirsiniz.

Proje dizininden, tercih ettiğiniz düzenleyicide veya IDE 'de *program. cs* dosyasını açın.

### <a name="find-the-subscription-key-and-endpoint"></a>Abonelik anahtarını ve uç noktayı bulma

Azure portala gidin. **Önkoşullar** bölümünde oluşturduğunuz görüntü işleme kaynak başarıyla dağıtılırsa, **sonraki adımlar** altında **Kaynağa Git** düğmesine tıklayın. Kaynak **yönetimi** altında, kaynağın **anahtar ve uç** nokta sayfasında abonelik anahtarınızı ve uç noktanızı bulabilirsiniz. 

Uygulamanın **Program** sınıfında görüntü işleme abonelik anahtarınız ve uç noktanız için değişkenler oluşturun. Abonelik anahtarınızı ve uç noktanızı, belirtilen yerde aşağıdaki koda yapıştırın. Görüntü İşleme uç noktanız formu vardır `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using_and_vars)]

> [!IMPORTANT]
> İşiniz bittiğinde kodunuzda abonelik anahtarını kaldırmayı unutmayın ve hiçbir zaman herkese açık bir şekilde nakletmeyin. Üretim için, kimlik bilgilerinizi depolamak ve bunlara erişmek için güvenli bir yol kullanmayı düşünün. Örneğin, [Azure Anahtar Kasası](../../../../key-vault/general/overview.md).

Uygulamanın `Main` yönteminde, bu hızlı başlangıçta kullanılan yöntemlere çağrılar ekleyin. Bunları daha sonra oluşturacaksınız.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extracttextinmain)]

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=set-up-client) [istemciyi ayarladım](?success=set-up-client#object-model)

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler, OCR .NET SDK 'sının bazı önemli özelliklerini işler.

|Ad|Açıklama|
|---|---|
| [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Bu sınıf tüm Görüntü İşleme işlevleri için gereklidir. Bunu Abonelik bilgileriniz ile birlikte başlatır ve birçok görüntü işlemini yapmak için kullanırsınız.|
|[ComputerVisionClientExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions)| Bu sınıf, **ComputerVisionClient** için ek yöntemler içerir.|

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, .NET için OCR istemci kitaplığı ile aşağıdaki görevlerin nasıl yapılacağını gösterir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Yazdırılmış ve el yazısı metin oku](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

**Program** sınıfındaki yeni bir yöntemde, uç nokta ve abonelik anahtarınızı içeren bir istemci örneği oluşturun. Abonelik anahtarınızla bir **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials)** nesnesi oluşturun ve bir **[ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient)** nesnesi oluşturmak için bunu uç noktanızla birlikte kullanın.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=authenticate-client) [istemciyi doğrulıyorum](?success=authenticate-client#read-printed-and-handwritten-text)

## <a name="read-printed-and-handwritten-text"></a>Yazdırılmış ve el yazısı metin oku

OCR hizmeti görüntüdeki görünür metni okuyabilir ve bunu bir karakter akışına dönüştürebilir. Metin tanıma hakkında daha fazla bilgi için bkz. [optik karakter tanıma (OCR)](../../overview-ocr.md) genel bakış. Bu bölümdeki kod, [okuma 3,0 için en son görüntü işleme SDK sürümünü](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) kullanır ve bir yöntemi tanımlar, bu, `BatchReadFileUrl` görüntüdeki metni algılamak ve ayıklamak için istemci nesnesini kullanır.

> [!TIP]
> Ayrıca, yerel görüntüden metin de ayıklayabilirsiniz. **Readınstreamasync** gibi [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) yöntemlerine bakın. Ya da, yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) 'daki örnek koda bakın.

### <a name="set-up-test-image"></a>Test görüntüsünü ayarla

**Program** sınıfınıza, metin ayıklamak ISTEDIĞINIZ görüntünün URL 'sine bir başvuru kaydedin. Bu kod parçacığı hem yazdırılmış hem de el yazısı metin için örnek görüntüler içerir.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readtext_url)]

### <a name="call-the-read-api"></a>Okuma API 'sini çağırma

Metni okumak için yeni bir yöntem tanımlayın. Verilen görüntü için **ReadAsync** yöntemini çağıran aşağıdaki kodu ekleyin. Bu işlem KIMLIĞI döndürür ve görüntünün içeriğini okumak için zaman uyumsuz bir işlem başlatır.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_1)]

### <a name="get-read-results"></a>Okuma sonuçları al

Ardından, **ReadAsync** çağrısından döndürülen işlem kimliğini alın ve bunu işlem sonuçları için hizmeti sorgulamak üzere kullanın. Aşağıdaki kod, sonuçlar döndürülünceye kadar işlemi denetler. Daha sonra ayıklanan metin verilerini konsola yazdırır.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_2)]

### <a name="display-read-results"></a>Okuma sonuçlarını görüntüle

Alınan metin verilerini ayrıştırmak ve göstermek için aşağıdaki kodu ekleyin ve yöntem tanımını sona erdirin.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_3)]

## <a name="run-the-application"></a>Uygulamayı çalıştırma

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

IDE penceresinin en üstündeki **Hata Ayıkla** düğmesine tıklayarak uygulamayı çalıştırın.

#### <a name="cli"></a>[CLI](#tab/cli)

Uygulamayı komut ile uygulama dizininizden çalıştırın `dotnet run` .

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, OCR istemci kitaplığını yüklemeyi ve okuma API 'sini kullanmayı öğrendiniz. Ardından, okuma API 'SI özellikleri hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
>[Okuma API 'sini çağırma](../../Vision-API-How-to-Topics/call-read-api.md)

* [OCR genel bakış](../../overview-ocr.md)
* Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs)' da bulunabilir.
