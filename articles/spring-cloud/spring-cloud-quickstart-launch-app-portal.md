---
title: 'Hızlı başlangıç: Azure portal kullanarak bir Azure Spring Cloud uygulaması başlatın'
description: Azure portal kullanarak Azure yay bulutuna örnek bir uygulama dağıtın.
author: jpconnock
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/4/2019
ms.author: jeconnoc
ms.openlocfilehash: 39a249fb75249505189e2af4872c3a3f61ebe2af
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133231"
---
# <a name="quickstart-launch-an-azure-spring-cloud-application-using-the-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak bir Azure Spring Cloud uygulaması başlatın

Azure Spring Cloud, Azure 'da Spring Cloud tabanlı mikro hizmet uygulamalarını kolayca çalıştırmanıza olanak sağlar.

Bu hızlı başlangıçta, mevcut bir yay bulutu uygulamasının Azure 'a nasıl dağıtılacağı gösterilir.  Bu öğreticide kullanılan örnek uygulama kodunu [GitHub örnekleri depomız](https://github.com/Azure-Samples/PiggyMetrics)bölümünde bulabilirsiniz. İşiniz bittiğinde, belirtilen örnek uygulama çevrimiçi olarak erişilebilir ve Azure portal aracılığıyla yönetilmeye hazırlıyordu.

Bu hızlı başlangıcı izleyerek şunları nasıl yapacağınızı öğreneceksiniz:

> [!div class="checklist"]
> * Hizmet örneği sağlama
> * Bir örnek için yapılandırma sunucusu ayarlama
> * Yerel olarak bir mikro Hizmetler uygulaması oluşturma
> * Her mikro hizmeti dağıtma
> * Uygulamanız için genel uç nokta atama

## <a name="prerequisites"></a>Önkoşullar

