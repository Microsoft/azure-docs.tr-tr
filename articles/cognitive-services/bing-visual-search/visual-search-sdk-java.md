---
title: 'Hızlı başlangıç: Java için Bing Görsel Arama istemci kitaplığı | Microsoft Docs'
description: Bing Görsel Arama SDK kullanarak bir görüntüyü karşıya yükleyin ve onunla ilgili Öngörüler alın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 09/30/2019
ms.author: aahi
ms.openlocfilehash: 7fb00fd3ce588aeeba4f315f191f6b82d6b75715
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "71695865"
---
# <a name="quickstart-bing-visual-search-client-library-for-java"></a>Hızlı başlangıç: Java için Bing Görsel Arama istemci kitaplığı

Java için Bing Görsel Arama istemci kitaplığı ile çalışmaya başlayın. Paketi yüklemek için bu adımları izleyin ve temel görevler için örnek kodu deneyin. 

Java için Bing Görsel Arama istemci kitaplığını kullanarak şunları yapın:

* Görsel arama isteği göndermek için bir görüntü karşıya yükleyin.
* Görüntü Insight belirtecini ve görsel arama etiketlerini alın.

[Başvuru belgeleri](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingvisualsearch?view=azure-java-stable)  | [kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch)  | [yapıt (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-visualsearch/)  | [örnekleri](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
* [Java Development Kit 'in (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html) geçerli sürümü
* [Gradle derleme aracı](https://gradle.org/install/)veya başka bir bağımlılık Yöneticisi

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-bing-visual-search-azure-resource"></a>Bing Görsel Arama Azure kaynağı oluşturma

Azure bilişsel hizmetler, abone olduğunuz Azure kaynakları tarafından temsil edilir. Yerel makinenizde [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) veya [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) kullanarak Bing Görsel Arama için bir kaynak oluşturun. Aşağıdakileri de yapabilirsiniz:

* Ücretsiz olarak yedi gün boyunca geçerli bir [deneme anahtarı](https://azure.microsoft.com/try/cognitive-services/#decision) alın. Kaydolduktan sonra [Azure Web sitesinde](https://azure.microsoft.com/try/cognitive-services/my-apis/)mevcut olacaktır.  
* [Azure Portal](https://portal.azure.com/)kaynağı görüntüleyin.

Deneme aboneliğinizden veya kaynağından bir anahtar aldıktan sonra, `BING_SEARCH_V7_SUBSCRIPTION_KEY` adlı anahtar için [bir ortam değişkeni oluşturun](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) .

### <a name="create-a-new-gradle-project"></a>Yeni bir Gradle projesi oluşturma

Konsol penceresinde (cmd, PowerShell veya Bash gibi), uygulamanız için yeni bir dizin oluşturun ve bu uygulamaya gidin. 

```console
mkdir myapp && cd myapp
```

Çalışma dizininizdeki `gradle init` komutunu çalıştırın. Bu komut, uygulamanızı oluşturmak ve yapılandırmak için çalışma zamanında kullanılan *Build. Gradle. kts* de dahil olmak üzere Gradle için gerekli derleme dosyaları oluşturur.

```console
gradle init --type basic
```

Bir **DSL**seçmeniz Istendiğinde, **Kotlin**' ı seçin.

*Build. Gradle. kts* ' i bulun ve tercıh ettiğiniz IDE veya metin düzenleyicinizle açın. Sonra bu derleme yapılandırmasında Kopyala:

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingVisualSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Örnek uygulamanız için bir klasör oluşturun. Çalışma dizininizden aşağıdaki komutu çalıştırın:

```console
mkdir -p src/main/java
```

API 'ye yüklemek istediğiniz görüntü için bir klasör oluşturun. Görüntüyü **kaynaklar** klasörünün içine yerleştirin.

```console
mkdir -p src/main/resources
``` 

Yeni klasöre gidin ve *Bingvisualsearchsample. Java*adlı bir dosya oluşturun. Bunu tercih ettiğiniz düzenleyicide veya IDE 'de açın ve aşağıdaki `import` deyimlerini ekleyin:

[!code-java[Import statements](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=imports)]

Ardından yeni bir sınıf oluşturun.

```java
public class BingVisualSearchSample {
}
```

Uygulamanın `main` yönteminde, kaynağınızın Azure uç noktası ve anahtarı için değişkenler oluşturun. Uygulamayı başlattıktan sonra ortam değişkenini oluşturduysanız, değişkene erişmek için onu çalıştıran düzenleyiciyi, IDE 'yi veya kabuğu kapatıp yeniden açmanız gerekir. Ardından karşıya yüklediğiniz görüntü için bir `byte[]` oluşturun. Daha sonra tanımladığınız yöntemler için bir `try` bloğu oluşturun ve `toByteArray()` kullanarak görüntüyü yükleyin ve bayta dönüştürün.

[!code-java[Main method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=main)]


### <a name="install-the-client-library"></a>İstemci kitaplığını yükler

Bu hızlı başlangıç, Gradle bağımlılık yöneticisini kullanır. İstemci kitaplığını ve diğer bağımlılık yöneticilerinin bilgilerini [Maven merkezi deposunda](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/)bulabilirsiniz.

Projenizin *Build. Gradle. kts* dosyasında, istemci kitaplığını bir `implementation` bildiri olarak eklediğinizden emin olun. 

```kotlin
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, Bing Görsel Arama istemci kitaplığı ve Java ile aşağıdaki görevlerin nasıl yapılacağını gösterir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Görsel arama isteği gönder](#send-a-visual-search-request)
* [Görüntü Insight belirtecini ve görsel arama etiketlerini yazdır](#print-the-image-insight-token-and-visual-search-tags)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

> [!NOTE]
> Bu hızlı başlangıç, `BING_SEARCH_V7_SUBSCRIPTION_KEY` adlı Bing Görsel Arama anahtarınız için [bir ortam değişkeni oluşturduğunuzu](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) varsayar.


Ana yönteminizin içinde, bir [Bingvisualsearchapı](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingvisualsearchapi?view=azure-java-stable) nesnesi oluşturmak için abonelik anahtarınızı kullandığınızdan emin olun.

```csharp
BingVisualSearchAPI client = BingVisualSearchManager.authenticate(subscriptionKey);
```

## <a name="send-a-visual-search-request"></a>Görsel arama isteği gönder

Yeni bir yöntemde, istemcinin [Bingimages (). visualSearch (](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingimages.visualsearch?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_visualsearch_BingImages_visualSearch__) ) yöntemini kullanarak görüntü bayt dizisini (`main()` yönteminde oluşturulan) gönderin. 

[!code-java[visualSearch() method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=visualSearch)]

## <a name="print-the-image-insight-token-and-visual-search-tags"></a>Görüntü Insight belirtecini ve görsel arama etiketlerini yazdır

[Imagekşimdi Ledge](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.models.imageknowledge?view=azure-java-stable) nesnesinin null olup olmadığını denetleyin. Aksi takdirde, Image Insights belirtecini, etiket sayısını, eylem sayısını ve ilk eylem türünü yazdırın.

[!code-java[Print token and tags](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=printVisualSearchResults)]

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı ile oluşturabilirsiniz:

```console
gradle build
```

Uygulamayı `run` hedefle çalıştırın:

```console
gradle run
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Tek sayfalı Web uygulaması oluşturma](tutorial-bing-visual-search-single-page-app.md)
