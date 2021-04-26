---
title: 'Hızlı başlangıç: Azure App Service Java uygulaması oluşturma'
description: İlk Java Merhaba Dünya Azure App Service dakikalar içinde dağıtın. Maven için Azure Web uygulaması eklentisi, Java uygulamalarını dağıtmayı kullanışlı hale getirir.
keywords: Azure, App Service, Web uygulaması, Windows, Linux, Java, Maven, hızlı başlangıç
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-java-uiex
ms.openlocfilehash: ebd189e1cf6e053f5400b8217fc1c2fc385cdac9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701731"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Hızlı başlangıç: Azure App Service Java uygulaması oluşturma

[Azure App Service](overview.md), yüksek oranda ölçeklenebilen, kendi kendine düzeltme eki uygulayan bir web barındırma hizmeti sunar.  Bu hızlı başlangıçta, bir. jar dosyası veya. war dosyası dağıtmak üzere [Maven için Azure Web App eklentisi](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) Ile [Azure CLI](/cli/azure/get-started-with-azure-cli) 'nın nasıl kullanılacağı gösterilmektedir. Java & Tomcat yönergeleri arasında geçiş yapmak için sekmeleri kullanın.


> [!NOTE]
> Aynı zamanda IntelliJ ve tutulma gibi popüler Ides 'ler kullanılarak da yapılabilir. [Azure Toolkit for IntelliJ hızlı](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) başlangıç veya [Azure Toolkit for Eclipse hızlı başlangıç](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app)aşamasında benzer belgelerimize göz atın.


