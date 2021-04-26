---
title: Azure yay bulutu 'nda hazırlama ortamı ayarlama | Microsoft Docs
description: Azure Spring Cloud ile mavi yeşil dağıtımı nasıl kullanacağınızı öğrenin
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 9b3a5659e91ca90d31500b10526e3e2179d4e7da
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046122"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Azure yay bulutu 'nda hazırlama ortamı ayarlama

**Bu makale şu şekilde geçerlidir:** ✔️ Java

Bu makalede, Azure Spring Cloud 'daki mavi yeşil dağıtım modelini kullanarak hazırlama dağıtımının nasıl ayarlanacağı açıklanır. Mavi-yeşil dağıtım, yeni (yeşil) bir sürümü dağıtıldığında mevcut (mavi) bir sürümün canlı kalmasını sağlayan bir Azure DevOps sürekli teslim modelidir. Bu makalede, üretim dağıtımını değiştirmeden bu hazırlama dağıtımını üretime nasıl koyabileceğiniz gösterilir.

## <a name="prerequisites"></a>Önkoşullar

* Standart bir fiyatlandırma katmanında Azure yay bulutu örneği
* Azure CLı için [Azure yay bulutu uzantısı](/cli/azure/azure-cli-extensions-overview)

Bu makale, Spring ınitialr 'den oluşturulmuş bir uygulamayı kullanır. Bu örnek için farklı bir uygulama kullanmak istiyorsanız, hazırlama dağıtımınızı üretimden ayırt etmek için uygulamanın herkese açık bir bölümünde basit bir değişiklik yapmanız gerekir.

