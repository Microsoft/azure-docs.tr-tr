---
title: Azure CLI'yi Jenkins ile yürütme
description: Jenkins İşlem Hattında Azure'a Java web uygulaması dağıtmak için Azure CLI'yi nasıl kullanacağınızı öğrenin
keywords: jenkins, azure, devops, app service, cli
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: bd9192974f6860d08d84a9028702ce2203f562e7
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158823"
---
# <a name="deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Jenkins ve Azure CLI ile Azure App Service'e dağıtım yapma
Azure'a Java web uygulaması dağıtmak için [Jenkins İşlem Hattı](https://jenkins.io/doc/book/pipeline/)'ndaki Azure CLI'yi kullanabilirsiniz. Bu öğreticide, aşağıdakileri öğrenerek bir Azure sanal makinesinde CI/CD işlem hattı oluşturursunuz:

> [!div class="checklist"]
> * Jenkins sanal makinesi oluşturma
> * Jenkins’i yapılandırma
> * Azure'da web uygulaması oluşturma
> * GitHub deposu hazırlama
> * Jenkins işlem hattı oluşturma
> * İşlem hattını çalıştırma ve web uygulamasını doğrulama

Bu öğretici, Azure CLI 2.0.4 veya sonraki bir sürümü gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükseltme gerekiyorsa, bkz. [Azure CLI yükleme]( /cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-configure-jenkins-instance"></a>Jenkins örneği oluşturma ve Jenkins örneğini yapılandırma
Zaten bir Jenkins ana şablonunuz yoksa, varsayılan olarak gerekli [Azure kimlik bilgileri](https://plugins.jenkins.io/azure-credentials) eklentisini Içeren [çözüm şablonuyla](install-jenkins-solution-template.md)başlayın. 

Azure kimlik bilgisi eklentisi, Jenkins 'de Microsoft Azure hizmet sorumlusu kimlik bilgilerini depolamanıza olanak tanır. 1\.2 sürümünde, Jenkins İşlem Hattının Azure kimlik bilgilerini alabilmesine yönelik desteği ekledik. 

1\.2 veya sonraki bir sürüme sahip olduğunuzdan emin olun:
* Jenkins panosunda **Manage Jenkins -> Plugin Manager**'ı (Jenkins'i Yönet -> Eklenti Yöneticisi) seçin ve **Azure Credential**'ı (Azure Kimlik Bilgileri) arayın. 
* Sürüm 1,2 ' den daha eski ise eklentiyi güncelleştirin.

Java JDK ve Maven, Jenkins ana sunucusunda da gereklidir. Yüklemek için SSH kullanarak Jenkins ana sunucusunda oturum açın ve aşağıdaki komutları çalıştırın:
```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Jenkins kimlik bilgilerine Azure hizmet sorumlusunu ekleme

Azure CLI'yi yürütmek için Azure kimlik bilgileri gereklidir.

* Jenkins panosunda **Credentials -> System**'a (Kimlik Bilgileri -> Sistem) tıklayın. Ardından, **Global credentials(unrestricted)** (Genel kimlik bilgileri (sınırsız)) seçeneğini belirleyin.
* Abonelik Kimliği, İstemci Kimliği, Gizli Anahtar ve OAuth 2.0 Belirteç Uç Noktası değerlerini girip **Microsoft Azure hizmet sorumlusu** eklemek için [Add Credentials](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)'a (Kimlik Bilgileri Ekle) tıklayın. Sonraki adımda kullanmak üzere bir kimlik girin.

![Kimlik Bilgileri Ekleme](./media/execute-cli-jenkins-pipeline/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Java web uygulamasını dağıtmak için Azure App Service oluşturma

**az appservice plan create** CLI komutunu kullanarak [ÜCRETSİZ](/cli/azure/appservice/plan#az-appservice-plan-create) fiyatlandırma katmanıyla bir Azure App Service planı oluşturun. Uygulama hizmeti planı, uygulamalarınızı barındırmak için kullanılan fiziksel kaynakları tanımlar. Uygulama hizmeti planına atanan tüm uygulamalar bu kaynakları paylaşarak birden çok uygulamayı barındırırken, maliyetten tasarruf etmenize imkan sağlar. 

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \ 
    --resource-group myResourceGroup \
    --sku FREE
```

Plan hazır olduğunda, Azure CLI aşağıdaki örnekte gösterilene benzer bir çıkış görüntüler:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Azure Web uygulaması oluşturma

 [ App Service planında web uygulaması tanımı oluşturmak için ](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)az webapp create`myAppServicePlan` CLI komutunu kullanın. Web uygulaması tanımı, uygulamanıza erişebilmek için bir URL sağlar ve çeşitli seçenekleri yapılandırarak kodunuzu Azure'a dağıtır. 

```azurecli-interactive
az webapp create \
    --name <app_name> \ 
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

`<app_name>` yer tutucusunu kendi benzersiz uygulama adınızla değiştirin. Bu benzersiz ad, web uygulamasının varsayılan etki alanı adının bir parçası olduğundan, adın Azure’daki tüm uygulamalarda benzersiz olması gerekir. Uygulamanızı kullanıcılarınızın kullanımına sunmadan önce web uygulamasına bir özel etki alanı adı girdisi eşleyebilirsiniz.

Web uygulaması tanımı hazır olduğunda Azure CLI aşağıda yer alan örnekteki gibi bilgiler gösterir: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
   ...
  < Output has been truncated for readability >
}
```

### <a name="configure-java"></a>Java'yı yapılandırma 

[az appservice web config update](/cli/azure/webapp/config) komutunu kullanarak uygulamanız için gerekli Java çalışma zamanı yapılandırmasını ayarlayın.

Aşağıdaki komut web uygulamasını yeni Java 8 JDK ve [Apache Tomcat](https://tomcat.apache.org/) 8.0 üzerinde çalıştırılacak şekilde yapılandırır.

```azurecli-interactive
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>GitHub Deposu hazırlama
[Azure için Basit Java Web Uygulaması](https://github.com/azure-devops/javawebappsample) deposunu açın. Kendi GitHub hesabınızda deponun çatalını oluşturmak için sağ üst köşedeki **Çatal** düğmesine tıklayın.

* GitHub web kullanıcı arabiriminde **Jenkinsfile** dosyasını açın. Sırasıyla 20. ve 21. satırlarda kaynak grubunuzu ve web uygulamanızın adını güncelleştirmek için bu dosyayı düzenlemek üzere kalem simgesine tıklayın.

```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<app_name>'
```

* Jenkins örneğinizdeki kimlik bilgilerini güncelleştirmek için 23. satırı değiştirin

```java
withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
```

## <a name="create-jenkins-pipeline"></a>Jenkins işlem hattı oluşturma
Jenkins'i bir web tarayıcısında açın ve **New Item**'a (Yeni Öğe) tıklayın. 

* İş için bir ad girin ve **İşlem Hattı**'nı seçin. **OK (Tamam)** düğmesine tıklayın.
* Ardından, **Pipeline** (İşlem hattı) sekmesine tıklayın. 
* **Definition** (Tanım) için **Pipeline script from SCM**'yi (SCM'den işlem hattı betiği) seçin.
* **SCM** için **Git**'i seçin.
* Çatalını oluşturduğunuz deponuzun GitHub URL'sini girin: https:\<çatalını oluşturduğunuz depo\>.git
* **Kaydet**’e tıklayın

## <a name="test-your-pipeline"></a>İşlem hattınızı test etme
* Oluşturduğunuz işlem hattına gidin ve **Build Now**'a (Şimdi Derle) tıklayın
* Birkaç saniye içinde bir derleme başarıyla oluşturulur ve derlemeye gidip **Konsol Çıktısı**'na tıklayarak derlemeyle ilgili ayrıntıları görebilirsiniz

## <a name="verify-your-web-app"></a>Web uygulamanızı doğrulama
Bu işlem, WAR dosyasının web uygulamanıza başarıyla dağıtıldığını doğrulamak için gerçekleştirilir. Bir web tarayıcısı açın:

* http://&lt;uygulama_adı>.azurewebsites.net/api/calculator/ping adresine gidin  
Şununla karşılaşırsınız:

        Welcome to Java Web App!!! This is updated!
        Sun Jun 17 16:39:10 UTC 2017

* x ve y toplamını almak için http://&lt;uygulama_adı>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (&lt;x> ve &lt;y> değerlerini sayılarla değiştirin) adresine gidin

![Calculator: add](./media/execute-cli-jenkins-pipeline/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Linux üzerinde Web App'e dağıtım yapma
Jenkins işlem hattınızda Azure CLI'yi nasıl kullanacağınızı öğrendiğinize göre artık betiği, Linux üzerinde Web App'e dağıtım yapacak şekilde değiştirebilirsiniz.

Linux üzerinde Web App, dağıtım yapmak için farklı bir yöntemi (Docker kullanımı) destekler. Dağıtım yapmak için web uygulamanızı, bir hizmet çalışma zamanı ile Docker görüntüsü olarak paket haline getiren bir Dockerfile sağlamanız gerekir. Daha sonra eklenti görüntüyü oluşturur, bir Docker kayıt defterine gönderir ve görüntüyü Web uygulamanıza dağıtır.

* Linux üzerinde çalışan bir Azure Web App oluşturma adımları için [buraya](../app-service/containers/quickstart-nodejs.md) tıklayın.
* Bu [makaledeki](https://docs.docker.com/engine/installation/linux/ubuntu/) yönergeleri uygulayarak Jenkins örneğinizde Docker yükleyin.
* [Buradaki](/azure/container-registry/container-registry-get-started-azure-cli) adımları kullanarak Azure portal'da bir Kapsayıcı Kayıt Defteri oluşturun.
* Çatalını oluşturmuş olduğunuz, [Azure için Basit Java Web Uygulaması](https://github.com/azure-devops/javawebappsample) deposunda **Jenkinsfile2** dosyasını düzenleyin:
    * 18-21 arası satırlarda sırasıyla kaynak grubunuzun, web uygulamanızın ve ACR'nizin adlarını güncelleştirin. 
        ```
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    * 24. satırda \<azsrvprincipal\> değerini kimlik bilginiz ile güncelleştirin
        ```
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

* Windows'da Azure web uygulamasına dağıtım yaparken gerçekleştirmiş olduğunuz gibi yeni bir Jenkins işlem hattı oluşturun. Yalnızca bu kez **Jenkinsfile2** dosyasını kullanın.
* Yeni işinizi çalıştırın.
* Azure CLI'de doğrulama için şunu çalıştırın:

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    Aşağıdaki sonucu alırsınız:
    
    ```
    [
    "calculator"
    ]
    ```
    
    http://&lt;uygulama_adı>.azurewebsites.net/api/calculator/ping adresine gidin. Şu iletiyle karşılaşırsınız: 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    x ve y toplamını almak için http://&lt;uygulama_adı>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (&lt;x> ve &lt;y> değerlerini sayılarla değiştirin) adresine gidin
    
## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, GitHub deposundaki kaynak kodu kullanıma alan bir Jenkins işlem hattı yapılandırdınız. Bir war dosyası oluşturmak için Maven'ı çalıştırır ve ardından Azure App Service'e dağıtım yapmak için Azure CLI'yi kullanır. Şunları öğrendiniz:

> [!div class="checklist"]
> * Jenkins sanal makinesi oluşturma
> * Jenkins’i yapılandırma
> * Azure'da web uygulaması oluşturma
> * GitHub deposu hazırlama
> * Jenkins işlem hattı oluşturma
> * İşlem hattını çalıştırma ve web uygulamasını doğrulama
