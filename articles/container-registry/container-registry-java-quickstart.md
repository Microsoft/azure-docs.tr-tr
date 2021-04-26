---
title: Hızlı başlangıç-Maven ve JIB kullanarak Azure Container Registry Java kapsayıcı görüntülerini derleyin ve gönderin
description: Kapsayıcılı bir Java uygulaması oluşturun ve Maven JIB eklentisini kullanarak Azure Container Registry gönderin.
author: KarlErickson
ms.author: karler
ms.date: 02/26/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
- mode-api
ms.openlocfilehash: 4d805458d90c73de879a9b87d5b08c98a8f1a250
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537304"
---
# <a name="quickstart-build-and-push-java-container-images-to-azure-container-registry"></a>Hızlı başlangıç: Java kapsayıcı görüntülerini derleyin ve Azure Container Registry gönderin

Bu hızlı başlangıçta, kapsayıcılı bir Java uygulamasının nasıl oluşturulacağı ve Maven JIB eklentisini kullanarak Azure Container Registry nasıl göndergörüntüleneceği gösterilmektedir. Maven ve JIB kullanımı, bir Azure Container Registry ile etkileşim kurmak için geliştirici araçları kullanmanın bir örneğidir.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği; henüz Azure aboneliğiniz yoksa [MSDN abone avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) etkinleştirebilir veya [ücretsiz Azure hesabı](https://azure.microsoft.com/pricing/free-trial) için kaydolabilirsiniz.
* [Azure Command-Line arabirimi (CLI)](/cli/azure/overview).
* Desteklenen bir Java Development Kit (JDK). Azure’da geliştirme sırasında kullanılabilecek JDK’ler hakkında daha fazla bilgi için bkz. <https://aka.ms/azure-jdks>.
* Apache [Maven](http://maven.apache.org) derleme aracı (sürüm 3 veya üzeri).
* [Git](https://git-scm.com) istemcisi.
* [Docker](https://www.docker.com) istemcisi.
* [ACR Docker kimlik bilgisi Yardımcısı](https://github.com/Azure/acr-docker-credential-helper).

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Docker üzerinde Spring Boot'u Kullanmaya Başlama web uygulamasını oluşturma

Aşağıdaki adımlar, bir Spring Boot web uygulaması oluşturma ve yerel olarak test etme sürecinde size yol gösterecektir.

1. Komut isteminden, [Docker Başlarken örnek projesinde Spring Boot](https://github.com/spring-guides/gs-spring-boot-docker) 'ı kopyalamak için aşağıdaki komutu kullanın.

   ```bash
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Tamamlanmış projenin dizinine geçin.

   ```bash
   cd gs-spring-boot-docker/complete
   ```

1. Örnek uygulamayı derlemek ve çalıştırmak için Maven'ı kullanın.

   ```bash
   mvn package spring-boot:run
   ```

1. `http://localhost:8080` adresine giderek veya aşağıdaki `curl` komutunu kullanarak web uygulamasını test edin:

   ```bash
   curl http://localhost:8080
   ```

Aşağıdaki iletinin görüntülendiğini görmeniz gerekir: **Merhaba Docker World**

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Azure CLI’yi kullanarak Azure Container Registry oluşturma

Ardından, aşağıdaki adımları kullanarak bir Azure Kaynak grubu ve ACR 'nizi oluşturacaksınız:

1. Aşağıdaki komutu kullanarak Azure hesabınızda oturum açın:

   ```azurecli
   az login
   ```

1. Kullanılacak Azure aboneliğini belirtin:

   ```azurecli
   az account set -s <subscription ID>
   ```

1. Bu öğreticide kullanılacak Azure kaynakları için bir kaynak grubu oluşturun. Aşağıdaki komutta, yer tutucuları kendi kaynak adınızla ve gibi bir konum ile değiştirdiğinizden emin olun `eastus` .

   ```azurecli
   az group create \
       --name=<your resource group name> \
       --location=<location>
   ```

1. Aşağıdaki komutu kullanarak kaynak grubunda özel bir Azure Container Registry oluşturun. Yer tutucuları gerçek değerlerle değiştirdiğinizden emin olun. Bu öğreticinin ilerleyen adımlarında örnek uygulama Docker görüntüsü olarak bu kayıt defterine gönderilecektir.

   ```azurecli
   az acr create \
       --resource-group <your resource group name> \
       --location <location> \
       --name <your registry name> \
       --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>Uygulamanızı Jib aracılığıyla kapsayıcı kayıt defterine gönderme

Son olarak, proje yapılandırmanızı güncelleyebilir ve görüntünüzü derlemek ve dağıtmak için komut istemi ' ni kullanabilirsiniz.

> [!NOTE]
> Yeni oluşturduğunuz Azure Container Registry 'de oturum açmak için Docker Daemon ' ın çalışıyor olması gerekir. Makinenize Docker yüklemek için [resmi Docker belgeleri burada bulabilirsiniz](https://docs.docker.com/install/).

1. Aşağıdaki komutu kullanarak Azure CLı 'dan Azure Container Registry oturum açın. Yer tutucusunu kendi kayıt defteri adınızla değiştirdiğinizden emin olun.

   ```azurecli
   az configure --defaults acr=<your registry name>
   az acr login
   ```

   `az configure`Komut, komutlarla kullanılacak varsayılan kayıt defteri adını ayarlar `az acr` .

1. Spring Boot uygulamanızın tamamlanmış proje dizinine (örneğin, "*C:\SpringBoot\gs-spring-boot-docker\complete*" veya "*/users/robert/SpringBoot/gs-spring-boot-docker/complete*") gidin ve *pom.xml* dosyasını metin düzenleyicide açın.

1. `<properties>` *pom.xml* DOSYASıNDAKI koleksiyonu aşağıdaki XML ile güncelleştirin. Yer tutucuyu kayıt defteri adınızla değiştirin ve `<jib-maven-plugin.version>` değeri ile bir özellik `2.2.0` veya [JIB-Maven-Plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin)' in daha yeni bir sürümünü ekleyin.

   ```xml
   <properties>
      <docker.image.prefix><your registry name>.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <jib-maven-plugin.version>2.2.0</jib-maven-plugin.version>
   </properties>
   ```

1. `<plugins>`Aşağıdaki örnekte gösterildiği gibi, *pom.xml* dosyasındaki koleksiyonu, `<plugin>` öğesinin öğesini içermesi ve için girişi olacak şekilde güncelleştirin `jib-maven-plugin` . `mcr.microsoft.com/java/jdk:8-zulu-alpine`Azure için resmi olarak desteklenen BIR JDK Içeren Microsoft Container Registry (MCR) ' den bir temel görüntü kullandığınızı unutmayın. Resmi olarak desteklenen JDKs 'e sahip diğer MCR temel görüntüleri için bkz. [Java ve JDK](https://hub.docker.com/_/microsoft-java-jdk), [Java, JRE](https://hub.docker.com/_/microsoft-java-jre), [Java, gözetimsiz JRE](https://hub.docker.com/_/microsoft-java-jre-headless)ve [Java, JDK ve Maven](https://hub.docker.com/_/microsoft-java-maven).

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>${jib-maven-plugin.version}</version>
     <configuration>
        <from>
            <image>mcr.microsoft.com/java/jdk:8-zulu-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
        </to>
     </configuration>
   </plugin>
   ```

1. Spring Boot uygulamanız için tamamlanmış proje dizinine gidin ve aşağıdaki komutu çalıştırarak görüntüyü oluşturun ve kayıt defterine gönderin:

   ```bash
   az acr login && mvn compile jib:build
   ```

> [!NOTE]
>
> Güvenlik nedenleriyle, tarafından oluşturulan kimlik bilgileri `az acr login` yalnızca 1 saat için geçerlidir. *401 Yetkisiz* bir hata alırsanız, `az acr login -n <your registry name>` yeniden kimlik doğrulaması yapmak için komutu tekrar çalıştırabilirsiniz.

## <a name="verify-your-container-image"></a>Kapsayıcı görüntünüzü doğrulama

Tebrikler! Artık Azure 'da bulunan ve Azure 'da bulunan ve ACR 'nize gönderilen Azure 'da Kapsayıcılı Java uygulama derlemeniz vardır. Artık görüntüyü Azure App Service veya komutu ile yerel (yer tutucuyu değiştirme) ile çekerek test edebilirsiniz:

```bash
docker pull <your registry name>.azurecr.io/gs-spring-boot-docker
```

## <a name="next-steps"></a>Sonraki adımlar

Resmi Microsoft tarafından desteklenen Java temel görüntülerinin diğer sürümleri için bkz.:

* [Java & JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [Java, JRE](https://hub.docker.com/_/microsoft-java-jre)
* [Java, gözetimsiz JRE](https://hub.docker.com/_/microsoft-java-jre-headless)
* [Java, JDK ve Maven](https://hub.docker.com/_/microsoft-java-maven)

Spring ve Azure hakkında daha fazlasını öğrenmek için Azure’da Spring belge merkezinde çalışmaya devam edin.

> [!div class="nextstepaction"]
> [Azure’da Spring](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Ek Kaynaklar

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Java geliştiricileri için Azure](/azure/java)
* [Azure DevOps ve Java ile çalışma](/azure/devops/java)
* [Docker üzerinde Spring Boot'u Kullanmaya Başlama](https://spring.io/guides/gs/spring-boot-docker)
* [Spring Initializr](https://start.spring.io)
* [Azure App Service’e Spring Boot Uygulaması Dağıtma](/azure/developer/java/spring-framework/deploy-spring-boot-java-app-on-linux#configure-maven-to-build-image-to-your-azure-container-registry)
* [Linux üzerinde Azure Web App’e yönelik özel Docker görüntüsü kullanma](../app-service/tutorial-custom-container.md)
