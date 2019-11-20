---
title: 'Hızlı başlangıç: Language Understanding (LUSıS) .NET için istemci kitaplığı yazma'
titleSuffix: Azure Cognitive Services
description: .NET için LUSıS istemci kitaplığı ile çalışmaya başlayın. Paketi yüklemek için bu adımları izleyin ve temel görevler için örnek kodu deneyin.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 08/30/2019
ms.author: diberry
ms.openlocfilehash: 6af076f585e7fc9afe870acada744ead2d2e9118
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672083"
---
# <a name="quickstart-language-understanding-luis-authoring-client-library-for-net"></a>Hızlı başlangıç: Language Understanding (LUSıS) .NET için istemci kitaplığı yazma

.NET için Language Understanding (LUSıS) yazma istemci kitaplığı ile çalışmaya başlayın. Paketi yüklemek için bu adımları izleyin ve temel görevler için örnek kodu deneyin.  Language Understanding (LUU), bir kullanıcının konuşma, doğal dil metnine, genel anlamı tahmin etmek ve ilgili ayrıntılı bilgileri almak için özel makine öğrenimi zekası uygulamanıza olanak sağlar. 

.NET için Language Understanding (LUSıS) yazma istemci kitaplığını kullanın:

* Uygulama oluşturma
* Amaç, varlık ve örnek ekleme
* Tümcecik listesi gibi özellikler ekleme
* Uygulamayı eğitme ve yayımlama

