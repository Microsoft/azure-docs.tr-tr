---
title: 'Hızlı Başlangıç: Java için Bing Özel Arama istemci kitaplığı | Microsoft Docs'
description: Bing Özel Arama örneğinizin arama sonuçlarını isteyerek, Java için Bing Özel Arama istemci kitaplığı ile çalışmaya başlayın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 09/17/2019
ms.author: aahi
ms.openlocfilehash: 7d1a7b2cdba082c78a1753ea0dcce6ead02ab036
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147982"
---
# <a name="quickstart-bing-custom-search-client-library-for-java"></a>Hızlı Başlangıç: Java için Bing Özel Arama istemci kitaplığı

Java için Bing Özel Arama istemci kitaplığı ile çalışmaya başlayın. Paketi yüklemek için bu adımları izleyin ve temel görevler için örnek kodu deneyin. Bing Özel Arama API'si, ilgilendiğiniz konular için özel, reklam ücretsiz arama deneyimleri oluşturmanıza olanak sağlar.

Java için Bing Özel Arama istemci kitaplığını kullanarak şunları yapın:

* Bing Özel Arama örneğinden Web 'de arama sonuçları bulun. 

[Başvuru belge](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-java-stable) | [kitaplığı kaynak kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [yapıtı (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/) | [örnekleri](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/).
* [Java Development Kit 'in (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)geçerli sürümü.
* [Gradle yapı aracı](https://gradle.org/install/)veya başka bir bağımlılık Yöneticisi.
* Bir Bing Özel Arama örneği. Bkz [. hızlı başlangıç: Daha fazla bilgi için ilk](quick-start.md) Bing özel arama örneğinizi oluşturun.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-bing-custom-search-azure-resource"></a>Bing Özel Arama Azure kaynağı oluşturma

Azure bilişsel hizmetler, abone olduğunuz Azure kaynakları tarafından temsil edilir. Yerel makinenizde [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) veya [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) kullanarak Bing özel arama için bir kaynak oluşturun. Aşağıdakileri de yapabilirsiniz:

* [Deneme anahtarını](https://azure.microsoft.com/try/cognitive-services/#decision) ücretsiz olarak 7 gün boyunca geçerli olacak şekilde öğrenin. Kaydolduktan sonra [Azure Web sitesinde](https://azure.microsoft.com/try/cognitive-services/my-apis/)mevcut olacaktır.  
* [Azure Portal](https://portal.azure.com/)kaynağı görüntüleyin.

Deneme aboneliğinizden veya kaynağından bir anahtar aldıktan sonra adlı `AZURE_BING_CUSTOM_SEARCH_API_KEY`anahtar için [bir ortam değişkeni oluşturun](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) .

### <a name="create-a-new-gradle-project"></a>Yeni bir Gradle projesi oluşturma

> [!TIP]
> Gradle kullanmıyorsanız, [Maven merkezi deposundaki](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/)diğer bağımlılık yöneticilerinin istemci kitaplığı ayrıntılarını bulabilirsiniz.

Konsol penceresinde (cmd, PowerShell veya Bash gibi), uygulamanız için yeni bir dizin oluşturun ve bu uygulamaya gidin. 

```console
mkdir myapp && cd myapp
```

Çalışma dizininizden `gradle init` komutunu çalıştırın. Bu komut, uygulamanızı yapılandırmak için çalışma zamanında kullanılan bir *Build. Gradle. kts* dosyası da dahil olmak üzere, Gradle için önemli derleme dosyaları oluşturur.

```console
gradle init --type basic
```

Bir **DSL**seçmeniz Istendiğinde, **Kotlin**' ı seçin.

## <a name="install-the-client-library"></a>İstemci kitaplığını yükler 

*Build. Gradle. kts* ' i bulun ve tercıh ettiğiniz IDE veya metin düzenleyicinizle açın. Sonra bu derleme yapılandırmasında kopyalayın. İstemci kitaplığının altına `dependencies`dahil ettiğinizden emin olun:

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingCustomSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-customsearch:1.0.2")
}
```

Örnek uygulamanız için bir klasör oluşturun. Çalışma dizininizden aşağıdaki komutu çalıştırın:

```console
mkdir src/main/java
```

Yeni klasöre gidin ve *Bingcustomsearchsample. Java*adlı bir dosya oluşturun. Açın ve aşağıdaki `import` deyimleri ekleyin:


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

Adlı bir sınıf oluşturun`BingCustomSearchSample`

```java
public class BingCustomSearchSample {
}
```

Sınıfında, kaynağınızın Azure uç noktası `main` ve anahtarı için bir yöntem ve değişken oluşturun. Uygulamayı başlattıktan sonra ortam değişkenini oluşturduysanız, değişkene erişmek için onu çalıştıran düzenleyiciyi, IDE 'yi veya kabuğu kapatın ve yeniden açın. Yöntemleri daha sonra tanımlayacaksınız.

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>Nesne modeli

Bing Özel Arama İstemcisi, [Bingcustomsearchmanager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager?view=azure-java-stable) nesnesinin [Authenticate ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomSearchManager_authenticate_String_) yönteminden oluşturulan bir [bingcustomsearchapı](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) nesnesidir. İstemcinin [Bingcustomınstances. Search ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) yöntemini kullanarak bir arama isteği gönderebilirsiniz.

API yanıtı, arama sorgusu ve arama sonuçları hakkında bilgi içeren bir [SearchResponse](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse?view=azure-java-stable) nesnesidir.

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, Java için Bing Özel Arama istemci kitaplığı ile aşağıdaki görevlerin nasıl yapılacağını gösterir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Özel arama örneğinizin arama sonuçlarını al](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

Main yönteminiz, anahtarınızı alan bir [Bingcustomsearchmanager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) nesnesi içermelidir ve ' ı çağırır `authenticate()`.

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>Özel arama örneğinizin arama sonuçlarını al

Özel örneğinize bir arama sorgusu göndermek için istemcinin [Bingcustomınstances. Search ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) işlevini kullanın. Öğesini özel yapılandırma kimliğinize veya varsayılan olarak olarak `1`ayarlayın. `withCustomConfig` API 'den bir yanıt aldıktan sonra, herhangi bir arama sonucunun bulunup bulunmadığını denetleyin. Bu durumda, yanıtın `webPages().value().get()` işlevini çağırarak ilk arama sonucunu alın ve sonucun adını ve URL 'sini yazdırın. 

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Projenin ana dizininden aşağıdaki komutla uygulamayı oluşturun:

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
> [Özel bir arama Web uygulaması oluşturma](./tutorials/custom-search-web-page.md)
