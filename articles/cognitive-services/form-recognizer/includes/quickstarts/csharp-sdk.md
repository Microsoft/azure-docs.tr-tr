---
title: 'Hızlı başlangıç: .NET için form tanıyıcı istemci kitaplığı'
description: Özel belgelerinizden anahtar/değer çiftlerini ve tablo verilerini çıkaran bir form işleme uygulaması oluşturmak için .NET için form tanıyıcı istemci Kitaplığı ' nı kullanın.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 04/14/2021
ms.author: lajanuar
ms.openlocfilehash: b7a35046a7f170365974c50a5be99f35cb4a868f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516470"
---
<!-- markdownlint-disable MD024 -->
> [!IMPORTANT]
> Bu makaledeki kod, basitlik nedenlerle zaman uyumlu Yöntemler ve güvenli olmayan kimlik bilgileri depolaması kullanır.

[Başvuru belgeleri](/dotnet/api/overview/azure/ai.formrecognizer-readme)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src)  |  [Paket (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer)  |  [Örnekler](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/)
* [Visual STUDIO IDE](https://visualstudio.microsoft.com/vs/) veya [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)'un geçerli sürümü.
* Eğitim verileri kümesi içeren bir Azure Depolama Blobu. Eğitim veri kümesini birlikte yerleştirmeye yönelik ipuçları ve seçenekler için bkz. [özel bir model için eğitim verileri kümesi oluşturma](../../build-training-data-set.md) . Bu hızlı başlangıçta, [örnek veri kümesinin](https://go.microsoft.com/fwlink/?linkid=2090451) **eğitme** klasörü altındaki dosyaları ( *sample_data.zip* indir ve Ayıkla) kullanabilirsiniz.
* Azure aboneliğiniz olduktan sonra <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" bir form tanıyıcı kaynağı oluşturun "  target="_blank"> </a> Azure Portal anahtarınızı ve uç noktanızı almak Için bir form tanıyıcı kaynağı oluşturun. Dağıtıldıktan sonra **Kaynağa Git ' e** tıklayın.
  * Uygulamanızı form tanıyıcı API 'sine bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
  * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.

## <a name="setting-up"></a>Ayarlanıyor

Konsol penceresinde (cmd, PowerShell veya Bash gibi), `dotnet new` adıyla yeni bir konsol uygulaması oluşturmak için komutunu kullanın `formrecognizer-quickstart` . Bu komut, tek bir kaynak dosyası olan basit bir "Merhaba Dünya" C# projesi oluşturur: *program. cs*.

```console
dotnet new console -n formrecognizer-quickstart
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

Uygulama dizini içinde, aşağıdaki komutla .NET için form tanıyıcı istemci Kitaplığı ' nı yükleyeceksiniz:

#### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/preview)

```console
dotnet add package Azure.AI.FormRecognizer --version 3.1.0-beta.4
```

> [!NOTE]
> 3.1.0-Beta. 4 SDK form tanıyıcı _API sürüm 2,1-Preview. 3 ' ü yansıtır.

#### <a name="v20"></a>[v2.0](#tab/ga)

```console
dotnet add package Azure.AI.FormRecognizer --version 3.0.0
```

> [!NOTE]
> Form tanıyıcı 3.0.0 SDK, API v 2.0 'ı yansıtır

---

> [!TIP]
> Tüm hızlı başlangıç kodu dosyasını aynı anda görüntülemek mi istiyorsunuz? Bu hızlı başlangıçta kod örneklerini içeren [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)'da bulabilirsiniz.

Proje dizininden, tercih ettiğiniz düzenleyicide veya IDE 'de *program. cs* dosyasını açın. Aşağıdaki yönergeleri ekleyin `using` :

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_using)]

Uygulamanın **Program** sınıfında, kaynağınızın anahtarı ve uç noktası için değişkenler oluşturun.

> [!IMPORTANT]
> Azure portala gidin. **Önkoşul** bölümünde oluşturduğunuz form tanıyıcı kaynağı başarıyla dağıtıldı, **sonraki adımlar** altındaki **Kaynağa Git** düğmesine tıklayın. Anahtar ve uç noktanızı kaynağın **anahtar ve uç nokta** sayfasında, **kaynak yönetimi** altında bulabilirsiniz.
>
> İşiniz bittiğinde kodu koddan kaldırmayı unutmayın ve hiçbir zaman herkese açık bir şekilde nakletmeyin. Üretim için, kimlik bilgilerinizi depolamak ve bunlara erişmek için güvenli bir yol kullanmayı düşünün. Daha fazla bilgi için bilişsel Hizmetler [güvenlik](../../../cognitive-services-security.md) makalesine bakın.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_creds)]

Uygulamanın **Main** yönteminde, bu hızlı başlangıçta kullanılan zaman uyumsuz görevlere bir çağrı ekleyin. Bunları daha sonra uygulayacaksınız.

#### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/preview)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_main)]

#### <a name="v20"></a>[v2.0](#tab/ga)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_main)]

---

## <a name="object-model"></a>Nesne modeli

Form tanıyıcı ile iki farklı istemci türü oluşturabilirsiniz. Birincisi, `FormRecognizerClient` hizmeti tanınan form alanları ve içerikleri için sorgulamak üzere kullanılır. İkincisi, `FormTrainingClient` tanımayı geliştirmek için kullanabileceğiniz özel modeller oluşturmak ve yönetmek için kullanılır.

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` için işlem sağlar:

* Özel formlarınızı çözümlemek için eğitilen özel modeller kullanarak form alanlarını ve içeriği tanıyor.  Bu değerler bir nesne koleksiyonunda döndürülür `RecognizedForm` . Bkz. [özel formları çözümleme](#analyze-forms-with-a-custom-model).
* Bir modeli eğitme gerekmeden tablolar, satırlar ve sözcükler dahil form içeriğini tanıma.  Form içeriği bir nesne koleksiyonunda döndürülür `FormPage` . Bkz. örnek [Çözümleme düzeni](#analyze-layout).
* Form tanıyıcı hizmetinde önceden eğitilen bir model kullanarak ABD giriş, iş kartı, fatura ve kimlik belgelerinin ortak alanlarını tanıyor.

### <a name="formtrainingclient"></a>Formtraıningclient

`FormTrainingClient` için işlem sağlar:

* Özel modellerinizde bulunan tüm alanları ve değerleri çözümlemek için özel modelleri eğitme.  `CustomFormModel`Modelin analiz edileceği form türlerini ve her form türü için ayıklanacak alanları gösteren bir döndürülür.
* Özel formlarınızı etiketleyerek belirttiğiniz belirli alanları ve değerleri çözümlemek için özel modeller eğitimi yapın.  , `CustomFormModel` Modelin ayıklanacağı alanları ve her bir alan için tahmini doğruluğu belirten bir döndürülür.
* Hesabınızda oluşturulan modelleri yönetme.
* Özel bir modeli bir form tanıyıcı kaynağından diğerine kopyalama.

[Bir modeli eğitme](#train-a-custom-model) ve [özel modelleri yönetme](#manage-custom-models)örneklerine bakın.

> [!NOTE]
> Modeller ayrıca [form tanıyıcı etiketleme aracı](../../quickstarts/label-tool.md)gibi bir grafik kullanıcı arabirimi kullanılarak eğitilmiş olabilir.

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, .NET için form tanıyıcı istemci kitaplığı ile aşağıdaki görevlerin nasıl yapılacağını gösterir:
<!-- markdownlint-disable MD001 -->

#### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/preview)

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Düzeni çözümle](#analyze-layout)
* [Alındıları analiz etme](#analyze-receipts)
* [İş kartlarını çözümle](#analyze-business-cards)
* [Faturaları analiz etme](#analyze-invoices)
* [Kimlik belgelerini analiz etme](#analyze-identity-documents)
* [Özel bir modeli eğitme](#train-a-custom-model)
* [Formları özel bir model ile analiz etme](#analyze-forms-with-a-custom-model)
* [Özel modellerinizi yönetin](#manage-your-custom-models)

#### <a name="v20"></a>[v2.0](#tab/ga)

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Düzeni çözümle](#analyze-layout)
* [Alındıları analiz etme](#analyze-receipts)
* [Özel bir modeli eğitme](#train-a-custom-model)
* [Formları özel bir model ile analiz etme](#analyze-forms-with-a-custom-model)
* [Özel modellerinizi yönetin](#manage-your-custom-models)

---

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

**Main** altında adlı yeni bir yöntem oluşturun `AuthenticateClient` . Bu görevi, form tanıyıcı hizmetine isteklerinizin kimliğini doğrulamak için diğer görevlerde kullanacaksınız. Bu yöntem, `AzureKeyCredential` gerekirse, API anahtarını yeni istemci nesneleri oluşturmadan güncelleştirebilmeniz için nesnesini kullanır.

> [!IMPORTANT]
> Azure portal anahtarınızı ve uç noktanızı alın. **Önkoşul** bölümünde oluşturduğunuz form tanıyıcı kaynağı başarıyla dağıtıldı, **sonraki adımlar** altındaki **Kaynağa Git** düğmesine tıklayın. Anahtar ve uç noktanızı kaynağın **anahtar ve uç nokta** sayfasında, **kaynak yönetimi** altında bulabilirsiniz.
>
> İşiniz bittiğinde kodu koddan kaldırmayı unutmayın ve hiçbir zaman herkese açık bir şekilde nakletmeyin. Üretim için, kimlik bilgilerinizi depolamak ve bunlara erişmek için güvenli bir yol kullanmayı düşünün. Örneğin, [Azure Anahtar Kasası](../../../../key-vault/general/overview.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_auth)]

Eğitim istemcisinin kimliğini doğrulayan yeni bir yöntem için yukarıdaki adımları tekrarlayın.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_auth_training)]

## <a name="get-assets-for-testing"></a>Test için varlıkları al

Ayrıca, eğitim ve test verileriniz için URL 'lere başvurular eklemeniz gerekecektir. Bunları **Program** sınıfınızın köküne ekleyin.

* [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL 'SI alımı":::
* Ardından, BLOB depolama kapsayıcısında tek bir belgenin SAS URL 'sini almak için yukarıdaki adımları tekrarlayın. Geçici bir konuma da kaydedin.
* Son olarak, aşağıda yer alan örnek görüntünün URL 'sini kaydedin ( [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms)üzerinde de mevcuttur).

#### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/preview)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_urls)]

#### <a name="v20"></a>[v2.0](#tab/ga)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_urls)]

---

## <a name="analyze-layout"></a>Düzeni çözümle

Belge içindeki tabloları, satırları ve sözcükleri, bir modeli eğmenize gerek kalmadan analiz etmek için form tanıyıcısı 'nı kullanabilirsiniz. Döndürülen değer bir **Formpage** nesneleri koleksiyonudur: gönderilen belgedeki her sayfa için bir tane. Düzen ayıklama hakkında daha fazla bilgi için bkz. [Düzen kavramsal Kılavuzu](../../concept-layout.md).

Belirli bir URL 'deki bir dosyanın içeriğini çözümlemek için `StartRecognizeContentFromUri` yöntemini kullanın.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_call)]

> [!TIP]
> Ayrıca, yerel bir dosyadan içerik alabilirsiniz. **Startrecognizecontent** gibi [Formrecognizerclient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) yöntemlerine bakın. Ya da, yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) 'daki örnek koda bakın.

Bu görevin geri kalanı, içerik bilgilerini konsola yazdırır.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_print)]

### <a name="output"></a>Çıktı

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    Line 6 has 3 words, and text: 'Sunnayvale, CA 87659'.
    Line 7 has 1 word, and text: '99243'.
    Line 8 has 2 words, and text: 'Invoice Number'.
    Line 9 has 2 words, and text: 'Invoice Date'.
    Line 10 has 3 words, and text: 'Invoice Due Date'.
    Line 11 has 1 word, and text: 'Charges'.
    Line 12 has 2 words, and text: 'VAT ID'.
    Line 13 has 1 word, and text: '34278587'.
    Line 14 has 1 word, and text: '6/18/2017'.
    Line 15 has 1 word, and text: '6/24/2017'.
    Line 16 has 1 word, and text: '$56,651.49'.
    Line 17 has 1 word, and text: 'PT'.
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    Cell (0, 5) contains text: 'VAT ID'.
    Cell (1, 0) contains text: '34278587'.
    Cell (1, 1) contains text: '6/18/2017'.
    Cell (1, 2) contains text: '6/24/2017'.
    Cell (1, 3) contains text: '$56,651.49'.
    Cell (1, 5) contains text: 'PT'.
```

## <a name="analyze-receipts"></a>Alındıları analiz etme

Bu bölümde, önceden eğitilen bir makbuz modeli kullanılarak ABD makbuzlarından ortak alanların nasıl analiz edileceği ve ayıklanacağı gösterilmektedir. Makbuz analizi hakkında daha fazla bilgi için bkz. [alındılar kavramsal Kılavuzu](../../concept-receipts.md).

Bir URL 'den alındıları çözümlemek için `StartRecognizeReceiptsFromUri` yöntemini kullanın.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_call)]

> [!TIP]
> Ayrıca, yerel alındı görüntülerini analiz edebilirsiniz. **StartRecognizeReceipts** gibi [Formrecognizerclient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) yöntemlerine bakın. Ya da, yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) 'daki örnek koda bakın.

Döndürülen değer bir `RecognizedForm` nesne koleksiyonudur: gönderilen belgedeki her sayfa için bir tane. Aşağıdaki kod, belirtilen URI 'de alış irsaliyesini işler ve ana alanları ve değerleri konsola yazdırır.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_print)]

### <a name="output"></a>Çıktı

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    Line 6 has 3 words, and text: 'Sunnayvale, CA 87659'.
    Line 7 has 1 word, and text: '99243'.
    Line 8 has 2 words, and text: 'Invoice Number'.
    Line 9 has 2 words, and text: 'Invoice Date'.
    Line 10 has 3 words, and text: 'Invoice Due Date'.
    Line 11 has 1 word, and text: 'Charges'.
    Line 12 has 2 words, and text: 'VAT ID'.
    Line 13 has 1 word, and text: '34278587'.
    Line 14 has 1 word, and text: '6/18/2017'.
    Line 15 has 1 word, and text: '6/24/2017'.
    Line 16 has 1 word, and text: '$56,651.49'.
    Line 17 has 1 word, and text: 'PT'.
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    Cell (0, 5) contains text: 'VAT ID'.
    Cell (1, 0) contains text: '34278587'.
    Cell (1, 1) contains text: '6/18/2017'.
    Cell (1, 2) contains text: '6/24/2017'.
    Cell (1, 3) contains text: '$56,651.49'.
    Cell (1, 5) contains text: 'PT'.
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
```

## <a name="analyze-business-cards"></a>İş kartlarını çözümle

####  <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/preview)

Bu bölümde, önceden eğitilen bir model kullanarak Ingilizce iş kartlarından ortak alanların nasıl analiz edileceği ve ayıklanacağı gösterilmektedir. İş kartı analizi hakkında daha fazla bilgi için bkz. [iş kartları kavramsal Kılavuzu](../../concept-business-cards.md).

Bir URL 'den iş kartlarını çözümlemek için `StartRecognizeBusinessCardsFromUriAsync` yöntemini kullanın.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_bc_call)]

> [!TIP]
> Ayrıca, yerel alındı görüntülerini analiz edebilirsiniz. **Startrecognizebusinesscarlar** gibi [Formrecognizerclient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) yöntemlerine bakın. Ya da, yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) 'daki örnek koda bakın.

Aşağıdaki kod, belirtilen URI 'de iş kartını işler ve ana alanları ve değerleri konsola yazdırır.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_bc_print)]

#### <a name="v20"></a>[v2.0](#tab/ga)

> [!IMPORTANT]
> Bu özellik seçili API sürümünde kullanılamaz.

---

## <a name="analyze-invoices"></a>Faturaları analiz etme

#### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/preview)

Bu bölümde, önceden eğitilen bir model kullanılarak satış faturalarından ortak alanların nasıl analiz edileceği ve ayıklanacağı gösterilmektedir. Fatura analizi hakkında daha fazla bilgi için bkz. [Fatura kavramsal Kılavuzu](../../concept-invoices.md).

Bir URL 'den faturaları çözümlemek için `StartRecognizeInvoicesFromUriAsync` yöntemini kullanın.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_invoice_call)]

> [!TIP]
> Yerel fatura görüntülerini da analiz edebilirsiniz. **Startrecognizeınices** gibi [Formrecognizerclient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) yöntemlerine bakın. Ya da, yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) 'daki örnek koda bakın.

Aşağıdaki kod, belirtilen URI 'de faturayı işler ve ana alanları ve değerleri konsola yazdırır.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_invoice_print)]

#### <a name="v20"></a>[v2.0](#tab/ga)

> [!IMPORTANT]
> Bu özellik seçili API sürümünde kullanılamaz.

---

## <a name="analyze-identity-documents"></a>Kimlik belgelerini analiz etme

#### <a name="v21-preview"></a>[v 2.1 Önizleme](#tab/preview)

Bu bölümde, form tanıyıcı önceden oluşturulmuş KIMLIK modelini kullanarak devlet tarafından verilen tanımlama belgelerinden önemli bilgilerin nasıl analiz edileceği ve ayıklanacağı gösterilmektedir. Kimlik belgesi analizi hakkında daha fazla bilgi için bkz. [önceden oluşturulmuş kimlik modelimiz kavramsal Kılavuzu](../../concept-identification-cards.md).

Bir URI 'den kimlik belgelerini çözümlemek için yöntemini kullanın `StartRecognizeIdDocumentsFromUriAsync` .

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs" id="snippet_id_call":::

> [!TIP]
> Yerel kimlik belge görüntülerini da analiz edebilirsiniz. **Startrecognizeiddocumentsasync** gibi [Formrecognizerclient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) yöntemlerine bakın. Ayrıca, yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) 'daki örnek koda bakın.

Aşağıdaki kod, belirtilen URI 'de kimlik belgesini işler ve ana alanları ve değerleri konsola yazdırır.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs" id="snippet_id_print":::

#### <a name="v20"></a>[v2.0](#tab/ga)

> [!IMPORTANT]
> Bu özellik seçili API sürümünde kullanılamaz.

---

## <a name="train-a-custom-model"></a>Özel bir modeli eğitme

Bu bölümde, bir modelin kendi verilerinize nasıl eğulacağı gösterilmektedir. Eğitilen bir model, özgün form belgesindeki anahtar/değer ilişkilerini içeren yapılandırılmış verileri çıktısını alabilir. Modeli eğdikten sonra, bunu test edebilir ve yeniden eğitebilir ve sonunda, gereksinimlerinize göre daha fazla formdan verileri güvenilir bir şekilde çıkarmak için kullanabilirsiniz.

> [!NOTE]
> Modelleri, [form tanıyıcı örnek etiketleme aracı](../../quickstarts/label-tool.md)gibi bir grafik kullanıcı arabirimi ile de eğitebilirsiniz.

### <a name="train-a-model-without-labels"></a>Etiketler olmadan bir modeli eğitme

Eğitim belgelerini el ile etiketlemeden özel formlarınızda bulunan tüm alanları ve değerleri çözümlemek için özel modeller eğitme. Aşağıdaki yöntem, belirli bir belge kümesi üzerinde bir model TRAIN ve modelin durumunu konsola yazdırır.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train)]

Döndürülen `CustomFormModel` nesne, modelin çözümleyebileceğiniz form türleri ve her form türünden ayıklayabileceğiniz alanlar hakkındaki bilgileri içerir. Aşağıdaki kod bloğu bu bilgileri konsola yazdırır.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_response)]

Son olarak, sonraki adımlarda kullanmak üzere eğitilen model KIMLIĞINI döndürün.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_return)]

### <a name="output"></a>Çıktı

Bu yanıt okunabilirlik için kesildi.

```console
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    ...
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    ...
Custom Model Info:
    Model Id: 95035721-f19d-40eb-8820-0c806b42798b
    Model Status: Ready
    Training model started on: 8/24/2020 6:36:44 PM +00:00
    Training model completed on: 8/24/2020 6:36:50 PM +00:00
Submodel Form Type: form-95035721-f19d-40eb-8820-0c806b42798b
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    ...
Custom Model Info:
    Model Id: e7a1181b-1fb7-40be-bfbe-1ee154183633
    Model Status: Ready
    Training model started on: 8/24/2020 6:36:44 PM +00:00
    Training model completed on: 8/24/2020 6:36:52 PM +00:00
Submodel Form Type: form-0
    FieldName: field-0, FieldLabel: Additional Notes:
    FieldName: field-1, FieldLabel: Address:
    FieldName: field-2, FieldLabel: Company Name:
    FieldName: field-3, FieldLabel: Company Phone:
    FieldName: field-4, FieldLabel: Dated As:
    FieldName: field-5, FieldLabel: Details
    FieldName: field-6, FieldLabel: Email:
    FieldName: field-7, FieldLabel: Hero Limited
    FieldName: field-8, FieldLabel: Name:
    FieldName: field-9, FieldLabel: Phone:
    ...
```

### <a name="train-a-model-with-labels"></a>Etiketler içeren bir modeli eğitme

Ayrıca, eğitim belgelerini el ile etiketleyerek özel modeller de eğitebilirsiniz. Etiketlerle eğitim, bazı senaryolarda daha iyi performansa yol açar. Etiketlerle eğitebilmeniz için, `\<filename\>.pdf.labels.json` eğitim belgelerinin yanı sıra BLOB depolama kapsayıcıda özel etiket bilgi dosyalarına () sahip olmanız gerekir. [Form tanıyıcı örnek etiketleme aracı](../../quickstarts/label-tool.md) , bu etiket dosyalarını oluşturmanıza yardımcı olmak için bir kullanıcı arabirimi sağlar. Bunu yaptıktan sonra, `StartTrainingAsync` `uselabels` parametresini olarak ayarlanan parametre ile çağırabilirsiniz `true` .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels)]

Döndürülen, `CustomFormModel` modelin ayıklayabileceğiniz alanları, her bir alandaki tahmini doğruluğunu gösterir. Aşağıdaki kod bloğu bu bilgileri konsola yazdırır.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels_response)]

