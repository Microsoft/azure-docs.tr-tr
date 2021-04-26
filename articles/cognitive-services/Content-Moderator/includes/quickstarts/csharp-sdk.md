---
title: Content Moderator .NET istemci kitaplığı hızlı başlangıç
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, .NET için Azure Content Moderator istemci kitaplığı ile çalışmaya başlama hakkında bilgi edinin. Yönetmeliklerle uyum sağlamak veya kullanıcılarınız için amaçlanan ortamı sürdürmek üzere uygulamanıza içerik filtreleme yazılımı oluşturun.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 09/15/2020
ms.author: pafarley
ms.custom: devx-track-dotnet, cog-serv-seo-aug-2020
ms.openlocfilehash: 573778316802b6e445b4c9d78576a8813af514a6
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726147"
---
.NET için Azure Content Moderator istemci kitaplığı 'nı kullanmaya başlayın. NuGet paketini yüklemek için bu adımları izleyin ve temel görevler için örnek kodu deneyin. 

Content Moderator, rahatsız edici, riskli veya başka türlü istenmeyen içerikleri işlemenize imkan tanıyan bir AI hizmetidir. Metin, resim ve videoları taramak ve içerik bayraklarını otomatik olarak uygulamak için AI destekli içerik denetleme hizmetini kullanın. Ardından, bir insan gözden geçirenler ekibi için çevrimiçi bir moderatör ortamı olan gözden geçirme aracı ile uygulamanızı tümleştirin. Yönetmeliklerle uyum sağlamak veya kullanıcılarınız için amaçlanan ortamı sürdürmek üzere uygulamanıza içerik filtreleme yazılımı oluşturun.

.NET için Content Moderator istemci kitaplığı 'nı kullanın:

* Orta metin
* Orta görüntüler
* İnceleme oluştur

[Başvuru belgeleri](/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator)  |  [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)  |  [Örnekler](../../samples-dotnet.md)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/)
* [Visual STUDIO IDE](https://visualstudio.microsoft.com/vs/) veya [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)'un geçerli sürümü.
* Azure aboneliğiniz olduktan sonra, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title=" "  target="_blank"> </a> anahtarınızı ve uç noktanızı almak için Azure Portal bir Content Moderator kaynağı oluşturun Content moderator bir kaynak oluşturun. Dağıtım için bekleyin ve **Kaynağa Git** düğmesine tıklayın.
    * Uygulamanızı Content Moderator bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
    * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-c-application"></a>Yeni bir C# uygulaması oluşturma

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Visual Studio 'yu kullanarak yeni bir .NET Core uygulaması oluşturun. 

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler 

Yeni bir proje oluşturduktan sonra, **Çözüm Gezgini** proje çözümüne sağ tıklayıp **NuGet Paketlerini Yönet**' i seçerek istemci kitaplığını yükleyebilirsiniz. Açılan paket yöneticisinde, Seç ' i seçin, **ön sürümü dahil** **et ' i** işaretleyin ve arama yapın `Microsoft.Azure.CognitiveServices.ContentModerator` . Sürüm `2.0.0` ' ü ve ardından **öğesini seçin**. 

#### <a name="cli"></a>[CLI](#tab/cli)

Konsol penceresinde (cmd, PowerShell veya Bash gibi), `dotnet new` adıyla yeni bir konsol uygulaması oluşturmak için komutunu kullanın `content-moderator-quickstart` . Bu komut, tek bir kaynak dosyası olan basit bir "Merhaba Dünya" C# projesi oluşturur: *program. cs*.

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

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler 

Uygulama dizini içinde, aşağıdaki komutla .NET için Content Moderator istemci Kitaplığı ' nı yüklemelisiniz:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

---

> [!TIP]
> Tüm hızlı başlangıç kodu dosyasını aynı anda görüntülemek mi istiyorsunuz? Bu hızlı başlangıçta kod örneklerini içeren [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs)'da bulabilirsiniz.

Proje dizininden, tercih ettiğiniz düzenleyicide veya IDE 'de *program. cs* dosyasını açın. Aşağıdaki `using` deyimlerini ekleyin:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_using)]

**Program** sınıfında, kaynağınızın anahtarı ve uç noktası için değişkenler oluşturun.

> [!IMPORTANT]
> Azure portala gidin. **Önkoşullar** bölümünde oluşturduğunuz Content moderator kaynak başarıyla dağıtılırsa, **sonraki adımlar** altında **Kaynağa Git** düğmesine tıklayın. Anahtar ve uç noktanızı kaynağın **anahtar ve uç nokta** sayfasında, **kaynak yönetimi** altında bulabilirsiniz. 
>
> İşiniz bittiğinde kodu koddan kaldırmayı unutmayın ve hiçbir zaman herkese açık bir şekilde nakletmeyin. Üretim için, kimlik bilgilerinizi depolamak ve bunlara erişmek için güvenli bir yol kullanmayı düşünün. Daha fazla bilgi için bilişsel Hizmetler [güvenlik](../../../cognitive-services-security.md) makalesine bakın.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_creds)]