>[!Note]
> Azure yay bulutu Şu anda genel önizleme olarak sunulmaktadır. Genel Önizleme teklifleri, müşterilerin resmi sürümünden önceki yeni özelliklerle deneme yapmasına olanak tanır.  Genel Önizleme özellikleri ve Hizmetleri üretim kullanımı için tasarlanmamıştır.  Önizleme sırasında destek hakkında daha fazla bilgi için lütfen [SSS](https://azure.microsoft.com/support/faq/) veya dosya dosyası [destek isteği](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) inceleyerek daha fazla bilgi edinebilirsiniz.

>[!TIP]
> Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur.  Git, JDK, Maven ve Azure CLı 'nin en son sürümleri de dahil olmak üzere önceden yüklenmiş ortak Azure araçları vardır. Azure aboneliğinizde oturum açtıysanız, shell.azure.com adresinden [Azure Cloud Shell](https://shell.azure.com) başlatın.  [Belgelerimizi okuyarak](../cloud-shell/overview.md) Azure Cloud Shell hakkında daha fazla bilgi edinebilirsiniz

Bu hızlı başlangıcı tamamlamak için:

1. [Git'i yükleyin](https://git-scm.com/)
2. [JDK 8 ' i yükler](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Maven 3,0 veya üstünü yükler](https://maven.apache.org/download.cgi)
4. [Azure CLı sürüm 2.0.67 veya üstünü yükler](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Azure aboneliğine kaydolma](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Azure CLı uzantısını yükler

Aşağıdaki komutu kullanarak Azure CLı için Azure yay bulutu uzantısını yükler

```Azure CLI
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Azure portal bir hizmet örneği sağlayın

1. Bir Web tarayıcısında, [Azure Portal Azure Spring Cloud bağlantısını](https://ms.portal.azure.com/#create/Microsoft.AppPlatform)açın.

1. Azure yay bulutu **Oluştur** sayfasında formu doldurun.  Aşağıdaki yönergeleri göz önünde bulundurun:
    - Hizmet adı: hizmet örneğinizin adını belirtin.  Ad 4 ila 32 karakter uzunluğunda olmalı ve yalnızca küçük harf, sayı ve kısa çizgi içermelidir.  Hizmet adının ilk karakteri bir harf olmalıdır ve son karakter bir harf ya da sayı olmalıdır.
    - Abonelik: Bu kaynak için faturalandırılması istediğiniz aboneliği seçin.  Bu aboneliğin Azure yay bulutu için izin verilenler listenize eklendiğinden emin olun.
    - Kaynak grubu: yeni kaynaklar için yeni kaynak grupları oluşturmak en iyi uygulamadır.
    - Konum: hizmet örneğinizin konumunu seçin. Şu anda desteklenen konumlar Doğu ABD, Batı ABD 2, Batı Avrupa ve Güneydoğu Asya 'yı içerir.

1. **Gözden geçir ve oluştur**' a tıklayın.

1. Belirtimlerinizi doğrulayıp **Oluştur**' a tıklayın.

Hizmetin dağıtılması yaklaşık 5 dakika sürer.  Hizmet örneği için **genel bakış** sayfası, dağıtıldıktan sonra görüntülenir.

## <a name="set-up-your-configuration-server"></a>Yapılandırma sunucunuzu ayarlama

1. Hizmete **genel bakış** sayfasına gidin ve **yapılandırma sunucusu**' nu seçin.

1. **Varsayılan depo** bölümünde, **URI** 'yi "https\://GitHub.com/Azure-Samples/piggymetrics" olarak ayarlayın, **etiketi** "config" olarak ayarlayın ve değişikliklerinizi kaydetmek için **Uygula** ' yı seçin.

    ![ASC portalının ekran görüntüsü](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

## <a name="build-and-deploy-microservice-applications"></a>Mikro hizmet uygulamaları oluşturma ve dağıtma

1. Bir [Azure Cloudshell](https://shell.azure.com) açın ve örnek uygulama deposunu yerel makinenize kopyalayın.  Burada, önce uygulamayı kopyalamadan önce `source-code` adlı geçici bir dizin oluşturacağız.

    ```azurecli
    mkdir source-code
    cd source-code
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

1. Kopyalanmış paketi oluşturun.

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```

1. Azure CLı 'da oturum açın ve etkin aboneliğinizi ayarlayın.

    ```azurecli
    # Login to Azure CLI
    az login

    # List all subscriptions
    az account list -o table

    # Set active subscription
    az account set --subscription <target subscription ID>
    ```

1. Kaynak grubunuza ve hizmetinize ad atayın. Aşağıdaki yer tutucuları, bu öğreticide daha önce sağladığınız kaynak grubu adı ve hizmet adı ile değiştirdiğinizden emin olun.

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

1. `gateway` uygulamasını oluşturun ve JAR dosyasını dağıtın.

    ```azurecli
    az spring-cloud app create -n gateway
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

1. Aynı kalıbı izleyerek `account-service` ve `auth-service` uygulamaları oluşturun ve JAR dosyalarını dağıtın.

    ```azurecli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

1. Uygulamaların dağıtılmasının tamamlanmasının birkaç dakika sürer. Dağıtıldığını doğrulamak için Azure portal **uygulamalar** dikey penceresine gidin. Her üç uygulamanın bir satırını görmeniz gerekir.

## <a name="assign-a-public-endpoint-to-gateway"></a>Ağ geçidine genel uç nokta atama

1. Soldaki menüden **uygulamalar** sekmesini açın.

1. **Genel bakış** sayfasını göstermek için `gateway` uygulamayı seçin.

1. Ağ geçidine bir genel uç nokta atamak için **etki alanı ata** ' yı seçin. Bu birkaç dakika sürebilir.

    ![ASC portalının ekran görüntüsü](media/spring-cloud-quickstart-launch-app-portal/portal-endpoint.png)

1. Çalışan uygulamanızı görüntülemek için tarayıcınıza atanan ortak uç noktayı ( **URL etiketli URL**) girin.

    ![ASC portalının ekran görüntüsü](media/spring-cloud-quickstart-launch-app-portal/sample-app.png)


## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta şunları öğrendiniz:

> [!div class="checklist"]
> * Hizmet örneği sağlama
> * Bir örnek için yapılandırma sunucusu ayarlama
> * Yerel olarak bir mikro Hizmetler uygulaması oluşturma
> * Her mikro hizmeti dağıtma
> * Uygulama ağ geçidiniz için genel uç nokta atama

> [!div class="nextstepaction"]
> [Azure Spring Cloud uygulamanızı dağıtıma hazırlama](spring-cloud-tutorial-prepare-app-deployment.md)
