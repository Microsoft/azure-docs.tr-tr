---
title: Yüz .NET istemci kitaplığı hızlı başlangıç
description: Yüzleri saptamak için .NET için yüz istemci kitaplığı 'nı kullanın, benzer (görüntüye göre arama), yüzleri (yüz tanıma arama) belirleyip yüz verilerinizi geçirin.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: ab99d574588989c84783e532fcf801dcaffdd54d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105958091"
---
.NET için yüz istemci kitaplığını kullanarak yüz tanıma ile çalışmaya başlayın. Paketi yüklemek için bu adımları izleyin ve temel görevler için örnek kodu deneyin. Yüz tanıma hizmeti, görüntülerdeki insan yüzlerini algılayıp tanımayı sağlayan gelişmiş algoritmalara erişmenizi sağlar.

.NET için yüz istemci kitaplığı 'nı kullanarak şunları yapın:

* [Bir görüntüdeki yüzleri algılama](#detect-faces-in-an-image)
* [Benzer yüzeyleri bulun](#find-similar-faces)
* [Bir PersonGroup oluşturma](#create-a-persongroup)
* [Yüz tanıma](#identify-a-face)

[Başvuru belgeleri](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face)  |  [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.6.0-preview.1)  |  [Örnekler](/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Önkoşullar


* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/)
* [Visual STUDIO IDE](https://visualstudio.microsoft.com/vs/) veya [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)'un geçerli sürümü.
* Azure aboneliğiniz olduktan sonra, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" bir yüz kaynağı oluşturun "  target="_blank"> </a> Azure Portal anahtar ve uç noktanıza ulaşmak için bir yüz kaynağı oluşturun. Dağıtıldıktan sonra **Kaynağa Git ' e** tıklayın.
    * Uygulamanızı Yüz Tanıma API'si bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
    * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-c-application"></a>Yeni bir C# uygulaması oluşturma

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Visual Studio 'yu kullanarak yeni bir .NET Core uygulaması oluşturun. 

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler 

Yeni bir proje oluşturduktan sonra, **Çözüm Gezgini** proje çözümüne sağ tıklayıp **NuGet Paketlerini Yönet**' i seçerek istemci kitaplığını yükleyebilirsiniz. Açılan paket yöneticisinde, Seç ' i seçin, **ön sürümü dahil** **et ' i** işaretleyin ve arama yapın `Microsoft.Azure.CognitiveServices.Vision.Face` . Sürüm `2.6.0-preview.1` ' ü ve ardından **öğesini seçin**. 

#### <a name="cli"></a>[CLI](#tab/cli)

Konsol penceresinde (cmd, PowerShell veya Bash gibi), `dotnet new` adıyla yeni bir konsol uygulaması oluşturmak için komutunu kullanın `face-quickstart` . Bu komut, tek bir kaynak dosyası olan basit bir "Merhaba Dünya" C# projesi oluşturur: *program. cs*. 

```console
dotnet new console -n face-quickstart
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

Uygulama dizini içinde, aşağıdaki komutla .NET için yüz istemci kitaplığı 'nı yüklersiniz:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.6.0-preview.1
```

---

> [!TIP]
> Tüm hızlı başlangıç kodu dosyasını aynı anda görüntülemek mi istiyorsunuz? Bu hızlı başlangıçta kod örneklerini içeren [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs)'da bulabilirsiniz.


Proje dizininden *program. cs* dosyasını açın ve aşağıdaki `using` yönergeleri ekleyin:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_using)]

Uygulamanın **Program** sınıfında, kaynağınızın anahtarı ve uç noktası için değişkenler oluşturun.


> [!IMPORTANT]
> Azure portala gidin. **Önkoşullar** bölümünde oluşturduğunuz yüz kaynağı başarıyla dağıtılırsa, **sonraki adımlar** altında **Kaynağa Git** düğmesine tıklayın. Anahtar ve uç noktanızı kaynağın **anahtar ve uç nokta** sayfasında, **kaynak yönetimi** altında bulabilirsiniz. 
>
> İşiniz bittiğinde kodu koddan kaldırmayı unutmayın ve hiçbir zaman herkese açık bir şekilde nakletmeyin. Üretim için, kimlik bilgilerinizi depolamak ve bunlara erişmek için güvenli bir yol kullanmayı düşünün. Daha fazla bilgi için bilişsel Hizmetler [güvenlik](../../../cognitive-services-security.md) makalesine bakın.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_creds)]

Uygulamanın **Main** yönteminde, bu hızlı başlangıçta kullanılan yöntemlere çağrılar ekleyin. Bunları daha sonra uygulayacaksınız.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_maincalls)]

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler, yüz .NET istemci kitaplığının bazı önemli özelliklerini işler:

