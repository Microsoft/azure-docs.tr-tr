---
title: Azure 'da bir işlev yayımlamak için Java ve Gradle kullanma
description: Java ve Gradle ile Azure 'da HTTP ile tetiklenen bir işlev oluşturun ve yayımlayın.
author: KarlErickson
ms.custom: devx-track-java
ms.author: karler
ms.topic: how-to
ms.date: 04/08/2020
ms.openlocfilehash: d7f8aa990f5a5e64d2d5c59b52457149187acddd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773990"
---
# <a name="use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>Azure 'da bir işlev oluşturmak ve yayımlamak için Java ve Gradle kullanma

Bu makalede, Gradle komut satırı aracıyla Azure Işlevleri 'nde bir Java işlevi projesi oluşturma ve yayımlama işlemi gösterilmektedir. İşiniz bittiğinde, işlev kodunuz Azure 'da [sunucusuz bir barındırma planında](consumption-plan.md) çalışır ve bir http isteği tarafından tetiklenir. 

> [!NOTE]
> Gradle, tercih edilmemiş geliştirme aracınız değilse [Maven](./create-first-function-cli-java.md), [IntelliJ fikrini](/azure/developer/java/toolkit-for-intellij/quickstart-functions) ve [vs Code](./create-first-function-vs-code-java.md)kullanarak Java geliştiricileri için benzer öğreticilerimize göz atın.

## <a name="prerequisites"></a>Önkoşullar

