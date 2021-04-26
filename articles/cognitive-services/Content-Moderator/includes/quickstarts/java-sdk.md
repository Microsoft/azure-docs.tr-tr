---
title: Content Moderator Java istemci kitaplığı hızlı başlangıç
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, Java için Azure Content Moderator istemci kitaplığı ile çalışmaya başlama hakkında bilgi edinin. Yönetmeliklerle uyum sağlamak veya kullanıcılarınız için amaçlanan ortamı sürdürmek üzere uygulamanıza içerik filtreleme yazılımı oluşturun.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 10/16/2020
ms.custom: devx-track-java, cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: 76d62806541e0a3186cd17712b7af3930d9ccf9f
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726169"
---
Java için Azure Content Moderator istemci kitaplığı 'nı kullanmaya başlayın. Maven paketini yüklemek için bu adımları izleyin ve temel görevler için örnek kodu deneyin. 

Content Moderator, rahatsız edici, riskli veya başka türlü istenmeyen içerikleri işlemenize imkan tanıyan bir AI hizmetidir. Metin, resim ve videoları taramak ve içerik bayraklarını otomatik olarak uygulamak için AI destekli içerik denetleme hizmetini kullanın. Yönetmeliklerle uyum sağlamak veya kullanıcılarınız için amaçlanan ortamı sürdürmek üzere uygulamanıza içerik filtreleme yazılımı oluşturun.

Java için Content Moderator istemci kitaplığını kullanarak şunları yapın:

* Orta metin
* Orta görüntüler

[Başvuru belgeleri](/java/api/overview/azure/cognitiveservices/client/contentmoderator)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-contentmoderator)  | [Yapıt (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator)  |  [Örnekler](/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/)
* [Java Development Kit 'in (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html) geçerli sürümü
* [Gradle yapı aracı](https://gradle.org/install/)veya başka bir bağımlılık Yöneticisi.
* Azure aboneliğiniz olduktan sonra, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title=" "  target="_blank"> </a> anahtarınızı ve uç noktanızı almak için Azure Portal bir Content Moderator kaynağı oluşturun Content moderator bir kaynak oluşturun. Dağıtım için bekleyin ve **Kaynağa Git** düğmesine tıklayın.
    * Uygulamanızı Content Moderator bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
    * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-gradle-project"></a>Yeni bir Gradle projesi oluşturma

Konsol penceresinde (cmd, PowerShell veya Bash gibi), uygulamanız için yeni bir dizin oluşturun ve bu uygulamaya gidin. 

```console
mkdir myapp && cd myapp
```

`gradle init`Çalışma dizininizden komutunu çalıştırın. Bu komut, uygulamanızı oluşturmak ve yapılandırmak için çalışma zamanında kullanılan *Build. Gradle. kts* de dahil olmak üzere Gradle için temel derleme dosyaları oluşturur.

```console
gradle init --type basic
```

Bir **DSL** seçmeniz Istendiğinde, **Kotlin**' ı seçin.

## <a name="install-the-client-library"></a>İstemci kitaplığını yükler

*Build. Gradle. kts* ' i bulun ve tercıh ettiğiniz IDE veya metin düzenleyicinizle açın. Ardından aşağıdaki derleme yapılandırması içine kopyalayın. Bu yapılandırma, bir Java uygulaması olarak projeyi, giriş noktası **Contentmoderatorhızlı başlangıç** sınıfı olan bir Java uygulaması olarak tanımlar. JSON serileştirme için Content Moderator istemci kitaplığı ve GSON SDK 'sini içeri aktarır.

```kotlin
plugins {
    java
    application
}

application{ 
    mainClassName = "ContentModeratorQuickstart"
}

repositories{
    mavenCentral()
}

dependencies{
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-contentmoderator", version = "1.0.2-beta")
    compile(group = "com.google.code.gson", name = "gson", version = "2.8.5")
}
```

### <a name="create-a-java-file"></a>Java dosyası oluşturma


Çalışma dizininizden bir proje kaynak klasörü oluşturmak için aşağıdaki komutu çalıştırın:

```console
mkdir -p src/main/java
```

Yeni klasöre gidin ve *Contentmoderatorquickstart. Java* adlı bir dosya oluşturun. Bunu tercih ettiğiniz düzenleyicide veya IDE 'de açın ve aşağıdaki deyimleri ekleyin `import` :

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

> [!TIP]
> Tüm hızlı başlangıç kodu dosyasını aynı anda görüntülemek mi istiyorsunuz? Bu hızlı başlangıçta kod örneklerini içeren [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java)'da bulabilirsiniz.

Uygulamanın **Contentmoderatorquickstart** sınıfında, kaynağınızın anahtarı ve uç noktası için değişkenler oluşturun.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_creds)]

> [!IMPORTANT]
> Azure portala gidin. **Önkoşullar** bölümünde oluşturduğunuz Content moderator kaynak başarıyla dağıtılırsa, **sonraki adımlar** altında **Kaynağa Git** düğmesine tıklayın. Anahtar ve uç noktanızı kaynağın **anahtar ve uç nokta** sayfasında, **kaynak yönetimi** altında bulabilirsiniz. 
>
> İşiniz bittiğinde kodu koddan kaldırmayı unutmayın ve hiçbir zaman herkese açık bir şekilde nakletmeyin. Üretim için, kimlik bilgilerinizi depolamak ve bunlara erişmek için güvenli bir yol kullanmayı düşünün. Daha fazla bilgi için bilişsel Hizmetler [güvenlik](../../../cognitive-services-security.md) makalesine bakın.

Uygulamanın **Main** yönteminde, bu hızlı başlangıçta kullanılan yöntemlere çağrılar ekleyin. Bu yöntemleri daha sonra tanımlayacaksınız.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_maincalls)]


## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar Content Moderator Java istemci kitaplığı 'nın bazı önemli özelliklerini işler.

|Ad|Açıklama|
|---|---|
|[ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient)|Bu sınıf tüm Content Moderator işlevleri için gereklidir. Bunu Abonelik bilgileriniz ile birlikte başlatır ve diğer sınıfların örneklerini oluşturmak için kullanırsınız.|
|[Imagedenetlemesi](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations)|Bu sınıf yetişkinlere yönelik içerik, kişisel bilgiler veya insan yüzeyleri için görüntüleri analiz etmek üzere işlevsellik sağlar.|
|[Metin Moderations](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations)|Bu sınıf, dil, küfür, hatalar ve kişisel bilgiler için metin çözümleme işlevlerini sağlar.|
|[İncelemeler](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews)|Bu sınıf, iş oluşturma, özel iş akışları ve insan incelemeleri için yöntemler de dahil olmak üzere, gözden geçirme API 'lerinin işlevlerini sağlar.|


## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, Java için Content Moderator istemci kitaplığı ile aşağıdaki görevlerin nasıl yapılacağını gösterir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Orta metin](#moderate-text)
* [Orta görüntüler](#moderate-images)


## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

Uygulamanın `main` yönteminde, abonelik uç noktası değerini ve abonelik anahtarınızı kullanarak bir [Contentmoderatorclient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient) nesnesi oluşturun.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]



## <a name="moderate-text"></a>Orta metin

### <a name="set-up-sample-text"></a>Örnek metin ayarlama

**Contentmoderatorhızlı başlangıç** sınıfınızın en üstünde, bir yerel metin dosyasına bir başvuru tanımlayın. Proje dizininize bir. txt dosyası ekleyin ve çözümlemek istediğiniz metni girin.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_var)]

### <a name="analyze-text"></a>Metni çözümle

. Txt dosyasını okuyan ve her satırda **Screentext** yöntemini çağıran yeni bir yöntem oluşturun.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod)]

### <a name="print-text-moderation-results"></a>Metin denetleme sonuçlarını yazdır

Denetleme sonuçlarını proje dizininizde bir. JSON dosyasına yazdırmak için aşağıdaki kodu ekleyin.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_print)]

`try` `catch` Yöntemini gerçekleştirmek için ve ifadesini kapatın.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_catch)]


## <a name="moderate-images"></a>Orta görüntüler

### <a name="set-up-sample-image"></a>Örnek görüntü ayarlama

Yeni bir yöntemde, bir görüntüyü işaret eden belirli bir URL dizesiyle bir **[Bodymodelmodel](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.bodymodelmodel)** nesnesi oluşturun.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod)]


### <a name="define-helper-class"></a>Yardımcı sınıfı tanımla

Ardından, *contentmoderatorquickstart. Java* dosyanızda, aşağıdaki sınıf tanımını **contentmoderatorquickstart** sınıfına ekleyin. Bu iç sınıf, görüntü denetleme işleminde kullanılır.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]


### <a name="analyze-content"></a>İçeriği analiz etme
Bu kod satırı, verilen URL 'deki görüntüyü yetişkinlere veya kcy içeriğine karşı denetler. Bu koşullarla ilgili bilgi için bkz. görüntü denetleme kavramsal Kılavuzu.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Metin denetimi
Bu kod satırı görüntüde görünür metin olup olmadığını denetler.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Yüzeyleri denetle
Bu kod satırı, resmi insan yüzeyleri için denetler.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Son olarak, döndürülen bilgileri `EvaluationData` listede depolayın.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Sonuçları Yazdır

`while`Döngüden sonra, sonuçları konsola ve bir çıkış dosyasına ( *src/Main/resources/ModerationOutput.js*) yazdıran aşağıdaki kodu ekleyin.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

`try`Yöntemi kapatın ve `catch` metodunu tamamlamaya yönelik bir ifade ekleyin.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı ile oluşturabilirsiniz:

```console
gradle build
```

Uygulamayı `gradle run` komutla çalıştırın:

```console
gradle run
```

Ardından dosya *üzerinde src/Main/Resources/ModerationOutput.js* gidin ve içerik denetleme sonuçlarını görüntüleyin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Content Moderator Java kitaplığı 'nı kullanarak denetleme görevlerini nasıl kullanacağınızı öğrendiniz. Daha sonra, bir kavramsal kılavuz okuyarak görüntülerin veya diğer ortamların denetimi hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Görüntü denetleme kavramları](../../image-moderation-api.md)