### <a name="output"></a>Çıktı

Bu yanıt okunabilirlik için kesildi.

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    ...
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    ...
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Custom Model Info:
    Model Id: 63c013e3-1cab-43eb-84b0-f4b20cb9214c
    Model Status: Ready
    Training model started on: 8/24/2020 6:42:54 PM +00:00
    Training model completed on: 8/24/2020 6:43:01 PM +00:00
Submodel Form Type: form-63c013e3-1cab-43eb-84b0-f4b20cb9214c
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    FieldName: DatedAs
    FieldName: Email
    FieldName: Merchant
    ...
```

## <a name="analyze-forms-with-a-custom-model"></a>Formları özel bir model ile analiz etme

Bu bölümde, kendi formlarınız ile eğitilen modeller kullanılarak özel form türlerinizde anahtar/değer bilgilerinin ve diğer içeriklerin nasıl ayıklanacağı gösterilmektedir.

> [!IMPORTANT]
> Bu senaryoyu uygulamak için, KIMLIĞINI aşağıdaki yönteme geçirebilmeniz için zaten bir model eğitilmeniz gerekir.

`StartRecognizeCustomFormsFromUri`Yöntemini kullanacaksınız.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze)]

> [!TIP]
> Yerel bir dosyayı da analiz edebilirsiniz. **Startrecognizeccustomforms** gibi [Formrecognizerclient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) yöntemlerine bakın. Ya da, yerel görüntüleri içeren senaryolar için [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) 'daki örnek koda bakın.

Döndürülen değer bir `RecognizedForm` nesne koleksiyonudur: gönderilen belgedeki her sayfa için bir tane. Aşağıdaki kod, analiz sonuçlarını konsola yazdırır. Her tanınan alanı ve karşılık gelen değeri, Güvenirlik puanı ile birlikte yazdırır.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze_response)]

### <a name="output"></a>Çıktı

Bu yanıt okunabilirlik için kesildi.

```console
Custom Model Info:
    Model Id: 9b0108ee-65c8-450e-b527-bb309d054fc4
    Model Status: Ready
    Training model started on: 8/24/2020 7:00:31 PM +00:00
    Training model completed on: 8/24/2020 7:00:32 PM +00:00
