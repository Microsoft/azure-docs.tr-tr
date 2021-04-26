---
title: Kaynak koddan Spring Cloud uygulamanızı başlatma
description: Bu hızlı başlangıçta, Azure Spring Cloud uygulamanızı doğrudan kaynak kodınızdan nasıl başlatacağınızı öğrenin
author: MikeDodaro
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/03/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: a6710a15bd0637eead0051ebb70a7cdd8bb8aa58
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104879365"
---
# <a name="how-to-launch-your-spring-cloud-application-from-source-code"></a>Kaynak koddan Spring Cloud uygulamanızı başlatma

**Bu makale şu şekilde geçerlidir:** ✔️ Java

Azure Spring Cloud, Azure üzerinde bulut tabanlı mikro hizmet uygulamalarına izin verebilir.

Uygulamaları doğrudan Java kaynak kodundan veya önceden oluşturulmuş bir JAR içinden başlatabilirsiniz. Bu makalede dağıtım yordamları açıklanmaktadır.

Bu hızlı başlangıçta nasıl yapılacağı açıklanmaktadır:

> [!div class="checklist"]
> * Hizmet örneği sağlama
> * Bir örnek için yapılandırma sunucusu ayarlama
> * Yerel olarak bir mikro Hizmetler uygulaması oluşturma
> * Her mikro hizmeti dağıtma
> * Uygulamanız için genel uç nokta atama

## <a name="prerequisites"></a>Önkoşullar
Başlamadan önce, Azure aboneliğinizin gerekli bağımlılıklara sahip olduğundan emin olun:

1. [Git'i yükleyin](https://git-scm.com/)
2. [JDK 8 ' i yükler](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Maven 3,0 veya üstünü yükler](https://maven.apache.org/download.cgi)
4. [Azure CLI'yi yükleme](/cli/azure/install-azure-cli)
5. [Azure aboneliğine kaydolma](https://azure.microsoft.com/free/)

> [!TIP]
> Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur.  Git, JDK, Maven ve Azure CLı 'nin en son sürümleri de dahil olmak üzere önceden yüklenmiş ortak Azure araçları vardır. Azure aboneliğinizde oturum açtıysanız, shell.azure.com adresinden [Azure Cloud Shell](https://shell.azure.com) başlatın.  [Belgelerimizi okuyarak](../cloud-shell/overview.md) Azure Cloud Shell hakkında daha fazla bilgi edinebilirsiniz

## <a name="install-the-azure-cli-extension"></a>Azure CLı uzantısını yükler

Aşağıdaki komutla Azure CLı için Azure yay bulutu uzantısını yükler

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Azure CLı kullanarak bir hizmet örneği sağlama

Azure CLı 'da oturum açın ve etkin aboneliğinizi seçin. 

```azurecli
az login
az account list -o table
az account set --subscription
```

Azure yay bulut hizmetinizi içeren bir kaynak grubu oluşturun. [Azure Kaynak grupları](../azure-resource-manager/management/overview.md)hakkında daha fazla bilgi edinebilirsiniz.

```azurecli
az group create --location eastus --name <resource group name>
```

Azure Spring Cloud 'ın bir örneğini sağlamak için aşağıdaki komutları çalıştırın. Azure yay bulut hizmetiniz için bir ad hazırlayın. Ad 4 ile 32 karakter arasında olmalıdır ve yalnızca küçük harf, sayı ve kısa çizgi içerebilir. Hizmet adının ilk karakteri bir harf olmalıdır ve son karakter bir harf ya da sayı olmalıdır.

```azurecli
az spring-cloud create -n <resource name> -g <resource group name>
```

Hizmet örneğinin dağıtılması yaklaşık beş dakika sürer.

Aşağıdaki komutları kullanarak varsayılan kaynak grubu adınızı ve Azure Spring Cloud örnek adınızı ayarlayın:

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

## <a name="create-the-azure-spring-cloud-application"></a>Azure Spring Cloud uygulamasını oluşturma

Aşağıdaki komut, aboneliğinizde bir Azure Spring Cloud uygulaması oluşturur.  Bu, uygulamamızı karşıya yükleyebilmemiz için boş bir hizmet oluşturur.

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>Spring Cloud uygulamanızı dağıtma

Uygulamanızı önceden oluşturulmuş bir kaya ya da Gradle veya Maven deposundan dağıtabilirsiniz.  Aşağıdaki her bir durum için yönergeler bulun.

### <a name="deploy-a-built-jar"></a>Oluşturulmuş bir JAR dağıtma

Yerel makinenize oluşturulmuş bir JAR 'ten dağıtım yapmak için, derlemenizi bir [FAT-jar](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven)ürettiğinden emin olun.

FAT-JAR öğesini etkin bir dağıtıma dağıtmak için

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR e.g. "target\hellospring-0.0.1-SNAPSHOT.jar">
```

FAT-JAR dosyasını belirli bir dağıtıma dağıtmak için

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-fat-JAR e.g. "target\hellospring-0.0.1-SNAPSHOT.jar">
```

### <a name="deploy-from-source-code"></a>Kaynak koddan dağıt

Azure Spring Cloud, projeyi derlemek için [kpack](https://github.com/pivotal/kpack) kullanır.  Azure CLı kullanarak kaynak kodunuzu karşıya yükleyebilir, kpack kullanarak projenizi oluşturabilir ve hedef uygulamaya dağıtabilirsiniz.

> [!WARNING]
> Proje, içinde girişi olan yalnızca bir JAR dosyası üretmelidir `main-class` `MANIFEST.MF` `target` (Maven dağıtımları için) veya `build/libs` (Gradle dağıtımları için).  Girişleri olan birden çok JAR dosyası `main-class` dağıtımın başarısız olmasına neden olur.

Tek modüllü Maven/Gradle projeleri için:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

Birden çok modüllü Maven/Gradle projeleri için her modül için yineleyin:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>Dağıtım günlüklerini göster

Aşağıdaki komutu kullanarak kpack Derleme günlüklerini gözden geçirin:

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> Kpack günlükleri yalnızca, söz konusu dağıtım kpack kullanılarak kaynaktan oluşturulduysa en son dağıtımı gösterir.

## <a name="assign-a-public-endpoint-to-gateway"></a>Ağ geçidine genel uç nokta atama

1. **Uygulama panosu** sayfasını açın.
2. `gateway` **Uygulama ayrıntıları** sayfasını göstermek için uygulamayı seçin.
3. Ağ geçidine genel bir uç nokta atamak için **uç nokta ata** ' yı seçin. Bu işlem birkaç dakika sürebilir. 
4. Çalışan uygulamanızı görüntülemek için tarayıcınıza atanan ortak IP 'yi girin.

> [!div class="nextstepaction"]
> [Bir sorunla karşılaştım](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta şunları öğrendiniz:

> [!div class="checklist"]
> * Hizmet örneği sağlama
> * Bir örnek için yapılandırma sunucusu ayarlama
> * Yerel olarak bir mikro Hizmetler uygulaması oluşturma
> * Her mikro hizmeti dağıtma
> * Uygulamalar için ortam değişkenlerini düzenleme
> * Uygulama ağ geçidiniz için genel IP atama

> [!div class="nextstepaction"]
> [Yay bulut günlükleri, ölçümler, izleme](spring-cloud-quickstart-logs-metrics-tracing.md)

GitHub 'da daha fazla örnek vardır: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).