![Azure App Service 'de çalışan örnek uygulama](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Java uygulaması oluşturma

# <a name="java-se"></a>[Java SE](#tab/javase)

[Spring Boot Başlarken](https://github.com/spring-guides/gs-spring-boot) örnek projesini klonlayın.

```bash
git clone https://github.com/spring-guides/gs-spring-boot
```

Tamamlanmış projenin dizinine geçin.

```bash
cd gs-spring-boot/complete
```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

Aşağıdaki Maven komutunu Cloud Shell istemine yürütün, adlı yeni bir uygulama oluşturun `helloworld` :

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

Sonra çalışma dizininizi proje klasörü olarak değiştirin:

```bash
cd helloworld
```

---

## <a name="configure-the-maven-plugin"></a>Maven eklentisini yapılandırma

Azure App Service’e dağıtma sürecinde, otomatik olarak Azure CLI’den alınan Azure kimlikleriniz kullanılacaktır. Azure CLı yerel olarak yüklü değilse, Maven eklentisi OAuth veya cihaz oturum açma bilgileriyle kimlik doğrulaması yapılır. Daha fazla bilgi için bkz. [Maven eklentileri ile kimlik doğrulama](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

Dağıtımı yapılandırmak için aşağıdaki Maven komutunu çalıştırın. Bu komut, App Service işletim sistemini, Java sürümünü ve Tomcat sürümünü ayarlamanıza yardımcı olur.

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. **Abonelik** seçeneğiyle istendiğinde, satır başındaki numarayı girerek uygun `Subscription` öğesini seçin.
1. **Web uygulaması** seçeneğinde sorulduğunda, `<create>` ENTER tuşuna basarak veya var olan bir uygulamayı seçerek, defalt seçeneğini kabul edin.
1. **Işletim sistemi** seçeneğinde sorulduğunda, girerek **Windows** ' u seçin `3` .
1. **Fiyatlandırma katmanı** seçeneği ile sorulduğunda, girerek **B2** ' ı seçin `2` .
1. ENTER tuşuna basarak Java **8** varsayılan Java sürümünü kullanın.
1. Son olarak, seçimlerinizi onaylamak için son istemde Enter tuşuna basın.

    Özet çıktınızı aşağıda gösterilen kod parçacığına benzer şekilde görünecektir.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007390755
    ResourceGroup : spring-boot-1599007390755-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : java 8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 41.118 s
    [INFO] Finished at: 2020-09-01T17:43:45-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. **Abonelik** seçeneğiyle istendiğinde, satır başındaki numarayı girerek uygun `Subscription` öğesini seçin.
1. **Web uygulaması** seçeneğinde sorulduğunda, `<create>` ENTER tuşuna basarak veya var olan bir uygulamayı seçerek, defalt seçeneğini kabul edin.
1. **Işletim sistemi** seçeneğinde sorulduğunda, girerek **Windows** ' u seçin `3` .
1. **Fiyatlandırma katmanı** seçeneği ile sorulduğunda, girerek **B2** ' ı seçin `2` .
1. ENTER tuşuna basarak Java **8** varsayılan Java sürümünü kullanın.
1. ENTER tuşuna basarak, **Tomcat 8,5** varsayılan Web kapsayıcısını kullanın.
1. Son olarak, seçimlerinizi onaylamak için son istemde Enter tuşuna basın.

    Özet çıktınızı aşağıda gösterilen kod parçacığına benzer şekilde görünecektir.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003152123
    ResourceGroup : helloworld-1599003152123-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 03:03 min
    [INFO] Finished at: 2020-09-01T16:35:30-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="java-se"></a>[Java SE](#tab/javase)

1. **Abonelik** seçeneğiyle istendiğinde, satır başındaki numarayı girerek uygun `Subscription` öğesini seçin.
1. **Web uygulaması** seçeneğinde sorulduğunda, `<create>` ENTER tuşuna basarak veya var olan bir uygulamayı seçerek, defalt seçeneğini kabul edin.
1. **Işletim sistemi** seçeneğinde sorulduğunda, ENTER tuşuna basarak **Linux** ' u seçin.
1. **Fiyatlandırma katmanı** seçeneği ile sorulduğunda, girerek **B2** ' ı seçin `2` .
1. ENTER tuşuna basarak Java **8** varsayılan Java sürümünü kullanın.
1. Son olarak, seçimlerinizi onaylamak için son istemde Enter tuşuna basın.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007116351
    ResourceGroup : spring-boot-1599007116351-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : JAVA 8-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 20.925 s
    [INFO] Finished at: 2020-09-01T17:38:51-07:00
    [INFO] ------------------------------------------------------------------------
    ```

### <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. **Abonelik** seçeneğiyle istendiğinde, satır başındaki numarayı girerek uygun `Subscription` öğesini seçin.
1. **Web uygulaması** seçeneğinde sorulduğunda, `<create>` ENTER tuşuna basarak veya var olan bir uygulamayı seçerek, defalt seçeneğini kabul edin.
1. **Işletim sistemi** seçeneğinde sorulduğunda, ENTER tuşuna basarak **Linux** ' u seçin.
1. **Fiyatlandırma katmanı** seçeneği ile sorulduğunda, girerek **B2** ' ı seçin `2` .
1. ENTER tuşuna basarak Java **8** varsayılan Java sürümünü kullanın.
1. ENTER tuşuna basarak, **Tomcat 8,5** varsayılan Web kapsayıcısını kullanın.
1. Son olarak, seçimlerinizi onaylamak için son istemde Enter tuşuna basın.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003744223
    ResourceGroup : helloworld-1599003744223-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : TOMCAT 8.5-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 50.785 s
    [INFO] Finished at: 2020-09-01T16:43:09-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end

Gerekirse, App Service yapılandırmasını doğrudan ' de değiştirebilirsiniz `pom.xml` . Bazı yaygın olanlar aşağıda listelenmiştir:

Özellik | Gerekli | Açıklama | Sürüm
---|---|---|---
`<schemaVersion>` | yanlış | Yapılandırma şemasının sürümünü belirtin. Desteklenen değerler şunlardır: `v1` , `v2` . | 1.5.2
`<subscriptionId>` | yanlış | Abonelik kimliğini belirtin. | 0.1.0 +
`<resourceGroup>` | true | Web uygulamanız için Azure Kaynak grubu. | 0.1.0 +
`<appName>` | true | Web uygulamanızın adı. | 0.1.0 +
`<region>` | true | Web uygulamanızın barındırılacak bölgeyi belirtir; Varsayılan değer **westeurope**' dir. [Desteklenen bölgelerde](https://azure.microsoft.com/global-infrastructure/services/?products=app-service) tüm geçerli bölgeler bölümü. | 0.1.0 +
`<pricingTier>` | yanlış | Web uygulamanız için fiyatlandırma katmanı. Varsayılan değer, üretim iş yükü için **P1V2** , **B2** ise Java geliştirme ve test için önerilen en düşük gereksinimdir. [Daha fazla bilgi edinin](https://azure.microsoft.com/pricing/details/app-service/linux/)| 0.1.0 +
`<runtime>` | true | Çalışma zamanı ortamı yapılandırması, [burada](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details)ayrıntıları görebilirsiniz. | 0.1.0 +
`<deployment>` | true | Dağıtım yapılandırması, ayrıntıları [burada](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details)görebilirsiniz. | 0.1.0 +

`<appName>`Ve `<resourceGroup>` ( `helloworld-1590394316693` `helloworld-1590394316693-rg` demo olarak da buna göre) değerleri hakkında dikkatli olun, daha sonra kullanılır.

> [!div class="nextstepaction"]
> [Bir sorunla karşılaştım](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>Uygulamayı dağıtma

Maven eklentisi, uygulama hizmetlerine dağıtmak için Azure CLı 'den hesap kimlik bilgilerini kullanır. Devam etmeden önce [Azure CLI Ile oturum açın](/cli/azure/authenticate-azure-cli) .

```azurecli
az login
```

Ardından, aşağıdaki komutu kullanarak Java uygulamanızı Azure 'a dağıtabilirsiniz.

```bash
mvn package azure-webapp:deploy
```

Dağıtım tamamlandığında, uygulamanız `http://<appName>.azurewebsites.net/` sayfasında (tanıtımda `http://helloworld-1590394316693.azurewebsites.net` sayfasında) kullanıma hazır olur. Yerel Web tarayıcınızla URL 'yi açın, şunu görmeniz gerekir:

![Azure App Service 'de çalışan örnek uygulama](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Tebrikler!** App Service için ilk Java uygulamanızı dağıttınız.

> [!div class="nextstepaction"]
> [Bir sorunla karşılaştım](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Önceki adımlarda, bir kaynak grubunda Azure kaynakları oluşturdunuz. Bu kaynaklara gelecekte ihtiyaç duymayacağınızı düşünüyorsanız kaynak grubunu portaldan veya Cloud Shell’de şu komutu çalıştırarak silin:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Bu komutun çalıştırılması bir dakika sürebilir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Java ile PostgreSQL için Azure DB 'ye bağlanma](../postgresql/connect-java.md)

> [!div class="nextstepaction"]
> [CI/CD ayarlama](deploy-continuous-deployment.md)

> [!div class="nextstepaction"]
> [Fiyatlandırma bilgileri](https://azure.microsoft.com/pricing/details/app-service/linux/)

> [!div class="nextstepaction"]
> [Toplu Günlükler ve ölçümler](troubleshoot-diagnostic-logs.md)

> [!div class="nextstepaction"]
> [Ölçeği artırma](manage-scale-up.md)

> [!div class="nextstepaction"]
> [Java geliştiricileri için Azure kaynakları](/java/azure/)

> [!div class="nextstepaction"]
> [Java uygulamanızı yapılandırma](configure-language-java.md)