Submodel Form Type: form-9b0108ee-65c8-450e-b527-bb309d054fc4
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    ...
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    ...

Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    ...
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Custom Model Info:
    Model Id: dc115156-ce0e-4202-bbe4-7426e7bee756
    Model Status: Ready
    Training model started on: 8/24/2020 7:00:31 PM +00:00
    Training model completed on: 8/24/2020 7:00:41 PM +00:00
Submodel Form Type: form-0
    FieldName: field-0, FieldLabel: Additional Notes:
    FieldName: field-1, FieldLabel: Address:
    FieldName: field-2, FieldLabel: Company Name:
    FieldName: field-3, FieldLabel: Company Phone:
    FieldName: field-4, FieldLabel: Dated As:
    ...
Form of type: custom:form
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: '$56,651.49
    Confidence: '0.249
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: 'PT
    Confidence: '0.245
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: '99243
    Confidence: '0.114
   ...
```

## <a name="manage-custom-models"></a>Özel modelleri yönetme

Bu bölümde, hesabınızda depolanan özel modellerin nasıl yönetileceği gösterilmektedir. Aşağıdaki yöntem içinde birden çok işlem gerçekleştirirsiniz:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage)]

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Formtanıyıcı kaynak hesabındaki modellerin sayısını denetleyin

Aşağıdaki kod bloğu, form tanıyıcı hesabınıza kaç modelin kaydedildiğini denetler ve hesap limitine göre karşılaştırır.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_count)]

### <a name="output"></a>Çıktı

```console
Account has 20 models.
It can have at most 5000 models.
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Şu anda kaynak hesapta depolanan modelleri listeleyin

