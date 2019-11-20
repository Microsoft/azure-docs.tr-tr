---
title: Linux 'ta Java Web uygulaması oluşturma-Azure App Service
description: Bu hızlı başlangıçta, Linux üzerindeki Azure App Service’te ilk Java Merhaba Dünya uygulamanızı birkaç dakika içinde dağıtacaksınız.
keywords: Azure, App Service, Web uygulaması, Linux, Java, Maven, hızlı başlangıç
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 3b011d3d7dc881d44fdcafb29efacf9548866d7a
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747738"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-linux"></a>Hızlı başlangıç: Linux üzerinde Azure App Service Java uygulaması oluşturma

[Linux üzerindeki App Service](app-service-linux-intro.md) Linux işletim sistemini kullanan yüksek oranda ölçeklenebilir, otomatik olarak düzeltme eki uygulayan bir web barındırma hizmeti sağlar. Bu hızlı başlangıçta, Linux işletim sisteminde Java Web arşivi (WAR) dosyasını dağıtmak üzere [Azure App Service Için Maven eklentisi](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) Ile [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 'nın nasıl kullanılacağı gösterilmektedir.

> [!NOTE]
>
> Aynı şey, IntelliJ ve tutulma gibi popüler Ides 'ler kullanılarak da yapılabilir. [Azure Toolkit for IntelliJ hızlı](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) başlangıç veya [Azure Toolkit for Eclipse hızlı başlangıç](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app)aşamasında benzer belgelerimize göz atın.
>
![Azure App Service 'de çalışan örnek uygulama](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Java uygulaması oluşturma

`helloworld`adlı yeni bir uygulama oluşturmak için Cloud Shell isteminde aşağıdaki Maven komutunu yürütün:

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp
```

## <a name="configure-the-maven-plugin"></a>Maven eklentisini yapılandırma

Azure App Service dağıtım işlemi, Azure CLı 'den hesap kimlik bilgilerini kullanır. Devam etmeden önce [Azure CLI Ile oturum açın](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) .

```azurecli
az login
```

Ardından, dağıtımı yapılandırabilir, komut Isteminde Maven komutunu `mvn com.microsoft.azure:azure-webapp-maven-plugin:1.8.0:config` çalıştırabilir ve **Onayla (Y/N)** istemi görüntüleninceye ve ardından **' Y '** tuşuna basarak ve yapılandırma yapıldıktan sonra **ENTER** tuşuna basarak varsayılan yapılandırmaları kullanabilirsiniz.

```cmd
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.8.0:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.8.0:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use:
Define value for javaVersion(Default: jre8):
1. Java 11
2. Java 8 [*]
Enter index to use:
Define value for runtimeStack(Default: TOMCAT 8.5):
1. TOMCAT 9.0
2. TOMCAT 8.5 [*]
3. WILDFLY 14
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1558400876966
ResourceGroup : helloworld-1558400876966-rg
Region : westeurope
PricingTier : Premium_P1V2
OS : Linux
RuntimeStack : TOMCAT 8.5-jre8
Deploy to slot : false
Confirm (Y/N)? : Y
```

> [!NOTE]
> Bu makalede yalnızca WAR dosyalarıyla paketlenmiş Java uygulamalarıyla çalışacağız. Eklenti ayrıca JAR web uygulamalarını da destekler. Denemek için [Linux'ta App Service'e Java SE JAR dosyası dağıtma](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

Eklenti yapılandırmasının güncelleştirildiğini görmek için `pom.xml` tekrar gidin, gerekirse doğrudan Pod dosyanızda App Service için diğer yapılandırmaları değiştirebilirsiniz, bazı yaygın olanlar aşağıda listelenmiştir:

 Özellik | Gerekli | Açıklama | Sürüm
---|---|---|---
`<schemaVersion>` | false | Yapılandırma şemasının sürümünü belirtin. Desteklenen değerler şunlardır: `v1`, `v2`. | 1.5.2 planlama
`<resourceGroup>` | doğru | Web uygulamanız için Azure Kaynak grubu. | 0.1.0 +
`<appName>` | doğru | Web uygulamanızın adı. | 0.1.0 +
[`<region>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region) | doğru | Web uygulamanızın barındırılacak bölgeyi belirtir; Varsayılan değer **westeurope**' dir. [Desteklenen bölgelerde](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region) tüm geçerli bölgeler bölümü. | 0.1.0 +
[`<pricingTier>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme##pricingtier) | false | Web uygulamanız için fiyatlandırma katmanı. Varsayılan değer **P1V2**' dir.| 0.1.0 +
[`<runtime>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting) | doğru | Çalışma zamanı ortamı yapılandırması, [burada](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting)ayrıntıları görebilirsiniz. | 0.1.0 +
[`<deployment>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting) | doğru | Dağıtım yapılandırması, ayrıntıları [burada](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting)görebilirsiniz. | 0.1.0 +

## <a name="deploy-the-app"></a>Uygulamayı dağıtma

Aşağıdaki komutu kullanarak Java uygulamanızı Azure'a dağıtın:

```bash
mvn package azure-webapp:deploy
```

Dağıtım tamamlandıktan sonra, web tarayıcınızda aşağıdaki URL’yi kullanarak dağıtılan uygulamanın konumuna gidin; örneğin `http://<webapp>.azurewebsites.net`. 

![Azure App Service 'de çalışan örnek uygulama](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Tebrikler!** Linux üzerinde App Service’e ilk Java uygulamanızı dağıttınız.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Önceki adımlarda, bir kaynak grubunda Azure kaynakları oluşturdunuz. Bu kaynakların gelecekte gerekli olacağını düşünmüyorsanız, Cloud Shell’de aşağıdaki komutu çalıştırarak kaynak grubunu silin:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Bu komutun çalıştırılması bir dakika sürebilir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: PostgreSQL ile Java Enterprise uygulaması](tutorial-java-enterprise-postgresql-app.md)

> [!div class="nextstepaction"]
> [Java uygulamasını yapılandırma](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Jenkins ile CI/CD](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Diğer Java geliştiricileri için Azure kaynakları](/java/azure/)
