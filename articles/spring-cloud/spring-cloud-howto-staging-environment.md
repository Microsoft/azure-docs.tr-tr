---
title: Azure yay bulutu 'nda hazırlama ortamı ayarlama | Microsoft Docs
description: Azure Spring Cloud ile mavi yeşil dağıtımı nasıl kullanacağınızı öğrenin
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: cd1573a3d896f3d2d5cb53130d8fac86be43beb6
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152080"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Azure yay bulutu 'nda hazırlama ortamı ayarlama

Bu makalede, Azure Spring Cloud 'daki mavi yeşil dağıtım modelini kullanarak bir hazırlama dağıtımının nasıl ayarlanacağı açıklanır. Ayrıca, bu hazırlama dağıtımını üretim dağıtımını doğrudan değiştirmeden üretime nasıl koyabileceğiniz gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede, [Azure yay bulut uygulaması başlatma hakkında öğreticimiz](spring-cloud-quickstart-launch-app-portal.md)tarafından zaten POF ölçümleri uygulamasını dağıttığınızı varsaymış olursunuz. Pıof ölçümleri üç uygulama içerir: "Gateway," "Account-Service" ve "Auth-Service."  

Bu örnek için farklı bir uygulama kullanmak istiyorsanız, uygulamanın genel kullanıma açık bir bölümünde basit bir değişiklik yapmanız gerekir.  Bu değişiklik, üretimden hazırlama dağıtımınızı farklılaştırır.

>[!TIP]
> Azure Cloud Shell, bu makaledeki yönergeleri çalıştırmak için kullanabileceğiniz, ücretsiz bir etkileşimli kabuktur.  Git, JDK, Maven ve Azure CLı 'nin en son sürümleri de dahil olmak üzere, yaygın, önceden yüklenmiş Azure araçlarına sahiptir. Azure aboneliğinizde oturum açtıysanız [Azure Cloud Shell](https://shell.azure.com)başlatın.  Daha fazla bilgi edinmek için bkz. [Azure Cloud Shell Genel Bakış](../cloud-shell/overview.md).

Azure Spring Cloud 'da hazırlama ortamı ayarlamak için, sonraki bölümlerdeki yönergeleri izleyin.

## <a name="install-the-azure-cli-extension"></a>Azure CLı uzantısını yükler

Aşağıdaki komutu kullanarak Azure CLı için Azure yay bulutu uzantısını yükler:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>Tüm dağıtımları görüntüle

Azure portal hizmet örneğinize gidin ve tüm dağıtımları görüntülemek için **dağıtım yönetimi** ' ni seçin. Daha fazla ayrıntı görüntülemek için her bir dağıtımı seçebilirsiniz.

## <a name="create-a-staging-deployment"></a>Hazırlama dağıtımı oluşturma

1. Yerel geliştirme ortamınızda, Papmetrik ağ geçidi uygulamasında küçük bir değişiklik yapın. Örneğin, *ağ geçidi/src/Main/Resources/static/CSS/Launch. css* dosyasındaki rengi değiştirin. Bunun yapılması iki dağıtımı kolayca ayırt etmenizi sağlar. Jar paketini derlemek için aşağıdaki komutu çalıştırın: 

    ```azurecli
    mvn clean package
    ```

1. Azure CLı 'de yeni bir dağıtım oluşturun ve bunu hazırlama dağıtım adı "yeşil" olarak verin.

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. Dağıtım başarıyla tamamlandıktan sonra, **uygulama panosundan**ağ geçidi sayfasına erişin ve sol taraftaki **uygulama örnekleri** sekmesinde tüm örneklerinizi görüntüleyin.
  
> [!NOTE]
> Doğrulama işlemi tamamlanmadan önce trafiğin Bu dağıtıma yönlendirilmemesi için bulma durumu *OUT_OF_SERVICE* .

## <a name="verify-the-staging-deployment"></a>Hazırlama dağıtımını doğrulama

1. **Dağıtım yönetimi** sayfasına dönün ve yeni dağıtımınızı seçin. Dağıtım durumu *çalışıyor*olarak gösterilmelidir. Ortam bir hazırlama ortamı olduğundan, **etki alanı ata/atamayı kaldır** düğmesi gri görünmelidir.

1. **Genel bakış** bölmesinde bir **Test uç noktası**görmeniz gerekir. Kopyalayıp yeni bir tarayıcı penceresine yapıştırın ve yeni PIP ölçümleri sayfası görüntülenmelidir.

>[!TIP]
> * CSS dosyasının doğru bir şekilde yüklendiğinden emin olmak için, test uç noktanızın eğik çizgiyle (/) bitdiğini doğrulayın.  
> * Tarayıcınızda sayfayı görüntülemek için oturum açma kimlik bilgilerini girmeniz gerekiyorsa, test uç noktanızın kodunu çözmek için [URL kodunu çöz](https://www.urldecoder.org/) kullanın. URL kod çözme "https://\<Kullanıcı adı >:\<Password > @\<Cluster-name >. test. azureapps. IO/Gateway/yeşil" biçiminde bir URL döndürür.  Uç noktanıza erişmek için bu formu kullanın.

>[!NOTE]    
> Yapılandırma sunucusu ayarları hem hazırlama ortamınız hem de üretim için geçerlidir. Örneğin, yapılandırma sunucusunda uygulama ağ geçidiniz için içerik yolunu (`server.servlet.context-path`) *somepath*olarak ayarlarsanız, yeşil dağıtımınızın yolu "https://\<username > olarak değişir:\<Password > @\<Cluster-name >. test. azureapps. IO/Gateway/yeşil/somepath/...".
 
 Bu noktada herkese açık uygulama ağ geçidinizi ziyaret ederseniz, eski sayfayı yeni değiştirmeksizin görmeniz gerekir.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>Yeşil dağıtımı üretim ortamı olarak ayarlama

1. Hazırlama ortamınızda yaptığınız değişikliği doğruladıktan sonra üretime gönderebilirsiniz. **Dağıtım yönetimine**dönün ve **ağ geçidi** uygulaması onay kutusunu seçin.

2. **Dağıtımı ayarla**' yı seçin.
3. **Üretim dağıtımı** listesinde **yeşil**' i seçin ve ardından **Uygula**' yı seçin.
4. Ağ Geçidi uygulamanıza **genel bakış** sayfasına gidin. Ağ Geçidi uygulamanız için zaten bir etki alanı atadıysanız, URL **genel bakış** bölmesinde görünür. Değiştirilen Pımetriler sayfasını görüntülemek için, URL 'yi seçin ve siteye gidin.

>[!NOTE]
> Yeşil dağıtımı üretim ortamı olarak ayarladıktan sonra, önceki dağıtım hazırlama dağıtımı olur.

## <a name="modify-the-staging-deployment"></a>Hazırlama dağıtımını değiştirme

Değişikliklerinizi tatmin ediyorsanız, uygulama kodunuzu değiştirebilir, yeni bir jar paketi oluşturabilir ve Azure CLı kullanarak yeşil dağıtımınıza yükleyebilirsiniz.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Hazırlama dağıtımını Sil

Hazırlama dağıtımınızı Azure bağlantı noktasından silmek için, hazırlama dağıtımı sayfanıza gidin ve **Sil** düğmesini seçin.

Alternatif olarak, aşağıdaki komutu çalıştırarak hazırlama dağıtımınızı Azure CLı 'den silebilirsiniz:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