Aşağıdaki kod bloğu, hesabınızdaki geçerli modelleri listeler ve ayrıntılarını konsola yazdırır.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_list)]

### <a name="output"></a>Çıktı

Bu yanıt okunabilirlik için kesildi.

```console
Custom Model Info:
    Model Id: 05932d5a-a2f8-4030-a2ef-4e5ed7112515
    Model Status: Creating
    Training model started on: 8/24/2020 7:35:02 PM +00:00
    Training model completed on: 8/24/2020 7:35:02 PM +00:00
Custom Model Info:
    Model Id: 150828c4-2eb2-487e-a728-60d5d504bd16
    Model Status: Ready
    Training model started on: 8/24/2020 7:33:25 PM +00:00
    Training model completed on: 8/24/2020 7:33:27 PM +00:00
Custom Model Info:
    Model Id: 3303e9de-6cec-4dfb-9e68-36510a6ecbb2
    Model Status: Ready
    Training model started on: 8/24/2020 7:29:27 PM +00:00
    Training model completed on: 8/24/2020 7:29:36 PM +00:00
```

### <a name="get-a-specific-model-using-the-models-id"></a>Modelin KIMLIĞINI kullanarak belirli bir modeli al

Aşağıdaki kod bloğu yeni bir model ( [model eğitme](#train-a-model-without-labels) bölümünde olduğu gibi) ve kimliğini kullanarak ikinci bir başvuru alır.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_get)]

### <a name="output"></a>Çıktı

Bu yanıt okunabilirlik için kesildi.

```console
Custom Model Info:
    Model Id: 150828c4-2eb2-487e-a728-60d5d504bd16
    Model Status: Ready
    Training model started on: 8/24/2020 7:33:25 PM +00:00
    Training model completed on: 8/24/2020 7:33:27 PM +00:00
Submodel Form Type: form-150828c4-2eb2-487e-a728-60d5d504bd16
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    FieldName: DatedAs
    FieldName: Email
    FieldName: Merchant
    FieldName: PhoneNumber
    FieldName: PurchaseOrderNumber
    FieldName: Quantity
    FieldName: Signature
    FieldName: Subtotal
    FieldName: Tax
    FieldName: Total
    FieldName: VendorName
    FieldName: Website
...
```

### <a name="delete-a-model-from-the-resource-account"></a>Kaynak hesabındaki bir modeli silme

Ayrıca, KIMLIĞINE başvurarak hesabınızdan bir modeli silebilirsiniz. Bu adım, yöntemi de kapatır.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_delete)]

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı komut ile uygulama dizininizden çalıştırın `dotnet run` .

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Sorun giderme