[Başvuru belgeleri](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) | [yazma paketi (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) | [ C# örnekleri](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs)

## <a name="prerequisites"></a>Ön koşullar

* Language Understanding (LUSıS) portal hesabı- [ücretsiz olarak bir tane oluşturun](https://www.luis.ai)
* [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)'un geçerli sürümü.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="get-your-language-understanding-luis-starter-key"></a>Language Understanding (LUSıS) başlangıç anahtarınızı alın

[Başlangıç anahtarınızı](luis-how-to-azure-subscription.md#starter-key)alın ve `COGNITIVESERVICE_AUTHORING_KEY`adlı anahtar için [bir ortam değişkeni oluşturun](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) .

### <a name="create-a-new-c-application"></a>Yeni C# bir uygulama oluşturun

Tercih ettiğiniz düzenleyicide veya IDE 'de yeni bir .NET Core uygulaması oluşturun. 

1. Konsol penceresinde (cmd, PowerShell veya Bash gibi), `language-understanding-quickstart`adında yeni bir konsol uygulaması oluşturmak için DotNet `new` komutunu kullanın. Bu komut, tek bir kaynak dosyası olan C# basit bir "Merhaba Dünya" projesi oluşturur: `Program.cs`. 

    ```console
    dotnet new console -n language-understanding-quickstart
    ```

1. Dizininizi yeni oluşturulan uygulama klasörüyle değiştirin. 

1. Uygulamayı ile oluşturabilirsiniz:

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


### <a name="install-the-sdk"></a>SDK yükle

Uygulama dizini içinde, aşağıdaki komutla .NET için Language Understanding (LUSıS) yazma istemci Kitaplığı ' nı yüklemelisiniz:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.0.0
```

Visual Studio IDE kullanıyorsanız, istemci kitaplığı indirilebilir bir NuGet paketi olarak kullanılabilir.


## <a name="object-model"></a>Nesne modeli

Language Understanding (LUSıS) yazma istemcisi, yazma anahtarınızı içeren Azure 'da kimlik doğrulayan bir [Luisauthoringclient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) nesnesidir.

İstemci oluşturulduktan sonra aşağıdaki işlevlere erişmek için bu istemciyi kullanın:

* Uygulamalar- [oluşturma](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet), [silme](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet), [Yayımlama](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet)
* Örnek araslar- [Batch ile ekleme](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet), [kimliğe göre silme](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync?view=azure-dotnet) 
* Özellikler- [tümcecik listelerini](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync?view=azure-dotnet) yönetme 
* Model- [amaçları](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) ve varlıkları yönetme
* Desen yönetme [desenleri](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions?view=azure-dotnet)
* Eğitim- [uygulamayı](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync?view=azure-dotnet) ve yoklama [durumunu](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet) eğitme
* [Sürümler](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions?view=azure-dotnet) -kopyalama, dışarı aktarma ve silme ile yönetme


## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, .NET için Language Understanding (LUSıS) yazma istemci kitaplığı ile aşağıdakilerin nasıl yapılacağını göstermektedir:

* [Uygulama oluşturma](#create-a-luis-app)
* [Varlık ekleme](#create-entities-for-the-app)
* [Hedef ekleme](#create-intent-for-the-app)
* [Örnek ekleme](#add-example-utterance-to-intent)
* [Uygulamayı eğitme](#train-the-app)
* [Uygulamayı yayımlama](#publish-a-language-understanding-app)

## <a name="add-the-dependencies"></a>Bağımlılıkları ekleme

Proje dizininden, **program.cs** dosyasını tercih ettiğiniz DÜZENLEYICIDE veya IDE 'de açın. Mevcut `using` kodunu aşağıdaki `using` yönergeleriyle değiştirin:

[!code-csharp[Using statements](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

1. `COGNITIVESERVICES_AUTHORING_KEY`adlı bir ortam değişkeninden çekilen yazma anahtarınızı yönetmek için bir değişken oluşturun. Uygulama başlatıldıktan sonra ortam değişkenini oluşturduysanız, bu değişkeni çalıştıran düzenleyici, IDE veya kabuğun kapatılıp yeniden yüklenmesi gerekir. Yöntemler daha sonra oluşturulacak.

1. Yazma bölgenizin ve uç noktasının tutulacağı değişkenler oluşturun. Yazma anahtarınızın bölgesi, yazmak istediğiniz yere bağlıdır. [Üç yazma bölgesi](luis-reference-regions.md) şunlardır:

    * Avustralya-`australiaeast`
    * Avrupa-`westeurope`
    * ABD ve diğer bölgeler-`westus` (varsayılan)
    
    [!code-csharp[Authorization to resource key](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Variables)]

1. Anahtarınızla bir [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet) nesnesi oluşturun ve bir [Luisauthoringclient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) nesnesi oluşturmak için bunu uç noktanızla birlikte kullanın.

    [!code-csharp[Create LUIS client object](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>LUSıS uygulaması oluşturma

1. Hedefleri, varlıkları ve örnek kullanımları tutan doğal dil işleme (NLP) modelini içeren bir LUO uygulaması oluşturun. 

1. Bir [Applicationcreateobject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet)oluşturun. Ad ve dil kültürü gerekli özelliklerdir. 

1. [Apps. Addadsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) yöntemini çağırın. Yanıt, uygulama KIMLIĞIDIR. 
    
    [!code-csharp[Create a LUIS app](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>Uygulama için amaç oluştur
Bir LUıN uygulamasının modelindeki birincil nesne, amaç ' dır. Amaç, Kullanıcı utterlerinin bir gruplandırması ile _hizalanır._ Bir Kullanıcı soru sorabilir veya bir bot 'tan (veya başka bir istemci uygulamasından) belirli bir _amaçlanan_ yanıtı bulmak için bir ifade oluşturabilir. Bir uçuşmaya örnek olarak, hedef şehirdeki hava durumu hakkında bilgi isteyerek ve müşteri hizmetleri için iletişim bilgilerini soruyor.   

Benzersiz bir amaç adıyla bir [modelcreateobject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet) oluşturun ve ardından uygulama kimliği, sürüm kimliği ve Modelcreateobject 'yi [model. addi](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet) metoduna geçirin. Yanıt, amaç KIMLIĞIDIR.

[!code-csharp[Create intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddIntents)]

## <a name="create-entities-for-the-app"></a>Uygulama için varlık oluşturma

Varlıklar gerekli olmasa da, çoğu uygulama içinde bulunur. Varlık, kullanıcının amaç bilgisini almak için gerekli olan bilgileri Kullanıcı aracılığıyla ayıklar. Her biri kendi veri dönüştürme nesnesi (DTO) modelleriyle birlikte, çok sayıda [önceden oluşturulmuş](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet) ve özel varlık türü vardır.  Uygulamanıza eklenecek ortak önceden oluşturulmuş varlıklar [Number](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), [geographyV2](luis-reference-prebuilt-geographyv2.md), [Ordinal](luis-reference-prebuilt-ordinal.md)içerir. 

Bu **Addentities** yöntemi iki rolle `Location` basit bir varlık, `Class` basit bir varlık, `Flight` bileşik bir varlık oluşturdu ve birden çok önceden oluşturulmuş varlık ekliyor.

Varlıkların bir amaç ile işaretlenmediğini bilmek önemlidir. Bunlar, genellikle birçok amaç için uygulanabilir. Yalnızca belirli bir amaç için örnek Kullanıcı utbotları işaretlenir.

Varlıklar için oluşturma yöntemleri [model](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) sınıfının bir parçasıdır. Her varlık türünün, genellikle [modeller](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet) ad alanındaki `model` sözcüğünü içeren kendi veri dönüştürme nesnesi (DTO) modeli vardır. 

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddEntities)]

## <a name="add-example-utterance-to-intent"></a>Amaca göre örnek ekleme

Bir utterance 'in amacı ve ayıklama varlıklarını tespit etmek için, uygulama için örneklere örnek gerekir. Örneklerin belirli, tek bir amacı hedeflemesi ve tüm özel varlıkları işaretlemesi gerekir. Önceden oluşturulmuş varlıkların işaretlenmesi gerekmez. 

Her örnek için tek bir nesne olan [Examplelabelobject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet) nesnelerinin bir listesini oluşturarak örnek bir parametre ekleyin. Her örnek, varlık adı ve varlık değerinin ad/değer çiftleri sözlüğüne sahip tüm varlıkları işaretlemelidir. Varlık değeri, örnek utterine 'nın metninde göründüğünden tam olarak olmalıdır. 

Uygulama KIMLIĞI, sürüm KIMLIĞI ve örneklerin listesi ile [örnek. BatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ExamplesExtensions_BatchAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_IExamples_System_Guid_System_String_System_Collections_Generic_IList_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_Models_ExampleLabelObject__System_Threading_CancellationToken_) çağrısı yapın. Çağrı, bir sonuç listesiyle yanıt verir. Modele başarıyla eklendiğinden emin olmak için her bir örneğin sonucunu denetlemeniz gerekir. 

[!code-csharp[Add example utterances to a specific intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringBatchAddUtterancesForIntent)]
    
**Createutterance** ve **CreateLabel** yöntemleri, nesneleri oluşturmanıza yardımcı olan yardımcı yöntemlerdir.

## <a name="train-the-app"></a>Uygulamayı eğitme

Model oluşturulduktan sonra, bu modelin bu sürümü için LUıN uygulamasının eğitililmesi gerekir. Eğitilen bir model [kapsayıcıda](luis-container-howto.md)kullanılabilir veya hazırlama veya ürün yuvalarında [yayımlanabilir](luis-how-to-publish-app.md) . 

[Tren. Traınversionasync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) yöntemi, uygulama kimliği ve sürüm kimliği gerektirir. 

Bu hızlı başlangıç gibi çok küçük bir model, çok hızlı bir şekilde eğitecektir. Üretim düzeyinde uygulamalar için, uygulamanın eğitim ne zaman veya ne zaman başarılı olduğunu öğrenmek için [Getstatusasync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) yöntemine bir yoklama çağrısı içermesi gerekir. Yanıt, her bir nesne için ayrı bir durum içeren [Modeltrainingınfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet) nesnelerinin bir listesidir. Eğitimin tamamlandı olarak kabul edilmesi için tüm nesnelerin başarılı olması gerekir.

[!code-csharp[Train the app's version](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringTrainVersion)]

## <a name="publish-a-language-understanding-app"></a>Language Understanding uygulaması yayımlama

[Publishasync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet) YÖNTEMINI kullanarak Luo uygulamasını yayımlayın. Bu, geçerli eğitilen sürümü uç noktada belirtilen yuvaya yayımlar. İstemci uygulamanız bu uç noktayı, amaç ve varlık ayıklama amacıyla Kullanıcı utbotları göndermek için kullanır.

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringPublishVersionAndSlot)]

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulama dizininizde DotNet `run` komutuyla uygulamayı çalıştırın.

```console
dotnet run
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Temizlemek istiyorsanız, LUO uygulamasını silebilirsiniz. Uygulamayı silme, [Apps. DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet) yöntemiyle yapılır. Ayrıca, uygulamayı [Luo portalından](https://www.luis.ai)silebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
>[Tahmin uç noktasını sorgulamak için .NET SDK 'sını kullanma](sdk-csharp-quickstart-query-prediction-endpoint.md)

* [Language Understanding (LUSıS) API nedir?](what-is-luis.md)
* [Yenilikler](whats-new.md)
* [Amaçlar](luis-concept-intent.md), [varlıklar](luis-concept-entity-types.md)ve [örnek](luis-concept-utterance.md)bildirimler ve [önceden oluşturulmuş varlıklar](luis-reference-prebuilt-entities.md)
* Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs)' da bulunabilir.