Java kullanarak işlev uygulamaları geliştirebilmeniz için şunlar yüklü olmalıdır:

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), sürüm 8
- [Azure CLI]
- [Azure Functions Core Tools](./functions-run-local.md#v2) sürüm 2.6.666 veya üzeri
- [Gradle](https://gradle.org/), sürüm 4,10 ve üzeri

Ayrıca etkin bir Azure aboneliğine de ihtiyacınız vardır. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Bu hızlı başlangıcın tamamlanabilmesi için JAVA_HOME ortam değişkeni JDK’nin yükleme konumu olarak ayarlanmalıdır.

## <a name="prepare-a-functions-project"></a>Işlevler projesi hazırlama

Örnek projeyi kopyalamak için aşağıdaki komutu kullanın:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

' `build.gradle` İ açın ve `appName` Azure 'a dağıtma sırasında etki alanı adı çakışmasını önlemek için aşağıdaki bölümde yer alan öğesini benzersiz bir adla değiştirin. 

```gradle
azurefunctions {
    resourceGroup = 'java-functions-group'
    appName = 'azure-functions-sample-demo'
    pricingTier = 'Consumption'
    region = 'westus'
    runtime {
      os = 'windows'
    }
    localDebug = "transport=dt_socket,server=y,suspend=n,address=5005"
}
```

Yeni Function. Java dosyasını bir metin düzenleyicisinde *src/Main/Java* yolundan açın ve oluşturulan kodu gözden geçirin. Bu kod, isteğin gövdesini yansıtan [http ile tetiklenen](functions-bindings-http-webhook.md) bir işlevdir. 

> [!div class="nextstepaction"]
> [Bir sorunla karşılaştım](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=generate-project)

## <a name="run-the-function-locally"></a>İşlevi yerel olarak çalıştırma

İşlev projesini oluşturmak için aşağıdaki komutu çalıştırın:

```bash
gradle jar --info
gradle azureFunctionsRun
```
Projeyi yerel olarak çalıştırdığınızda Azure Functions Core Tools aşağıdakine benzer bir çıktı görürsünüz:

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
</pre>

Yeni bir Terminal penceresinde aşağıdaki kıvrımlı komutunu kullanarak işlevi komut satırından tetikleyin:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

Beklenen çıkış şunlardır:

<pre>
Hello, AzureFunctions
</pre>

> [!NOTE]
> AuthLevel 'ı veya olarak ayarlarsanız `FUNCTION` `ADMIN` , yerel olarak çalıştırılırken [işlev anahtarı](functions-bindings-http-webhook-trigger.md#authorization-keys) gerekli değildir.  

İşlev kodunu durdurmak için terminalde `Ctrl+C` komutunu kullanın.

> [!div class="nextstepaction"]
> [Bir sorunla karşılaştım](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=local-run)

## <a name="deploy-the-function-to-azure"></a>İşlevi Azure’a dağıtma

İşlev uygulamanızı ilk kez dağıttığınızda Azure 'da bir işlev uygulaması ve ilgili kaynaklar oluşturulur. Dağıtmadan önce, Azure aboneliğinizde oturum açmak için [az Login](/cli/azure/authenticate-azure-cli) Azure CLI komutunu kullanın. 

```azurecli
az login
```

> [!TIP]
> Hesabınız birden çok aboneliğe erişebildeyse, bu oturum için varsayılan aboneliği ayarlamak üzere [az Account set](/cli/azure/account#az_account_set) kullanın. 

Projenizi yeni bir işlev uygulamasına dağıtmak için aşağıdaki komutu kullanın. 

```bash
gradle azureFunctionsDeploy
```

Bu, Build. Gradle dosyasındaki değerlere bağlı olarak aşağıdaki kaynakları Azure 'da oluşturur:

+ Kaynak grubu. Sağladığınız _resourceGroup_ ile adlandırılmış.
+ Depolama hesabı. Işlevleri için gereklidir. Ad, depolama hesabı adı gereksinimlerine göre rastgele oluşturulur.
+ Planı App Service. Belirtilen _Appregion_'da işlev uygulamanız için barındırılan sunucusuz tüketim planı. Ad rastgele oluşturulur.
+ İşlev uygulaması. İşlev uygulaması, işlevleriniz için dağıtım ve yürütme birimidir. Ad, rastgele oluşturulmuş bir sayıyla eklenmiş olan _uygulamadır_. 

Dağıtım ayrıca proje dosyalarını paketleyip, paket Çalıştır modu etkin olan [ZIP dağıtımını](functions-deployment-technologies.md#zip-deploy)kullanarak yeni işlev uygulamasına dağıtır.

Örnek projede HTTP tetikleyicisi için authLevel, `ANONYMOUS` kimlik doğrulamasını atlayacak. Ancak, veya gibi başka bir authLevel kullanıyorsanız `FUNCTION` `ADMIN` , http üzerinden işlev uç noktasını çağırmak için işlev anahtarını almanız gerekir. İşlev anahtarını almanın en kolay yolu [Azure Portal].

> [!div class="nextstepaction"]
> [Bir sorunla karşılaştım](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=deploy)

## <a name="get-the-http-trigger-url"></a>HTTP tetikleyici URL 'sini al

İşlevinizi tetiklemek için gereken URL 'yi, Azure portal işlev anahtarıyla alabilirsiniz. 

1. [Azure Portal]gidin, oturum açın, sayfanın üst kısmında **arama** yapmak için işlev uygulamanızın _appname_ öğesini yazın ve ENTER tuşuna basın.
 
1. İşlev uygulamanızda **işlevler**' i seçin, işlevinizi seçin ve sonra sağ üst köşedeki **Işlev Url 'sini al</>** ' a tıklayın. 

    :::image type="content" source="./media/functions-create-first-java-gradle/get-function-url-portal.png" alt-text="Azure portalından işlev URL’sini kopyalama":::

1. **Varsayılan (işlev anahtarı)** öğesini seçin ve **Kopyala**' yı seçin. 

Şimdi, işlevinizi erişmek için kopyalanmış URL 'YI kullanabilirsiniz.

## <a name="verify-the-function-in-azure"></a>Azure 'da işlevi doğrulama

Kullanarak Azure 'da çalışan işlev uygulamasını doğrulamak için `cURL` , aşağıdaki örnekteki URL 'yi portaldan KOPYALADıĞıNıZ URL ile değiştirin.

```console
curl -w "\n" http://azure-functions-sample-demo.azurewebsites.net/api/HttpExample --data AzureFunctions
```

Bu, isteğin gövdesinde bulunan işlev uç noktasına bir POST isteği gönderir `AzureFunctions` . Aşağıdaki yanıtı görürsünüz.

<pre>
Hello, AzureFunctions
</pre>

> [!div class="nextstepaction"]
> [Bir sorunla karşılaştım](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=verify-deployment)

## <a name="next-steps"></a>Sonraki adımlar

HTTP ile tetiklenen bir işlev içeren bir Java işlevleri projesi oluşturdunuz, yerel makinenizde çalıştırın ve Azure 'a dağıttınız. Şimdi, işlevinizi genişletin...

> [!div class="nextstepaction"]
> [Azure depolama kuyruğu çıkış bağlaması ekleme](functions-add-output-binding-storage-queue-java.md)


[Azure CLI]: /cli/azure
[Azure portalı]: https://portal.azure.com