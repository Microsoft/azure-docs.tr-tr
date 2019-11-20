---
title: 'Hızlı başlangıç: .NET için Content Moderator istemci kitaplığı | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: .NET için Content Moderator istemci kitaplığı ile çalışmaya başlayın.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: pafarley
ms.openlocfilehash: 5b287253e471ced244db3169be9f7d8f14e5862e
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73045268"
---
# <a name="quickstart-content-moderator-client-library-for-net"></a>Hızlı başlangıç: .NET için Content Moderator istemci kitaplığı

.NET için Content Moderator istemci kitaplığı ile çalışmaya başlayın. Paketi yüklemek için bu adımları izleyin ve temel görevler için örnek kodu deneyin. Content Moderator, zararlı olabilecek, riskli veya başka türlü istenmeyen malzemeler için metin, resim ve video içeriğini denetleyen bir bilişsel hizmettir. Hizmet, böyle bir öğeyle karşılaştığında içeriğe uygun etiketler (bayraklar) ekler. Uygulamanız daha sonra bayraklı içeriği idare edebilir veya kullanıcılara yönelik hedeflenen ortamı koruyabilir.

.NET için Content Moderator istemci kitaplığı 'nı kullanın:

* [Orta metin](#moderate-text)
* [Orta görüntüler](#moderate-images)
* [İnceleme oluştur](#create-a-review)

[Başvuru belgeleri](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-dotnet)  | [kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator)  | [paketi (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)  | [örnekleri](https://docs.microsoft.com/azure/cognitive-services/content-moderator/samples-dotnet)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
* [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)'un geçerli sürümü.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-content-moderator-azure-resource"></a>Content Moderator Azure kaynağı oluşturma

Azure bilişsel hizmetler, abone olduğunuz Azure kaynakları tarafından temsil edilir. Yerel makinenizde [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) veya [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) kullanarak Content moderator için bir kaynak oluşturun. Aşağıdakileri de yapabilirsiniz:

* Ücretsiz olarak yedi gün boyunca geçerli bir [deneme anahtarı](https://azure.microsoft.com/try/cognitive-services/#decision) alın. Kaydolduktan sonra [Azure Web sitesinde](https://azure.microsoft.com/try/cognitive-services/my-apis/)mevcut olacaktır.  
* [Azure Portal](https://portal.azure.com/) kaynağı görüntüleyin

Deneme aboneliğinizden veya kaynağından bir anahtar aldıktan sonra, sırasıyla `CONTENT_MODERATOR_SUBSCRIPTION_KEY` ve `CONTENT_MODERATOR_ENDPOINT`adlı anahtar ve uç nokta URL 'SI için [ortam değişkenleri oluşturun](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) .

### <a name="create-a-new-c-application"></a>Yeni C# bir uygulama oluşturun

Tercih ettiğiniz metin düzenleyicisinde veya IDE 'de yeni bir .NET Core uygulaması oluşturun. 

Konsol penceresinde (cmd, PowerShell veya Bash gibi), `content-moderator-quickstart`adlı yeni bir konsol uygulaması oluşturmak için `dotnet new` komutunu kullanın. Bu komut, tek bir kaynak dosyası olan C# basit bir "Merhaba Dünya" projesi oluşturur: *program.cs*.

```console
dotnet new console -n content-moderator-quickstart
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

Proje dizininden, *program.cs* dosyasını tercih ettiğiniz DÜZENLEYICIDE veya IDE 'de açın. Aşağıdaki `using` deyimlerini ekleyin:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_using)]

**Program** sınıfında, kaynak uç noktası konumu ve anahtarı ortam değişkenleri olarak bir değişken oluşturun.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_creds)]

> [!NOTE]
> Uygulamayı başlattıktan sonra ortam değişkenlerini oluşturduysanız, değişkenlere erişmek için onu çalıştıran düzenleyiciyi, IDE 'yi veya kabuğu kapatıp yeniden açmanız gerekir.

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler

Uygulama dizini içinde, aşağıdaki komutla .NET için Content Moderator istemci Kitaplığı ' nı yüklemelisiniz:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

Visual Studio IDE kullanıyorsanız, istemci kitaplığı indirilebilir bir NuGet paketi olarak kullanılabilir.

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar, Content Moderator .NET SDK 'nın bazı önemli özelliklerini işler.

|Adı|Açıklama|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient?view=azure-dotnet)|Bu sınıf tüm Content Moderator işlevleri için gereklidir. Bunu Abonelik bilgileriniz ile birlikte başlatır ve diğer sınıfların örneklerini oluşturmak için kullanırsınız.|
|[Imagedenetlemesi](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet)|Bu sınıf yetişkinlere yönelik içerik, kişisel bilgiler veya insan yüzeyleri için görüntüleri analiz etmek üzere işlevsellik sağlar.|
|[Textdenetlemesi](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation?view=azure-dotnet)|Bu sınıf, dil, küfür, hatalar ve kişisel bilgiler için metin çözümleme işlevlerini sağlar.|
|[İnceleyen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet)|Bu sınıf, iş oluşturma, özel iş akışları ve insan incelemeleri için yöntemler de dahil olmak üzere, gözden geçirme API 'lerinin işlevlerini sağlar.|

## <a name="code-examples"></a>Kod örnekleri


Bu kod parçacıkları, .NET için Content Moderator istemci kitaplığı ile aşağıdaki görevlerin nasıl yapılacağını gösterir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Orta metin](#moderate-text)
* [Orta görüntüler](#moderate-images)
* [İnceleme oluştur](#create-a-review)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

Yeni bir yöntemde, uç nokta ve anahtarınızla istemci nesneleri oluşturun. Her senaryo için farklı bir istemciye ihtiyacınız yoktur, ancak kodunuzu düzenli tutmaya yardımcı olabilir.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_client)]

## <a name="moderate-text"></a>Orta metin

Aşağıdaki kod, bir metin gövdesini çözümlemek ve sonuçları konsola yazdırmak için bir Content Moderator istemcisi kullanır. **Program** sınıfınızın kökünde, giriş ve çıkış dosyalarını tanımlayın:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_text_vars)]

Sonra projenizin kökünde bir *textfile. txt* dosyası ekleyin. Bu dosyaya kendi metninizi ekleyin veya aşağıdaki örnek metni kullanın:

```
Is this a grabage email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
Crap is the profanity here. Is this information PII? phone 3144444444
```

Aşağıdaki yöntem çağrısını `Main` yöntemine ekleyin:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod_call)]

Ardından, **Program** sınıfınıza bir yerde metin denetleme yöntemi tanımlayın:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Orta görüntüler

Aşağıdaki kod, Yetişkin ve kcy içeriği için uzak görüntüleri çözümlemek üzere bir [ımagemoderasyonu](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet) nesnesiyle birlikte bir Content moderator istemcisi kullanır.

> [!NOTE]
> Yerel görüntünün içeriğini de çözümleyebilirsiniz. Yerel görüntülerle çalışan Yöntemler ve işlemler için [başvuru belgelerine](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) bakın.

### <a name="get-sample-images"></a>Örnek görüntüleri al

Giriş ve çıkış dosyalarınızı tanımlayın:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_image_vars)]

Ardından, projenizin kökündeki *ımagefiles. txt*giriş dosyasını oluşturun. Bu dosyada, her satırda bir URL&mdash;analiz edilecek görüntülerin URL 'Lerini eklersiniz. Aşağıdaki örnek görüntüleri kullanabilirsiniz:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

Giriş ve çıkış dosyalarınızı `Main` yönteminde aşağıdaki yöntem çağrısına geçirin. Bu yöntemi sonraki bir adımda tanımlayacaksınız.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod_call)]

### <a name="define-helper-class"></a>Yardımcı sınıfı tanımla

**Program** sınıfına aşağıdaki sınıf tanımını ekleyin. Bu iç sınıf, görüntü denetleme sonuçlarını işleyecek.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>Görüntü denetleme yöntemini tanımlama

Aşağıdaki yöntem bir metin dosyasındaki görüntü URL 'Lerinde yinelenir, bir **EvaluationData** örneği oluşturur ve yetişkinlere/rampaiçeriği, metin ve insan yüzeyleri için görüntüyü analiz eder. Ardından, son **EvaluationData** örneğini bir listeye ekler ve döndürülen verilerin tüm listesini konsola yazar.

#### <a name="iterate-through-image-urls"></a>Görüntü URL 'Leri üzerinden yineleme

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>İçeriği analiz etme

Ekranları Content Moderator görüntü öznitelikleri hakkında daha fazla bilgi için bkz. [görüntü denetleme kavramları](./image-moderation-api.md) Kılavuzu.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Dosyaya denetleme sonuçları yaz

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>İnceleme oluştur

Content Moderator .NET SDK 'Yı kullanarak, insan moderatör tarafından gözden geçirebilmesi için içeriği [Gözden geçirme aracında](https://contentmoderator.cognitive.microsoft.com) akışını sağlayabilirsiniz. Inceleme aracı hakkında daha fazla bilgi edinmek için bkz. [İnceleme aracı kavramsal Kılavuzu](./review-tool-user-guide/human-in-the-loop.md).

Bu bölümdeki Yöntem, gözden geçirme oluşturmak, KIMLIĞINI almak ve gözden geçirme aracının Web portalı aracılığıyla insan girişini aldıktan sonra ayrıntılarını denetlemek için [İncelemeleri](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet) sınıfını kullanır. Tüm bu bilgileri bir çıktı metin dosyasında günlüğe kaydeder. `Main` yönteminden yöntemi çağırın:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_call)]

### <a name="get-sample-images"></a>Örnek görüntüleri al

**Program** sınıfınızın kökünde aşağıdaki diziyi bildirin. Bu değişken, gözden geçirmeyi oluşturmak için kullanılacak örnek bir görüntüye başvurur.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>İnceleme kimlik bilgilerini al

[İnceleme aracında](https://contentmoderator.cognitive.microsoft.com) oturum açın ve takımınızın adını alın. Ardından, **Program** sınıfındaki uygun değişkene atayın. İsteğe bağlı olarak, gözden geçirme etkinliği üzerinde güncelleştirmeleri almak için bir geri çağırma uç noktası ayarlayabilirsiniz.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Yardımcı sınıfı tanımla

Aşağıdaki sınıf tanımını **Program** sınıfınız içine ekleyin. Bu sınıf, gözden geçirme aracına gönderilen tek bir gözden geçirme örneğini temsil etmek için kullanılacaktır.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Helper metodunu tanımla

**Program** sınıfına aşağıdaki yöntemi ekleyin. Bu yöntem, gözden geçirme sorgularının sonuçlarını çıktı metin dosyasına yazar.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>İnceleme oluşturma yöntemini tanımlayın

Şimdi, gözden geçirme oluşturma ve sorgulama işleyecek yöntemi tanımlamaya hazır olursunuz. Yeni bir yöntem, **Createreviews**ekleyin ve aşağıdaki yerel değişkenleri tanımlayın.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>Inceleme aracında incelemeler gönderi

Ardından, belirtilen örnek görüntüler arasında yinelemek için aşağıdaki kodu ekleyin, meta verileri ekleyin ve tek bir toplu işte Inceleme aracına gönderin. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_create)]

API çağrısından döndürülen nesne, karşıya yüklenen her resim için benzersiz KIMLIK değerleri içerir. Aşağıdaki kod, bu kimlikleri ayrıştırır ve sonra toplu işteki her bir görüntünün durumu için Content Moderator sorgulamak üzere kullanır.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>İnceleme ayrıntılarını al

Aşağıdaki kod programın kullanıcı girişini beklemesine neden olur. Çalışma zamanında bu adıma gittiğinizde, [Gözden geçirme aracına](https://contentmoderator.cognitive.microsoft.com) kendiniz gidebilir, örnek görüntünün karşıya yüklendiğini ve bununla etkileşime geçebilirsiniz. Bir gözden geçirime nasıl etkileşim kuracağınızı öğrenmek için bkz. [nasıl yapılır Kılavuzu](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images). İşiniz bittiğinde programa devam etmek ve İnceleme işleminin sonuçlarını almak için herhangi bir tuşa basabilirsiniz.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_results)]

Bu senaryoda bir geri çağırma uç noktası kullandıysanız, bu biçimde bir olay almalıdır:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı `dotnet run` komutuyla uygulama dizininizden çalıştırın.

```dotnet
dotnet run 
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Sonraki adımlar


Bu hızlı başlangıçta, Content Moderator .NET kitaplığını kullanarak denetleme görevlerini nasıl kullanacağınızı öğrendiniz. Daha sonra, bir kavramsal kılavuz okuyarak görüntülerin veya diğer ortamların denetimi hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Görüntü denetleme kavramları](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Azure Content Moderator nedir?](./overview.md)
* Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ContentModerator/Program.cs)' da bulunabilir.