Uygulamanın `main()` yönteminde, bu hızlı başlangıçta kullanılan yöntemlere çağrılar ekleyin. Bunları daha sonra oluşturacaksınız.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_call)]


## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar Content Moderator .NET istemci kitaplığının bazı önemli özelliklerini işler.

|Ad|Açıklama|
|---|---|
|[ContentModeratorClient](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient)|Bu sınıf tüm Content Moderator işlevleri için gereklidir. Bunu Abonelik bilgileriniz ile birlikte başlatır ve diğer sınıfların örneklerini oluşturmak için kullanırsınız.|
|[Imagedenetlemesi](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation)|Bu sınıf yetişkinlere yönelik içerik, kişisel bilgiler veya insan yüzeyleri için görüntüleri analiz etmek üzere işlevsellik sağlar.|
|[Textdenetlemesi](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation)|Bu sınıf, dil, küfür, hatalar ve kişisel bilgiler için metin çözümleme işlevlerini sağlar.|
|[İncelemeler](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews)|Bu sınıf, iş oluşturma, özel iş akışları ve insan incelemeleri için yöntemler de dahil olmak üzere, gözden geçirme API 'lerinin işlevlerini sağlar.|

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, .NET için Content Moderator istemci kitaplığı ile aşağıdaki görevlerin nasıl yapılacağını gösterir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Orta metin](#moderate-text)
* [Orta görüntüler](#moderate-images)
* [İnceleme oluştur](#create-a-review)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

Yeni bir yöntemde, uç nokta ve anahtarınızla istemci nesneleri oluşturun.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_auth)]

## <a name="moderate-text"></a>Orta metin

Aşağıdaki kod, bir metin gövdesini çözümlemek ve sonuçları konsola yazdırmak için bir Content Moderator istemcisi kullanır. **Program** sınıfınızın kökünde, giriş ve çıkış dosyalarını tanımlayın:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_text_vars)]