|Ad|Açıklama|
|---|---|
|[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) | Bu sınıf, yüz hizmetini kullanma yetkinizi temsil eder ve tüm yüz işlevleri için buna ihtiyacınız vardır. Bunu Abonelik bilgileriniz ile birlikte başlatır ve diğer sınıfların örneklerini oluşturmak için kullanırsınız. |
|[Çok yönlü Işlemler](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations)|Bu sınıf, insan yüzeyleri ile gerçekleştirebileceğiniz temel algılama ve tanıma görevlerini işler. |
|[DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface)|Bu sınıf, görüntüde tek bir yüz tarafından algılanan tüm verileri temsil eder. Yüz hakkında ayrıntılı bilgi almak için bu uygulamayı kullanabilirsiniz.|
|[FaceListOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations)|Bu sınıf, bir assıralanan yüz kümesini depolayan, bulutta depolanan çok **yönlü liste** yapılarını yönetir. |
|[Persongrouppersonextensıons](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions)| Bu sınıf, tek bir kişiye ait olan bir yüzey kümesini depolayan, bulutta depolanan **kişi** yapılarını yönetir.|
|[PersonGroupOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations)| Bu sınıf, bir dizi yönetilen **kişi** nesnesini depolayan, bulut ile depolanmış olan **persongroup** yapılarını yönetir. |

## <a name="code-examples"></a>Kod örnekleri

Aşağıdaki kod parçacıkları, .NET için yüz istemci kitaplığı ile aşağıdaki görevlerin nasıl yapılacağını göstermektedir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Bir görüntüdeki yüzleri algılama](#detect-faces-in-an-image)
* [Benzer yüzeyleri bulun](#find-similar-faces)
* [Bir PersonGroup oluşturma](#create-a-persongroup)
* [Yüz tanıma](#identify-a-face)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

Yeni bir yöntemde, uç nokta ve anahtarınızla bir istemci örneği oluşturun. Anahtarınızla bir **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials)** nesnesi oluşturun ve bir **[faceclient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient)** nesnesi oluşturmak için bunu uç noktanızla birlikte kullanın.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_auth)]

### <a name="declare-helper-fields"></a>Yardımcı alanları bildirme

Daha sonra ekleyeceğiniz birçok yüz işlemi için aşağıdaki alanlar gereklidir. **Program** sınıfınızın KÖKÜNDE aşağıdaki URL dizesini tanımlayın. Bu URL, örnek görüntülerin bir klasörünü işaret eder.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_image_url)]

**Ana** yönteminizin farklı tanıma modeli türlerine işaret etmek için dizeleri tanımlayın. Daha sonra, yüz algılama için kullanmak istediğiniz tanıma modelini belirleyebileceksiniz. Bu seçenekler hakkında bilgi için bkz. [bir tanıma modeli belirtme](../../Face-API-How-to-Topics/specify-recognition-model.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_models)]

## <a name="detect-faces-in-an-image"></a>Bir görüntüdeki yüzleri algılama

### <a name="get-detected-face-objects"></a>Algılanan yüz nesneleri Al

Yüzeyleri algılamak için yeni bir yöntem oluşturun. `DetectFaceExtract`Yöntemi, VERILEN URL 'nin üç görüntüsünü işler ve program belleğindeki **[algılayıcısı](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface)** olan nesnelerin bir listesini oluşturur. **[Faceattributetype](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)** değerlerinin listesi hangi özelliklerin ayıklanacağını belirtir. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect)]

> [!TIP]
> Ayrıca, yerel görüntüdeki yüzeyleri de algılayabilirsiniz. **Detectwithstreamasync** gibi [ifasetat işlemleri](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ifaceoperations) yöntemlerine bakın.

### <a name="display-detected-face-data"></a>Algılanan yüz verileri görüntüle

Yöntemin geri kalanı `DetectFaceExtract` algılanan her bir yüzey için öznitelik verilerini ayrıştırır ve yazdırır. Her öznitelik, özgün yüz algılama API çağrısında ( **[Faceattributetype](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)** listesinde) ayrı olarak belirtilmelidir. Aşağıdaki kod her özniteliği işler, ancak büyük olasılıkla yalnızca bir veya birkaç tane kullanmanız gerekir.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Benzer yüzleri bulma

Aşağıdaki kod, algılanan tek bir yüzeyi (kaynak) alır ve eşleşmeleri (yüz görüntüye göre arama) bulmak için bir dizi diğer yüzeyi (hedef) arar. Bir eşleşme bulduğunda, eşleşen yüzün KIMLIĞINI konsola yazdırır.

### <a name="detect-faces-for-comparison"></a>Karşılaştırılacak Yüzleri Algıla

İlk olarak, ikinci bir yüz algılama yöntemi tanımlayın. Bunları karşılaştırabilmeniz için görüntülerdeki yüzeyleri saptamanız gerekir ve bu algılama yöntemi karşılaştırma işlemleri için iyileştirilmiştir. Yukarıdaki bölümde olduğu gibi ayrıntılı yüz özniteliklerini ayıklamaz ve farklı bir tanıma modeli kullanır.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Eşleşmeleri bul

Aşağıdaki yöntem, bir hedef görüntüler kümesindeki yüzeyleri ve tek bir kaynak görüntüyü algılar. Ardından, bunları karşılaştırır ve kaynak görüntüye benzer tüm hedef görüntüleri bulur.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Yazdırma eşleşmeleri

Aşağıdaki kod, eşleşme ayrıntılarını konsola yazdırır:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar_print)]

