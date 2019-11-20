---
title: 'Hızlı başlangıç: .NET için form tanıyıcı istemci kitaplığı | Microsoft Docs'
description: .NET için form tanıyıcı istemci kitaplığını kullanarak, yapılandırılmış veri çıkışını eğitme, ayıklama, çözümleme ve alma işlemi gerçekleştirin.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/12/2019
ms.author: pafarley
ms.openlocfilehash: 6d360939a0196d7e5dc651ecf9a01ef5cbd2f689
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904598"
---
# <a name="quickstart-form-recognizer-client-library-for-net"></a>Hızlı başlangıç: .NET için form tanıyıcı istemci kitaplığı

.NET için form tanıyıcı istemci kitaplığı ile çalışmaya başlayın. Form tanıyıcı, form belgelerinden anahtar/değer çiftlerini ve tablo verilerini tanımlamak ve ayıklamak için makine öğrenimi teknolojisini kullanan bilişsel bir hizmettir. Ardından, asıl dosyadaki ilişkileri de içeren yapılandırılmış verinin çıktısını verir. SDK paketini yüklemek için bu adımları izleyin ve temel görevler için örnek kodu deneyin.

.NET için form tanıyıcı istemci kitaplığı 'nı kullanarak şunları yapın:

* [Özel form tanıyıcı modeli eğitme](#train-a-custom-model)
* [Ayıklanan anahtarların bir listesini alın](#get-a-list-of-extracted-keys)
* [Formları özel bir model ile analiz etme](#analyze-forms-with-a-custom-model)
* [Özel modellerin bir listesini alın](#get-a-list-of-custom-models)
* [Özel bir modeli silme](#delete-a-custom-model)

[Başvuru belgeleri](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/formrecognizer?view=azure-dotnet-preview) | [kitaplığı kaynak kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.FormRecognizer) | [paketi (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.FormRecognizer/)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/).
* Form tanıyıcı sınırlı erişim önizlemesine erişim. Önizlemeye erişim sağlamak için [form tanıyıcı erişim isteği](https://aka.ms/FormRecognizerRequestAccess) formunu doldurun ve gönderebilirsiniz.
* Eğitim verileri kümesi içeren bir Azure Depolama Blobu. Eğitim verilerinizi birlikte yerleştirmeye yönelik ipuçları ve seçenekler için bkz. [özel bir model için eğitim verileri kümesi oluşturma](../build-training-data-set.md) . Bu hızlı başlangıçta, [örnek veri kümesinin](https://go.microsoft.com/fwlink/?linkid=2090451) **eğitme** klasörü altındaki dosyaları kullanabilirsiniz.
* [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)'un geçerli sürümü.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-form-recognizer-azure-resource"></a>Form tanıyıcı Azure kaynağı oluşturma

[!INCLUDE [create resource](../includes/create-resource.md)]

Deneme aboneliğinizden veya kaynağından bir anahtar aldıktan sonra, sırasıyla `FORM_RECOGNIZER_KEY` ve `FORM_RECOGNIZER_ENDPOINT`adlı anahtar ve uç nokta için [ortam değişkenleri oluşturun](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) .

### <a name="create-a-new-c-application"></a>Yeni C# bir uygulama oluşturun

Konsol penceresinde (cmd, PowerShell veya Bash gibi), `formrecognizer-quickstart`adlı yeni bir konsol uygulaması oluşturmak için `dotnet new` komutunu kullanın. Bu komut, tek bir kaynak dosyası olan C# basit bir "Merhaba Dünya" projesi oluşturur: _program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
```

Dizininizi yeni oluşturulan uygulama klasörüyle değiştirin. Ardından uygulamayı ile derleyin:

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

Proje dizininden, _program.cs_ dosyasını tercih ettiğiniz DÜZENLEYICIDE veya IDE 'de açın. Aşağıdaki `using` deyimlerini ekleyin:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_using)]

Sonra uygulamanın **Main** yöntemine aşağıdaki kodu ekleyin. Bu zaman uyumsuz görevi üzerinde daha sonra tanımlayacaksınız.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_main)]

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler

Uygulama dizini içinde, aşağıdaki komutla .NET için form tanıyıcı istemci Kitaplığı ' nı yükleyeceksiniz:

```console
dotnet add package Microsoft.Azure.CognitiveServices.FormRecognizer --version 0.8.0-preview
```

Visual Studio IDE kullanıyorsanız, istemci kitaplığı indirilebilir bir NuGet paketi olarak kullanılabilir.

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar form tanıyıcı SDK 'nın ana işlevlerini işler.

|Ad|Açıklama|
|---|---|
|[FormRecognizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.formrecognizerclient?view=azure-dotnet-preview)|Bu sınıf, tüm form tanıyıcı işlevleri için gereklidir. Bunu Abonelik bilgileriniz ile birlikte başlatır ve diğer sınıfların örneklerini oluşturmak için kullanırsınız.|
|[Traınrequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainrequest?view=azure-dotnet-preview)| Kendi eğitim girişi verilerinizi kullanarak özel bir form tanıyıcı modeli eğitebilmeniz için bu sınıfı kullanın. |
|[Traınresult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainresult?view=azure-dotnet-preview)| Bu sınıf, daha sonra formları çözümlemek için kullanabileceğiniz model KIMLIĞI de dahil olmak üzere özel model eğitme işleminin sonuçlarını sunar. |
|[Analiz Zersonucu](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)| Bu sınıf özel bir model çözümle işleminin sonuçlarını sunar. Bu, **Extractedpage** örneklerinin bir listesini içerir. |
|[ExtractedPage](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.extractedpage?view=azure-dotnet-preview)| Bu sınıf, tek bir form belgesinden ayıklanan tüm verileri temsil eder.|

## <a name="code-examples"></a>Kod örnekleri

<!--
    Include code snippets and short descriptions for each task you list in the the bulleted list. Briefly explain each operation, but include enough clarity to explain complex or otherwise tricky operations.

    Include links to the service's reference content when introducing a class for the first time
-->

Bu kod parçacıkları, .NET için form tanıyıcı istemci kitaplığı ile aşağıdaki görevlerin nasıl yapılacağını gösterir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Özel form tanıyıcı modeli eğitme](#train-a-custom-model)
* [Ayıklanan anahtarların bir listesini alın](#get-a-list-of-extracted-keys)
* [Formları özel bir model ile analiz etme](#analyze-forms-with-a-custom-model)
* [Özel modellerin bir listesini alın](#get-a-list-of-custom-models)
* [Özel bir modeli silme](#delete-a-custom-model)

## <a name="define-variables"></a>Değişkenleri tanımlama

Herhangi bir yöntemi tanımladıktan önce, aşağıdaki değişken tanımlarını **Program** sınıfınızın en üstüne ekleyin. Değişkenlerin bazılarını kendiniz doldurmanız gerekir. 

* Eğitim verilerinize yönelik SAS URL 'sini almak için, Microsoft Azure Depolama Gezgini açın, kapsayıcınıza sağ tıklayın ve **paylaşılan erişim Imzasını al**' ı seçin. **Okuma** ve **Listeleme** izinlerinin işaretli olduğundan emin olun ve **Oluştur**' a tıklayın. Sonra **URL** bölümündeki değeri kopyalayın. Şu biçimde olmalıdır: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Analiz etmek için bir örnek form gerekiyorsa, [örnek veri kümesinin](https://go.microsoft.com/fwlink/?linkid=2090451) **Test** klasörü altındaki dosyalardan birini kullanabilirsiniz. Bu kılavuz yalnızca PDF formlarını kullanır.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_variables)]

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

`Main` yönteminin altında, `Main`başvuruda bulunulan görevi tanımlayın. Burada, yukarıda tanımladığınız abonelik değişkenlerini kullanarak istemci nesnesinin kimliğini doğrulacaksınız. Daha sonra diğer yöntemleri tanımlayacaksınız.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_maintask)]

## <a name="train-a-custom-model"></a>Özel bir modeli eğitme

Aşağıdaki yöntem, Azure Blob kapsayıcısında depolanan belgelerde yeni bir tanıma modeli eğitebilmeniz için form tanıyıcı istemci nesnesini kullanır. Yeni eğitilen model hakkındaki bilgileri (bir [Modelresult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelresult?view=azure-dotnet-preview) nesnesi tarafından temsil edilir) göstermek için bir yardımcı yöntem kullanır ve model kimliğini döndürür.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_train)]

Aşağıdaki yardımcı yöntemi bir form tanıyıcı modeliyle ilgili bilgileri görüntüler.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displaymodel)]

## <a name="get-a-list-of-extracted-keys"></a>Ayıklanan anahtarların bir listesini alın

Eğitim tamamlandığında, özel model eğitim belgelerinden ayıkladığı anahtarların bir listesini tutar. Gelecekteki form belgelerinin bu anahtarları içermesini bekler ve analiz işleminde karşılık gelen değerleri ayıklar. Ayıklanan anahtarların listesini almak ve konsola yazdırmak için aşağıdaki yöntemi kullanın. Bu, eğitim sürecinin etkili olduğunu doğrulamak için iyi bir yoldur.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getkeys)]

## <a name="analyze-forms-with-a-custom-model"></a>Formları özel bir model ile analiz etme

Bu yöntem, bir PDF form belgesini analiz etmek ve anahtar/değer verilerini ayıklamak için form tanıyıcı istemcisini ve model KIMLIĞINI kullanır. Sonuçları göstermek için bir yardımcı yöntemi kullanır (bir [analiz Zeresult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview) nesnesi tarafından gösterilir).

> [!NOTE]
> Aşağıdaki yöntem bir PDF formunu analiz eder. JPEG ve PNG formlarını çözümleyen benzer yöntemler için [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer)'daki tam örnek koda bakın.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_analyzepdf)]

Aşağıdaki yardımcı yöntemi bir çözümleme işlemi hakkındaki bilgileri görüntüler.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displayanalyze)]

## <a name="get-a-list-of-custom-models"></a>Özel modellerin bir listesini alın

Hesabınıza ait olan eğitilen modellerin bir listesini döndürebilir ve oluşturulma tarihi hakkında bilgi alabilirsiniz. Modellerin listesi bir [Modelsresult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelsresult?view=azure-dotnet-preview) nesnesiyle temsil edilir.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getmodellist)]

## <a name="delete-a-custom-model"></a>Özel bir modeli silme

Özel modeli hesabınızdan silmek istiyorsanız aşağıdaki yöntemi kullanın:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_deletemodel)]

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulama dizininizden `dotnet run` komutunu çağırarak uygulamayı çalıştırın.

```console
dotnet run
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Ayrıca, hesabınızdan silmek istediğiniz özel bir model varsa, [özel bir modeli silme](#delete-a-custom-model)bölümünde yöntemi çalıştırın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, özel bir modeli eğitme ve formları analiz etmek için tanıyıcı .NET istemci kitaplığı formunu kullandınız. Daha sonra, daha iyi eğitim veri kümesi oluşturma ve daha doğru modeller üretme hakkında ipuçları edinin.

> [!div class="nextstepaction"]
>[Eğitim veri kümesi oluşturma](../build-training-data-set.md)

* [Form tanıyıcı nedir?](../overview.md)
* Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer)' da bulunabilir.