Ardından, projenizin kökünde bir *TextFile.txt* dosyası ekleyin. Bu dosyaya kendi metninizi ekleyin veya aşağıdaki örnek metni kullanın:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```


Ardından, **Program** sınıfınıza bir yerde metin denetleme yöntemi tanımlayın:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Orta görüntüler

Aşağıdaki kod, Yetişkin ve kcy içeriği için uzak görüntüleri çözümlemek üzere bir [ımagemoderasyonu](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation) nesnesiyle birlikte bir Content moderator istemcisi kullanır.

> [!NOTE]
> Yerel görüntünün içeriğini de çözümleyebilirsiniz. Yerel görüntülerle çalışan Yöntemler ve işlemler için [başvuru belgelerine](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) bakın.

### <a name="get-sample-images"></a>Örnek görüntüleri al

Giriş ve çıkış dosyalarınızı **Program** sınıfınızın kökünde tanımlayın:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_image_vars)]

Daha sonra projenizin kökünde *ImageFiles.txt* giriş dosyasını oluşturun. Bu dosyada, &mdash; her satırda BIR URL 'yi çözümlemek için görüntülerin URL 'lerini eklersiniz. Aşağıdaki örnek görüntüleri kullanabilirsiniz:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Yardımcı sınıfı tanımla

**Program** sınıfına aşağıdaki sınıf tanımını ekleyin. Bu iç sınıf, görüntü denetleme sonuçlarını işleyecek.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>Görüntü denetleme yöntemini tanımlama

Aşağıdaki yöntem bir metin dosyasındaki görüntü URL 'Lerinde yinelenir, bir **EvaluationData** örneği oluşturur ve yetişkinlere/rampaiçeriği, metin ve insan yüzeyleri için görüntüyü analiz eder. Ardından, son **EvaluationData** örneğini bir listeye ekler ve döndürülen verilerin tüm listesini konsola yazar.

#### <a name="iterate-through-images"></a>Görüntüler arasında yineleme

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>İçeriği analiz etme

Ekranları Content Moderator görüntü öznitelikleri hakkında daha fazla bilgi için bkz. [görüntü denetleme kavramları](../../image-moderation-api.md) Kılavuzu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Dosyaya denetleme sonuçları yaz

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>İnceleme oluştur

Content Moderator .NET istemci kitaplığını kullanarak, insan yöneticileri tarafından gözden geçirebilmeleri için içeriği [Gözden geçirme aracında](https://contentmoderator.cognitive.microsoft.com) bulabilirsiniz. Inceleme aracı hakkında daha fazla bilgi edinmek için bkz. [İnceleme aracı kavramsal Kılavuzu](../../review-tool-user-guide/human-in-the-loop.md).

Bu bölümdeki Yöntem, gözden geçirme oluşturmak, KIMLIĞINI almak ve gözden geçirme aracının Web portalı aracılığıyla insan girişini aldıktan sonra ayrıntılarını denetlemek için [İncelemeleri](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews) sınıfını kullanır. Tüm bu bilgileri bir çıktı metin dosyasında günlüğe kaydeder. 

### <a name="get-sample-images"></a>Örnek görüntüleri al

**Program** sınıfınızın kökünde aşağıdaki diziyi bildirin. Bu değişken, gözden geçirmeyi oluşturmak için kullanılacak örnek bir görüntüye başvurur.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>İnceleme kimlik bilgilerini al

[İnceleme aracında](https://contentmoderator.cognitive.microsoft.com) oturum açın ve takımınızın adını alın. Ardından, **Program** sınıfındaki uygun değişkene atayın. İsteğe bağlı olarak, gözden geçirme etkinliği üzerinde güncelleştirmeleri almak için bir geri çağırma uç noktası ayarlayabilirsiniz.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Yardımcı sınıfı tanımla

Aşağıdaki sınıf tanımını **Program** sınıfınız içine ekleyin. Bu sınıf, gözden geçirme aracına gönderilen tek bir gözden geçirme örneğini temsil etmek için kullanılacaktır.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Helper metodunu tanımla

**Program** sınıfına aşağıdaki yöntemi ekleyin. Bu yöntem, gözden geçirme sorgularının sonuçlarını çıktı metin dosyasına yazar.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>İnceleme oluşturma yöntemini tanımlayın

Şimdi, gözden geçirme oluşturma ve sorgulama işleyecek yöntemi tanımlamaya hazır olursunuz. Yeni bir yöntem, **Createreviews** ekleyin ve aşağıdaki yerel değişkenleri tanımlayın.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>Inceleme aracında incelemeler gönderi

Ardından, belirtilen örnek görüntüler arasında yinelemek için aşağıdaki kodu ekleyin, meta verileri ekleyin ve tek bir toplu işte Inceleme aracına gönderin. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_create)]

API çağrısından döndürülen nesne, karşıya yüklenen her resim için benzersiz KIMLIK değerleri içerir. Aşağıdaki kod, bu kimlikleri ayrıştırır ve sonra toplu işteki her bir görüntünün durumu için Content Moderator sorgulamak üzere kullanır.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>İnceleme ayrıntılarını al

Aşağıdaki kod programın kullanıcı girişini beklemesine neden olur. Çalışma zamanında bu adıma gittiğinizde, [Gözden geçirme aracına](https://contentmoderator.cognitive.microsoft.com) kendiniz gidebilir, örnek görüntünün karşıya yüklendiğini ve bununla etkileşime geçebilirsiniz. Bir gözden geçirime nasıl etkileşim kuracağınızı öğrenmek için bkz. [nasıl yapılır Kılavuzu](../../review-tool-user-guide/review-moderated-images.md). İşiniz bittiğinde programa devam etmek ve İnceleme işleminin sonuçlarını almak için herhangi bir tuşa basabilirsiniz.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_results)]

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

Bu hızlı başlangıçta, Content Moderator .NET kitaplığını kullanarak denetleme görevlerini nasıl kullanacağınızı öğrendiniz. Daha sonra, bir kavramsal kılavuz okuyarak görüntülerin veya diğer ortamların denetimi hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Görüntü denetleme kavramları](../../image-moderation-api.md)