---
title: "Hızlı başlangıç: Maven-Azure yay bulutu 'nı kullanarak bir uygulama başlatma"
description: Maven kullanarak örnek uygulama başlatma
author: jpconnock
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 01bf33c3ed50311b031e015b24566f5819ac0857
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123810"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>Hızlı başlangıç: Maven eklentisini kullanarak bir Azure Spring Cloud uygulaması başlatın

Azure Spring Cloud Maven eklentisini kullanarak Azure Spring Cloud uygulamalarınızı kolayca oluşturabilir ve güncelleştirebilirsiniz. Bir yapılandırmayı önceden tanımlayarak, mevcut Azure yay bulut hizmetinize uygulama dağıtabilirsiniz. Bu makalede, bu özelliği göstermek için POF ölçümleri adlı örnek bir uygulama kullanırsınız.

Bu hızlı başlangıcı izleyerek şunları nasıl yapacağınızı öğreneceksiniz:

> [!div class="checklist"]
> * Hizmet örneği sağlama
> * Bir örnek için yapılandırma sunucusu ayarlama
> * Mikro hizmetler uygulamasını yerel olarak kopyalama ve derleme
> * Her mikro hizmeti dağıtma
> * Uygulamanız için genel bir uç nokta atama

>[!Note]
> Azure yay bulutu Şu anda genel önizleme olarak sunulmaktadır. Genel Önizleme teklifleri, müşterilerin resmi sürümünden önceki yeni özelliklerle deneme yapmasına olanak tanır.  Genel Önizleme özellikleri ve Hizmetleri üretim kullanımı için tasarlanmamıştır.  Önizleme sırasında destek hakkında daha fazla bilgi için lütfen [SSS](https://azure.microsoft.com/support/faq/) veya dosya dosyası [destek isteği](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) inceleyerek daha fazla bilgi edinebilirsiniz.


>[!TIP]
> Azure Cloud Shell, bu makaledeki komutları çalıştırmak için kullanabileceğiniz, ücretsiz bir etkileşimli kabuktur. Git 'in en son sürümleri, Java Geliştirme Seti (JDK), Maven ve Azure CLı dahil olmak üzere önceden yüklenmiş ortak Azure araçları vardır. Azure aboneliğinizde oturum açtıysanız [Azure Cloud Shell](https://shell.azure.com)başlatın. Daha fazla bilgi için bkz. [Azure Cloud Shell Genel Bakış](../cloud-shell/overview.md).

Bu hızlı başlangıcı tamamlamak için:

1. [Git’i yükleyin](https://git-scm.com/).
2. [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)' i yükler.
3. [Maven 3,0 veya üstünü yükler](https://maven.apache.org/download.cgi).
4. [Azure CLI sürüm 2.0.67 veya üstünü yükler](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
5. [Ücretsiz bir Azure aboneliğine kaydolun](https://azure.microsoft.com/free/).

## <a name="install-the-azure-cli-extension"></a>Azure CLı uzantısını yükler

Aşağıdaki komutu kullanarak Azure CLı için Azure yay bulutu uzantısını yükler:

```Azure CLI
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Azure portal bir hizmet örneği sağlayın

1. Bir Web tarayıcısında, [Bu bağlantıyı Azure Portal Azure Spring Cloud 'a](https://ms.portal.azure.com/#create/Microsoft.AppPlatform)açın ve hesabınızda oturum açın.

1. Örnek uygulama için **proje ayrıntılarını** aşağıdaki gibi sağlayın:

    1. Uygulamanın ilişkilendirileceği **aboneliği** seçin.
    1. Uygulama için bir kaynak grubu seçin veya oluşturun. Yeni bir kaynak grubu oluşturmanızı öneririz.  Aşağıdaki örnekte `myspringservice`adlı yeni bir kaynak grubu gösterilmektedir.
    1. Yeni Azure yay bulut hizmeti için bir ad sağlayın.  Ad 4 ila 32 karakter uzunluğunda olmalı ve yalnızca küçük harf, sayı ve kısa çizgi içermelidir. Hizmet adının ilk karakteri bir harf olmalıdır ve son karakter bir harf ya da sayı olmalıdır.  Aşağıdaki örnekteki hizmetin adı `contosospringcloud`vardır.
    1. Belirtilen seçeneklerden uygulamanız için bir konum seçin.  Bu örnekte `East US`seçeceğiz.
    1. Yeni hizmetinizin özetini gözden geçirmek için **gözden geçir + oluştur** ' u seçin.  Her şey doğru görünüyorsa **Oluştur**' u seçin.

    > [!div class="mx-imgBorder"]
    > Gözden geçir + oluştur ![seçin](media/maven-qs-review-create.jpg)

Hizmetin dağıtılması yaklaşık 5 dakika sürer. Hizmet dağıtıldıktan sonra **Kaynağa Git** ' i seçin ve hizmet örneği Için **genel bakış** sayfası görüntülenir.

## <a name="set-up-your-configuration-server"></a>Yapılandırma sunucunuzu ayarlama

1. Hizmete **genel bakış** sayfasında, **yapılandırma sunucusu**' nu seçin.
1. **Varsayılan depo** bölümünde, **URI** 'yi **https://github.com/Azure-Samples/piggymetrics** olarak ayarlayın, **etiketi** **config**olarak ayarlayın ve ardından değişikliklerinizi kaydetmek için **Uygula** ' yı seçin.

    > [!div class="mx-imgBorder"]
    > ![yapılandırma ayarlarını tanımlama ve uygulama](media/maven-qs-apply-config.jpg)

## <a name="clone-and-build-the-sample-application-repository"></a>Örnek uygulama deposunu kopyalama ve derleme

1. [Azure Cloud Shell](https://shell.azure.com)başlatın.

1. Aşağıdaki komutu çalıştırarak Git deposunu kopyalayın:

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Dizini değiştirin ve aşağıdaki komutu çalıştırarak projeyi derleyin:

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Azure yay bulutuna yapılandırma ve dağıtım oluşturma

1. Üst POB 'yi içeren Pumetrik ölçümleri kök klasöründe aşağıdaki komutu çalıştırarak yapılandırma oluşturun:

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.0.0:config
    ```

    a. `gateway`,`auth-service`ve `account-service`modüllerini seçin.

    b. Aboneliğinizi ve Azure yay bulut hizmeti kümenizi seçin.

    c. Belirtilen projeler listesinde, ortak erişim sağlamak için `gateway` karşılık gelen sayıyı girin.
    
    d. Yapılandırmayı onaylayın.

1. Pod artık eklenti bağımlılıklarını ve yapılandırmasını içerir. Aşağıdaki komutu kullanarak uygulamaları dağıtın:

   ```azurecli
   mvn azure-spring-cloud:deploy
   ```

1. Dağıtım tamamlandıktan sonra, önceki komutun çıktısında belirtilen URL 'YI kullanarak Pudmetriye erişebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Maven deposundan bir yay bulutu uygulaması dağıttınız. Azure Spring Cloud hakkında daha fazla bilgi edinmek için uygulamanızı dağıtıma hazırlama hakkında öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure Spring Cloud uygulamanızı dağıtıma hazırlayın](spring-cloud-tutorial-prepare-app-deployment.md)
> [Azure için Maven eklentileri hakkında daha fazla bilgi edinin](https://github.com/microsoft/azure-maven-plugin)