>[!TIP]
> [Azure Cloud Shell](https://shell.azure.com) , bu makaledeki yönergeleri çalıştırmak için kullanabileceğiniz, ücretsiz bir etkileşimli kabuktur.  Git, JDK, Maven ve Azure CLı 'nin en son sürümleri de dahil olmak üzere, yaygın, önceden yüklenmiş Azure araçlarına sahiptir. Azure aboneliğinizde oturum açtıysanız Cloud Shell örneğinizi başlatın. Daha fazla bilgi edinmek için bkz. [Azure Cloud Shell Genel Bakış](../cloud-shell/overview.md).

Azure Spring Cloud 'da mavi-yeşil dağıtım ayarlamak için, sonraki bölümlerdeki yönergeleri izleyin.

## <a name="install-the-azure-cli-extension"></a>Azure CLı uzantısını yükler

Aşağıdaki komutu kullanarak Azure CLı için Azure yay bulutu uzantısını yükler:

```azurecli
az extension add --name spring-cloud
```
## <a name="prepare-the-app-and-deployments"></a>Uygulama ve dağıtımları hazırlama
Uygulamayı derlemek için aşağıdaki adımları izleyin:

1. [Bu yapılandırmayla](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client)Spring ınitialvr kullanarak örnek uygulama için kod oluşturun.

2. Kodu indirin.
3. Aşağıdaki HelloController. Java kaynak dosyasını klasörüne ekleyin `\src\main\java\com\example\hellospring\` :

   ```java
   package com.example.hellospring; 
   import org.springframework.web.bind.annotation.RestController; 
   import org.springframework.web.bind.annotation.RequestMapping; 

   @RestController 

   public class HelloController { 

   @RequestMapping("/") 

     public String index() { 

         return "Greetings from Azure Spring Cloud!"; 
     } 

   } 
   ```
4. . Jar dosyasını oluşturun:

   ```azurecli
   mvn clean packge -DskipTests
   ```
5. Azure Spring Cloud örneğiniz üzerinde uygulamayı oluşturun:

   ```azurecli
   az spring-cloud app create -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --assign-endpoint
   ```
6. Uygulamayı Azure Spring Cloud 'a dağıtın:

   ```azurecli
   az spring-cloud app deploy -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
   ```
7. Hazırlama dağıtımınızın kodunu değiştirin:

   ```java
   package com.example.hellospring; 
   import org.springframework.web.bind.annotation.RestController; 
   import org.springframework.web.bind.annotation.RequestMapping; 

   @RestController 

   public class HelloController { 

   @RequestMapping("/") 

     public String index() { 

         return "Greetings from Azure Spring Cloud! THIS IS THE GREEN DEPLOYMENT"; 
     } 

   } 
   ```
8. . Jar dosyasını yeniden oluşturun:

   ```azurecli
   mvn clean packge -DskipTests
   ```
9. Yeşil dağıtımı oluşturun: 

   ```azurecli
   az spring-cloud app deployment create -n green --app demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar 
   ```

## <a name="view-apps-and-deployments"></a>Uygulamaları ve dağıtımları görüntüleme

Aşağıdaki yordamı kullanarak dağıtılan uygulamaları görüntüleyin:

1. Azure portal Azure Spring Cloud örneğinize gidin.

1. Sol bölmeden, hizmet örneğiniz için uygulamaları görüntülemek üzere **uygulamalar** bölmesini açın.

   ![Açık uygulamalar bölmesinin ekran görüntüsü.](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. Bir uygulama seçebilir ve ayrıntıları görüntüleyebilirsiniz.

   ![Bir uygulama için ayrıntıların ekran görüntüsü.](media/spring-cloud-blue-green-staging/app-overview.png)

1. Uygulamanın tüm dağıtımlarını görmek için **dağıtımları** açın. Kılavuz hem üretim hem de hazırlama dağıtımlarını gösterir.

   ![Listelenen uygulama dağıtımlarını gösteren ekran görüntüsü.](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Geçerli olarak dağıtılan uygulamayı açmak için URL 'YI seçin.
    
   ![Dağıtılan uygulama için U R L 'yi gösteren ekran görüntüsü.](media/spring-cloud-blue-green-staging/running-blue-app.png)

1. Varsayılan uygulamayı görmek için **durum** sütununda **Üretim** ' ı seçin.
    
   ![Varsayılan uygulama için U R L 'yi gösteren ekran görüntüsü.](media/spring-cloud-blue-green-staging/running-default-app.png)

1. Hazırlama uygulamasını görmek için **durum** sütununda **hazırlama** ' yı seçin.
    
   ![Hazırlama uygulamasının U R L 'sini gösteren ekran görüntüsü.](media/spring-cloud-blue-green-staging/running-staging-app.png)

>[!TIP]
> * CSS dosyasının doğru bir şekilde yüklendiğinden emin olmak için, test uç noktanızın eğik çizgiyle (/) bitdiğini doğrulayın.  
> * Tarayıcınızda sayfayı görüntülemek için oturum açma kimlik bilgilerini girmeniz gerekiyorsa, test uç noktanızın kodunu çözmek için [URL kodunu çöz](https://www.urldecoder.org/) kullanın. URL kod çözme *https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.io/Gateway/Green* biçiminde bir URL döndürür. Uç noktanıza erişmek için bu biçimi kullanın.

>[!NOTE]    
> Yapılandırma sunucusu ayarları hem hazırlama ortamınız hem de üretim ortamınız için geçerlidir. Örneğin, yapılandırma sunucusundaki uygulama ağ geçidiniz için bağlam yolunu (*Server. servlet. Context-Path*) bir *somepath* olarak ayarlarsanız, yeşil dağıtımınızın yolu *https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.io/Gateway/Green/somepath/...* olarak değişir.
 
Bu noktada herkese açık uygulama ağ geçidinizi ziyaret ederseniz, eski sayfayı yeni değiştirmeksizin görmeniz gerekir.

## <a name="set-the-green-deployment-as-the-production-environment"></a>Yeşil dağıtımı üretim ortamı olarak ayarlama

1. Hazırlama ortamınızda yaptığınız değişikliği doğruladıktan sonra üretime gönderebilirsiniz. **Uygulamalar**  >  **dağıtımları** sayfasında, şu anda **üretimde** olan uygulamayı seçin.

1. Yeşil dağıtımın **kayıt durumu** sonrasında üç noktayı seçin ve ardından **üretim olarak ayarla**' yı seçin. 

   ![Hazırlama derlemesini üretime ayarlamaya yönelik seçimleri gösteren ekran görüntüsü.](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Uygulamanın URL 'sinin değişikliklerinizi görüntülediğini doğrulayın.

   ![Şimdi üretimde uygulamanın U R L bölümünü gösteren ekran görüntüsü.](media/spring-cloud-blue-green-staging/new-production-deployment.png)

>[!NOTE]
> Yeşil dağıtımı üretim ortamı olarak ayarladıktan sonra, önceki dağıtım hazırlama dağıtımı olur.

## <a name="modify-the-staging-deployment"></a>Hazırlama dağıtımını değiştirme

Değişikliklerinizi tatmin ediyorsanız, uygulama kodunuzu değiştirebilir, yeni bir. jar paketi oluşturabilir ve Azure CLı kullanarak yeşil dağıtımınıza yükleyebilirsiniz:

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Hazırlama dağıtımını Sil

Hazırlama dağıtımınızı Azure portal silmek için, hazırlama dağıtımınızın sayfasına gidin ve **Sil** düğmesini seçin.

Alternatif olarak, aşağıdaki komutu çalıştırarak hazırlama dağıtımınızı Azure CLı 'den silebilirsiniz:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure yay bulutu için CI/CD](./spring-cloud-howto-cicd.md?pivots=programming-language-java)