## <a name="identify-a-face"></a>Yüz tanıma

Tanımlama işlemi, bir kişinin (veya birden çok kişinin) bir görüntüsünü alır ve görüntüdeki her bir yüzün kimliğini bulmak için (yüz tanıma arama) arar. Algılanan her yüzü, yüz özellikleri bilinen farklı **kişi** nesnelerinin bir veritabanı olan bir **persongroup** ile karşılaştırır. Bu işlemi tanımlamak için önce bir **Persongroup** oluşturmanız ve eğitmeniz gerekir

### <a name="create-a-persongroup"></a>Bir PersonGroup oluşturma

Aşağıdaki kod, altı farklı **kişi** nesnesi Ile bir **persongroup** oluşturur. Her **kişiyi** bir örnek görüntü kümesiyle ilişkilendirir ve sonra her kişiyi yüz özellikleriyle tanıyacak şekilde algılar. **Person** ve **Persongroup** nesneleri, Verify, tanımla ve Gruplandır işlemlerinde kullanılır.

Oluşturduğunuz **Persongroup** 'un kimliğini temsil etmek için sınıfınızın kökünde bir dize değişkeni bildirin.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_declare)]

Yeni bir yöntemde aşağıdaki kodu ekleyin. Bu yöntem, tanımla işlemini çalıştırır. İlk kod bloğu, kişilerin adlarını örnek görüntülerle ilişkilendirir.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_files)]

Bu kodun bir değişken tanımladığına dikkat edin `sourceImageFileName` . Bu değişken, &mdash; tanımlamak üzere kişileri içeren görüntünün kaynak görüntüsüne karşılık gelir.

Sonra, Sözlükteki her kişi için bir **kişi** nesnesi oluşturmak ve uygun görüntülerden yüz verilerini eklemek için aşağıdaki kodu ekleyin. Her **kişi** nesnesi, benzersiz kimlik dizesi aracılığıyla aynı **persongroup** ile ilişkilendirilir. , `client` `url` Ve değişkenlerini `RECOGNITION_MODEL1` Bu yönteme geçirmeye unutmayın.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_create)]

> [!TIP]
> Yerel görüntülerden de bir **Persongroup** oluşturabilirsiniz. **Addfacefromstreamasync** gibi [ıpersongroupperson](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ipersongroupperson) yöntemlerine bakın.

### <a name="train-the-persongroup"></a>PersonGroup 'ı eğitme

Görüntülerinizden yüz verilerini ayıkladıktan ve farklı **kişi** nesnelerine sıraladıktan sonra, **kişi** nesnelerinden her biriyle ilişkili görsel özellikleri belirlemek için **persongroup** 'u eğitmeniz gerekir. Aşağıdaki kod, zaman uyumsuz **eğitme** yöntemini çağırır ve sonuçları, durumu konsola yazdırarak tarar.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_train)]

> [!TIP]
> Yüz Tanıma API'si, doğası gereği statik olan önceden oluşturulmuş modeller kümesi üzerinde çalışır (modelin performansı, hizmet çalıştırıldığı sürece bu şekilde çalışmaz veya geliştirilecektir). Modelin ürettiği sonuçlar, Microsoft modelin arka ucunu tamamen yeni bir model sürümüne geçirmeden güncelleştirse de değişiklik gösterebilir. Bir modelin daha yeni bir sürümünden yararlanmak için, daha yeni modeli aynı kayıt görüntülerine sahip bir parametre olarak belirterek, **Personal grubunuzu** yeniden eğitebilirsiniz.

Bu **kişi** grubu ve ilişkili **kişi** nesneleri artık doğrulama, tanımla veya grupla işlemlerinde kullanılmak üzere hazırdır.

### <a name="identify-faces"></a>Yüzleri belirleme

Aşağıdaki kod, kaynak görüntüyü alır ve görüntüde algılanan tüm yüzlerin bir listesini oluşturur. Bunlar, **Persongroup** ile ilgili olarak tanımlanabilecek yüzlerdir.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify_sources)]

Sonraki kod parçacığı, **ıdentıyasync** işlemini çağırır ve sonuçları konsola yazdırır. Burada hizmet, kaynak görüntüdeki her yüzü, belirtilen Person **grubundaki** bir **kişiye** eşleştirmeye çalışır. Bu, tanımlana yöntemini kapatır.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify)]

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

Bu hızlı başlangıçta bir **kişilik grubu** oluşturduysanız ve silmek istiyorsanız, programınızda aşağıdaki kodu çalıştırın:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_delete)]

Aşağıdaki kodla birlikte silme yöntemini tanımlayın:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_deletepersongroup)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, temel yüz tanıma görevlerini yapmak için .NET için yüz istemci kitaplığı 'nı nasıl kullanacağınızı öğrendiniz. Daha sonra, kitaplık hakkında daha fazla bilgi edinmek için başvuru belgelerini inceleyin.

> [!div class="nextstepaction"]
> [Yüz Tanıma API'si Başvurusu (.NET)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)

* [Yüz Tanıma hizmeti nedir?](../../overview.md)
* Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs)' da bulunabilir.
