---
title: Windows 'de Java Web uygulaması oluşturma-Azure App Service
description: Bu hızlı başlangıçta, Windows 'da Azure App Service ilk Java Merhaba Dünya dakikalar içinde dağıtırsınız.
keywords: Azure, App Service, Web uygulaması, Windows, Java, Maven, hızlı başlangıç
services: app-service\web
documentationcenter: ''
author: msangapu-msft
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: d119ffdcb952405f17e0df152c817df9a8a63110
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73833638"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-windows"></a>Hızlı başlangıç: Windows üzerinde Azure App Service Java uygulaması oluşturma

> [!NOTE]
> Bu makalede bir uygulamanın Windows üzerinde App Service'e dağıtımı yapılır. _Linux_üzerinde App Service dağıtmak için bkz. [Linux üzerinde Java Web uygulaması oluşturma](./containers/quickstart-java.md).
>

[Azure App Service](overview.md), yüksek oranda ölçeklenebilen, kendi kendine düzeltme eki uygulayan bir web barındırma hizmeti sunar.  Bu hızlı başlangıçta, Java Web arşivi (WAR) dosyasını dağıtmak üzere [Azure App Service Için Maven eklentisi](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) Ile [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 'nın nasıl kullanılacağı gösterilmektedir.

> [!NOTE]
> Aynı şey, IntelliJ ve tutulma gibi popüler Ides 'ler kullanılarak da yapılabilir. [Azure Toolkit for IntelliJ hızlı](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) başlangıç veya [Azure Toolkit for Eclipse hızlı başlangıç](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app)aşamasında benzer belgelerimize göz atın.
>
![Azure App Service 'de çalışan örnek uygulama](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Java uygulaması oluşturma

`helloworld`adlı yeni bir uygulama oluşturmak için Cloud Shell isteminde aşağıdaki Maven komutunu yürütün:

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp
```

## <a name="configure-the-maven-plugin"></a>Maven eklentisini yapılandırma

Maven'dan dağıtım için, Cloud Shell'deki kod düzenleyiciyi kullanarak `pom.xml` dizinindeki proje `helloworld` dosyasını açın. 

```bash
code pom.xml
```

Sonra `<build>` dosyasının `pom.xml` öğesinin içine aşağıdaki eklenti tanımını ekleyin.

```xml
<plugins>
    <!--*************************************************-->
    <!-- Deploy to Tomcat in App Service Windows         -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.8.0</version>
        <configuration>
            <!-- Specify v2 schema -->
            <schemaVersion>v2</schemaVersion>
            <!-- App information -->
            <subscriptionId>SUBSCRIPTION_ID</subscriptionId>
            <resourceGroup>RESOURCEGROUP_NAME</resourceGroup>
            <appName>WEBAPP_NAME</appName>
            <region>REGION</region>
            <!-- Java Runtime Stack for App Service on Windows-->
            <runtime>
                <os>windows</os>
                <javaVersion>1.8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>
            <deployment>
                <resources>
                    <resource>
                        <directory>${project.basedir}/target</directory>
                        <includes>
                            <include>*.war</include>
                        </includes>
                    </resource>
                </resources>
            </deployment>
        </configuration>
    </plugin>
</plugins>
```

> [!NOTE]
> Bu makalede yalnızca WAR dosyalarıyla paketlenmiş Java uygulamalarıyla çalışacağız. Eklenti ayrıca JAR web uygulamalarını da destekler. Denemek için [Linux'ta App Service'e Java SE JAR dosyası dağıtma](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).


Eklenti yapılandırmasında aşağıdaki yer tutucuları güncelleştirin:

| Yer tutucu | Açıklama |
| ----------- | ----------- |
| `SUBSCRIPTION_ID` | Uygulamanızı dağıtmak istediğiniz aboneliğin benzersiz KIMLIĞI. Varsayılan aboneliğin KIMLIĞI, `az account show` komutu kullanılarak Cloud Shell veya CLı tarafından bulunabilir. Tüm kullanılabilir abonelikler için `az account list` komutunu kullanın.|
| `RESOURCEGROUP_NAME` | Uygulamanızın oluşturulacağı yeni kaynak grubunun adı. Uygulamanın tüm kaynaklarını bir gruba koyarak birlikte yönetebilirsiniz. Örneğin, kaynak grubunu sildiğinizde uygulamayla ilişkili tüm kaynaklar da silinir. Bu değeri, benzersiz bir yeni kaynak grubu adı (örneğin, *Myresourcegroup*) ile güncelleştirin. Bu kaynak grubunu daha sonraki bir bölümde tüm Azure kaynaklarını temizlemek için kullanacaksınız. |
| `WEBAPP_NAME` | Uygulama adı, Azure 'a dağıtıldığında uygulamanın ana bilgisayar adının bir parçası olacaktır (WEBAPP_NAME. azurewebsites. net). Bu değeri, Java uygulamanızı barındıracak yeni App Service uygulaması için benzersiz bir adla güncelleştirin. Örneğin, *contoso*. |
| `REGION` | Uygulamanın barındırıldığı bir Azure bölgesi; Örneğin, *westus2*. Cloud Shell'den veya CLI'dan `az account list-locations` komutunu kullanarak bölgelerin bir listesini alabilirsiniz. |

## <a name="deploy-the-app"></a>Uygulamayı dağıtma

Aşağıdaki komutu kullanarak Java uygulamanızı Azure'a dağıtın:

```bash
mvn package azure-webapp:deploy
```

Dağıtım tamamlandıktan sonra, web tarayıcınızda aşağıdaki URL’yi kullanarak dağıtılan uygulamanın konumuna gidin; örneğin `http://<webapp>.azurewebsites.net/`.

![Azure App Service 'de çalışan örnek uygulama](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

**Tebrikler!** Windows üzerinde App Service için ilk Java uygulamanızı dağıttınız.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Java geliştiricileri için Azure kaynakları](/java/azure/)

> [!div class="nextstepaction"]
> [Özel etki alanı eşleme](app-service-web-tutorial-custom-domain.md)