.NET SDK kullanarak bilişsel hizmetler form tanıyıcı istemci kitaplığıyla etkileşim kurarken, hizmet tarafından döndürülen hatalar bir ile sonuçlanır `RequestFailedException` . Bunlar, bir REST API isteği tarafından döndürülen HTTP durum kodunu içerir.

Örneğin, geçersiz bir URI ile bir makbuz görüntüsü gönderirseniz, `400` "bozuk istek" belirten bir hata döndürülür.

```csharp
try
{
    RecognizedReceiptCollection receipts = await client.StartRecognizeReceiptsFromUri(new Uri(receiptUri)).WaitForCompletionAsync();
}
catch (RequestFailedException e)
{
    Console.WriteLine(e.ToString());
}
```

İşlemin istemci istek KIMLIĞI gibi ek bilgilerin günlüğe kaydedileceğini görürsünüz.

``

İleti: Azure. RequestFailedException: hizmet isteği başarısız oldu.
Durum: 400 (Hatalı Istek)

İçerik: {"Error": {"Code": "Failedtodownloadımage", "ınnererror": {"RequestId": "8ca04feb-86dB-4552-857c-fde903251518"}, "ileti": "resim giriş URL 'sinden indirilemedi."}}

Üstbilgiler: aktarım-kodlama: öbekli x-Envoy-yukarı akış-hizmet-zamanı: REDAKSIYONU yapılmış apim-istek kimliği: REDAKSIYONU yapılan katı-taşıma-güvenlik: REDAKSIYONU yapılmış X-Content-Type-Options: REDAKSIYONU 22:48:35 2020 tarihi: charset = UTF-8 ' '

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, modelleri eğitmek ve formları farklı yollarla analiz etmek için tanıyıcı .NET istemci kitaplığı formunu kullandınız. Daha sonra, daha iyi eğitim veri kümesi oluşturma ve daha doğru modeller üretme hakkında ipuçları edinin.

> [!div class="nextstepaction"]
> [Eğitim verileri kümesi oluşturma](../../build-training-data-set.md)

* [Form Tanıma nedir?](../../overview.md)
* Bu kılavuzun örnek kodu (ve daha fazla) [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)'da bulabilirsiniz.
