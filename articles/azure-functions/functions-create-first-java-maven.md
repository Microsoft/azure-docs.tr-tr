---
title: Azure 'da bir işlev yayımlamak için Java ve Maven kullanma
description: Java ve Maven ile Azure 'da HTTP ile tetiklenen bir işlev oluşturun ve yayımlayın.
author: rloutlaw
manager: gwallace
ms.service: azure-functions
ms.topic: quickstart
ms.date: 08/10/2018
ms.author: glenga
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 5c51e445aaa27f3f83627ccf0da8fb80e01f156c
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329510"
---
# <a name="quickstart-use-java-and-maven-to-create-and-publish-a-function-to-azure"></a>Hızlı başlangıç: Azure 'da bir işlev oluşturmak ve yayımlamak için Java ve Maven kullanma

Bu makalede, Maven komut satırı aracıyla Azure Işlevleri için bir Java işlevi oluşturma ve yayımlama işlemi gösterilmektedir. İşiniz bittiğinde, işlev kodunuz Azure 'da [sunucusuz bir barındırma planında](functions-scale.md#consumption-plan) çalışır ve bir http isteği tarafından tetiklenir.

<!--
> [!NOTE] 
> You can also create a Kotlin-based Azure Functions project by using the azure-functions-kotlin-archetype instead. Visit the [GitHub repository](https://github.com/microsoft/azure-maven-archetypes/tree/develop/azure-functions-kotlin-archetype) for more information.
-->

## <a name="prerequisites"></a>Önkoşullar

Java kullanarak işlevleri geliştirmek için aşağıdakilerin yüklü olması gerekir:

- [Java geliştirici seti](https://aka.ms/azure-jdks), sürüm 8
- [Apache Maven](https://maven.apache.org), sürüm 3,0 veya üzeri
- [Azure CLI]
- [Azure Functions Core Tools](./functions-run-local.md#v2) sürüm 2.6.666 veya üzeri
- Azure aboneliği.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


> [!IMPORTANT]
> Bu hızlı başlangıcın tamamlanabilmesi için JAVA_HOME ortam değişkeni JDK’nin yükleme konumu olarak ayarlanmalıdır.

## <a name="generate-a-new-functions-project"></a>Yeni İşlevler projesi oluşturma

İşlevler projesini bir [Maven arketipinden](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html) oluşturmak için boş bir klasörde aşağıdaki komutu çalıştırın.

### <a name="linuxmacos"></a>Linux/macOS

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

> [!NOTE]
> Komutu çalıştırmaya ilişkin sorunlarla karşılaşıyorsanız, `maven-archetype-plugin` sürümünün kullanıldığını göz atın. Komutunu `.pom` dosyası olmayan boş bir dizinde çalıştırdığınız için, Maven 'nizi eski bir sürümden yükselttiyseniz, `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` ' den eski sürümün bir eklentisini kullanmaya çalışıyor olabilir. Bu durumda, `maven-archetype-plugin` dizinini silmeyi ve komutu yeniden çalıştırmayı deneyin.

### <a name="windows"></a>Windows

```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-archetype"
```

```cmd
mvn archetype:generate ^
    "-DarchetypeGroupId=com.microsoft.azure" ^
    "-DarchetypeArtifactId=azure-functions-archetype"
```

Maven, dağıtımda projenin oluşturulmasını tamamlaması için gereken değerleri ister. İstendiğinde aşağıdaki değerleri sağlayın:

| Değer | Açıklama |
| ----- | ----------- |
| **ID** | Java için [paket adlandırma kurallarını](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) takip eden tüm projelerde projenizi benzersiz bir şekilde tanımlayan bir değer. Bu hızlı başlangıçtaki örneklerde `com.fabrikam.functions` kullanılır. |
| **ArtifactId** | Bir sürüm numarası olmadan jar adı olan bir değer. Bu hızlı başlangıçtaki örneklerde `fabrikam-functions` kullanılır. |
| **Sürüm** | @No__t-0 varsayılan değerini seçin. |
| **leyebilir** | Oluşturulan işlev kodu için Java paketi olan bir değer. Varsayılan değeri kullanın. Bu hızlı başlangıçtaki örneklerde `com.fabrikam.functions` kullanılır. |
| **appName** | Azure 'da yeni işlev uygulamanızı tanımlayan genel olarak benzersiz ad. _ArtifactId_ , rastgele bir sayı ile eklenmiş olan varsayılanı kullanın. Bu değeri, daha sonra ihtiyacınız olacağını unutmayın. |
| **appRegion** | Kendinize veya işlevinizin erişeceği diğer hizmetlere yakın bir [bölge](https://azure.microsoft.com/regions/) seçin. Varsayılan değer: `westus`. Tüm bölgelerin bir listesini almak için bu [Azure CLI] komutunu çalıştırın:<br/>`az account list-locations --query '[].{Name:name}' -o tsv` |
| **Kaynak** | İşlev uygulamanızın oluşturulacağı yeni [kaynak grubunun](../azure-resource-manager/resource-group-overview.md) adı. Bu hızlı başlangıçtaki örneklerde kullanılan `myResourceGroup` ' ı kullanın. Kaynak grubu, Azure aboneliğiniz için benzersiz olmalıdır.|

Onaylamak için `Y` yazın veya ENTER tuşuna basın.

Maven, proje dosyalarını _ArtifactId_adlı yeni bir klasörde oluşturur ve bu örnekte `fabrikam-functions` ' dir. 

Yeni Function. Java dosyasını bir metin düzenleyicisinde *src/Main/Java* yolundan açın ve oluşturulan kodu gözden geçirin. Bu kod, isteğin gövdesini yansıtan [http ile tetiklenen](functions-bindings-http-webhook.md) bir işlevdir. 

## <a name="run-the-function-locally"></a>İşlevi yerel olarak çalıştırma

Dizini yeni oluşturulan proje klasörü ile değiştiren aşağıdaki komutu çalıştırın, sonra işlev projesini oluşturup çalıştırır:

```console
cd fabrikam-function
mvn clean package 
mvn azure-functions:run
```

Projeyi yerel olarak çalıştırdığınızda Azure Functions Core Tools aşağıdakine benzer bir çıktı görürsünüz:

```Output
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpTrigger-Java: [GET,POST] http://localhost:7071/api/HttpTrigger-Java
...
```

Yeni bir Terminal penceresinde kıvrımlı kullanarak işlevi komut satırından tetikleyin:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

```Output
Hello AzureFunctions!
```
Yerel olarak çalıştırılırken [işlev anahtarı](functions-bindings-http-webhook.md#authorization-keys) gerekli değildir. İşlev kodunu durdurmak için terminalde `Ctrl+C` komutunu kullanın.

## <a name="deploy-the-function-to-azure"></a>İşlevi Azure’a dağıtma

İşlev uygulamanızı ilk kez dağıttığınızda Azure 'da bir işlev uygulaması ve ilgili kaynaklar oluşturulur. Dağıtmadan önce, Azure aboneliğinizde oturum açmak için [az Login](/cli/azure/authenticate-azure-cli) Azure CLI komutunu kullanın. 

```azurecli
az login
```

> [!TIP]
> Hesabınız birden çok aboneliğe erişebildeyse, bu oturum için varsayılan aboneliği ayarlamak üzere [az Account set](/cli/azure/account#az-account-set) kullanın. 

Projenizi yeni bir işlev uygulamasına dağıtmak için aşağıdaki Maven komutunu kullanın. 

```azurecli
mvn azure-functions:deploy
```

Bu `azure-functions:deploy` Maven hedefi, Azure 'da aşağıdaki kaynakları oluşturur:

+ Kaynak grubu. Sağladığınız _resourceGroup_ ile adlandırılmış.
+ Depolama hesabı. Işlevleri için gereklidir. Ad, depolama hesabı adı gereksinimlerine göre rastgele oluşturulur.
+ App Service planı. Belirtilen _Appregion_'da işlev uygulamanız için sunucusuz barındırma. Ad rastgele oluşturulur.
+ İşlev uygulaması. İşlev uygulaması, işlevleriniz için dağıtım ve yürütme birimidir. Ad, rastgele oluşturulmuş bir sayıyla eklenmiş olan _uygulamadır_. 

Dağıtım ayrıca proje dosyalarını paketleyip, paket Çalıştır modu etkin olan [ZIP dağıtımını](functions-deployment-technologies.md#zip-deploy)kullanarak yeni işlev uygulamasına dağıtır.

Dağıtım tamamlandıktan sonra, işlev uygulaması uç noktalarınıza erişmek için kullanabileceğiniz URL 'YI görürsünüz. Yayımladığımız HTTP tetikleyicisi `authLevel = AuthorizationLevel.FUNCTION` ' ı kullandığından, işlev uç noktasını HTTP üzerinden çağırmak için işlev anahtarını almanız gerekir. İşlev anahtarını almanın en kolay yolu [Azure portalda].

## <a name="get-the-http-trigger-url"></a>HTTP tetikleyici URL 'sini al

<!--- We can updates this to remove portal dependency after the Maven archetype returns the full URLs with keys on publish (https://github.com/microsoft/azure-maven-plugins/issues/571). -->

İşlevinizi tetiklemeniz için gereken URL 'yi, Azure portal işlev anahtarıyla alabilirsiniz. 

1. [Azure portalda]gidin, oturum açın, sayfanın üst kısmında **arama** yapmak için işlev uygulamanızın _appname_ öğesini yazın ve ENTER tuşuna basın.
 
1. İşlev uygulamanızda Işlevler ' i genişletin **(salt okunurdur)** , işlevinizi seçin, sonra **</> Işlev URL 'sini al** ' ı seçin. 

    ![Azure portalından işlev URL’sini kopyalama](./media/functions-create-java-maven/get-function-url-portal.png)

1. **Varsayılan (işlev anahtarı)** öğesini seçin ve **Kopyala**' yı seçin. 

Şimdi, işlevinizi erişmek için kopyalanmış URL 'YI kullanabilirsiniz.

## <a name="verify-the-function-in-azure"></a>Azure 'da işlevi doğrulama

@No__t-0 kullanarak Azure 'da çalışan işlev uygulamasını doğrulamak için, aşağıdaki örnekteki URL 'YI portaldan kopyaladığınız URL ile değiştirin.

```azurecli
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Bu, istek gövdesinde `AzureFunctions` ile işlev uç noktasına bir POST isteği gönderir. Aşağıdaki yanıtı görürsünüz.

```Output
Hello AzureFunctions!
```

## <a name="next-steps"></a>Sonraki adımlar

HTTP ile tetiklenen bir işlev içeren bir Java işlevleri projesi oluşturdunuz, yerel makinenizde çalıştırın ve Azure 'a dağıttınız. Şimdi, işlevinizi genişletin...

> [!div class="nextstepaction"]
> [Azure depolama kuyruğu çıkış bağlaması ekleme](functions-add-output-binding-storage-queue-java.md)


[Azure CLI]: /cli/azure
[Azure portalda]: https://portal.azure.com