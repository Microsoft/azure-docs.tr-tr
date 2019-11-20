---
title: Jenkins eklentisini kullanarak Azure App Service'e dağıtım yapma
description: Jenkins'de Azure'a bir Java web uygulaması dağıtmak için Azure App Service Jenkins eklentisini nasıl kullanacağınızı öğrenin
keywords: jenkins, azure, devops, app service
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: de1bf0ea06210c86ff1da21dcac667754f11d7f4
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158523"
---
# <a name="deploy-to-azure-app-service-by-using-the-jenkins-plugin"></a>Jenkins eklentisini kullanarak Azure App Service'e dağıtım yapma 

Bir Java web uygulamasını Azure'a dağıtmak için [Jenkins İşlem Hattı](/azure/jenkins/execute-cli-jenkins-pipeline)'ndaki Azure CLI'yi veya [Azure App Service Jenkins eklentisini](https://plugins.jenkins.io/azure-app-service) kullanabilirsiniz. Jenkins eklentisi 1.0 sürümü, Azure App Service'in Web Apps özelliğini kullanarak aşağıdakiler üzerinden sürekli dağıtımı destekler:
* Dosya yükleme.
* Linux üzerinde Web App için Docker.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Dosya yükleme yoluyla Web Apps dağıtımı için Jenkins'i yapılandırma.
> * Kapsayıcılar için Web App dağıtmak üzere Jenkins'i yapılandırma.

## <a name="create-and-configure-a-jenkins-instance"></a>Jenkins örneği oluşturma ve yapılandırma

Bir Jenkins Ana Sunucunuz yoksa, Java Development Kit (JDK) 8 sürümünü ve aşağıdaki gerekli Jenkins eklentilerini içeren [çözüm şablonu](install-jenkins-solution-template.md) ile başlayın:

* [Jenkins Git istemcisi eklentisi](https://plugins.jenkins.io/git-client) 2.4.6 sürümü 
* [Docker Commons eklentisi](https://plugins.jenkins.io/docker-commons) 1.4.0 sürümü
* [Azure Kimlik Bilgileri](https://plugins.jenkins.io/azure-credentials) 1.2 sürümü
* [Azure App Service](https://plugins.jenkins.io/azure-app-service) 0.1 sürümü

Bir Web uygulamasını C#, php, Python, Java ve Node. js gibi Web Apps tarafından desteklenen herhangi bir dilde dağıtmak Için Jenkins eklentisini kullanabilirsiniz. Biz bu öğreticide [Azure için basit bir Java web uygulaması](https://github.com/azure-devops/javawebappsample) kullanacağız. Kendi GitHub hesabınızda deponun çatalını oluşturmak için GitHub arabiriminin sağ üst köşesinde bulunan **Çatal** düğmesini seçin.  

> [!NOTE]
> Java projesi oluşturmak için Java JDK ve Maven gereklidir. Jenkins Ana Sunucusunda veya sürekli tümleştirme için aracıyı kullanıyorsanız VM aracısında bu bileşenleri yükleyin. Bir Java SE uygulaması dağıtıyorsanız, derleme sunucusunda ZIP’e de ihtiyaç vardır.
>

Bu bileşenleri yüklemek için SSH ile Jenkins örneğinde oturum açın ve aşağıdaki komutları çalıştırın:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Kapsayıcılar için Web App'e dağıtım yapmak istiyorsanız Jenkins Ana Sunucusuna veya derleme için kullanılan VM aracısına Docker'ı yükleyin. Yönergeler için bkz. [Ubuntu üzerinde Docker'ı yükleme](https://docs.docker.com/engine/installation/linux/ubuntu/).

## <a name="service-principal"></a> Jenkins kimlik bilgilerine bir Azure hizmet sorumlusu ekleme

Azure'a dağıtım yapmak için bir Azure hizmet sorumlusuna ihtiyacınız vardır. 


1. Azure hizmet sorumlusu oluşturmak için [Azure clı](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) veya [Azure Portal](/azure/azure-resource-manager/resource-group-create-service-principal-portal)kullanın.
2. Jenkins panosunda **Credentials** > **System**'ı (Kimlik Bilgileri > Sistem) seçin. Ardından, **Global credentials(unrestricted)** (Genel kimlik bilgileri (sınırsız)) seçeneğini belirleyin.
3. Microsoft Azure hizmet sorumlusu eklemek için **Add Credentials**'ı (Kimlik Bilgileri Ekle) seçin. **Abonelik Kimliği**, **İstemci Kimliği**, **Gizli Anahtar** ve **OAuth 2.0 Belirteç Uç Noktası** alanları için değer girin. **Kimlik** alanını **mySp** olarak ayarlayın. Bu makaledeki sonraki adımlarda bu kimliği kullanacağız.


## <a name="configure-jenkins-to-deploy-web-apps-by-uploading-files"></a>Karşıya dosya yükleme yoluyla Web Apps dağıtımı için Jenkins'i yapılandırma

Projenizi Web Apps’e dağıtmak için, derleme yapıtlarınızı dosya yüklemeyle yükleyebilirsiniz. Azure App Service, birden çok dağıtım seçeneğini destekler. Azure App Service Jenkins eklentisini bunu sizin için basitleştirir ve türeyen dosya türüne göre dağıtım seçeneğini türetir. 

* Java EE uygulamaları için [WAR dağıtımı](/azure/app-service/deploy-zip#deploy-war-file) kullanılır.
* Java SE uygulamaları için [ZIP dağıtımı](/azure/app-service/deploy-zip#deploy-zip-file) kullanılır.
* Diğer diller için [Git dağıtımını](/azure/app-service/deploy-local-git) kullanılır.

İşi Jenkins'de ayarlayabilmek için bir Azure App Service planına ve Java uygulamasını çalıştıracak bir web uygulamasına ihtiyacınız vardır.


1. Azure CLI komutunu`az appservice plan create` kullanarak [ÜCRETSİZ](/cli/azure/appservice/plan#az-appservice-plan-create) fiyatlandırma katmanıyla bir Azure App Service planı oluşturun. App Service planı, uygulamalarınızı barındırmak için kullanılan fiziksel kaynakları tanımlar. Bir App Service planına atanan tüm uygulamalar bu kaynakları paylaşır. Paylaşılan kaynaklar, birden fazla uygulamayı barındırdığınız durumlarda maliyetten tasarruf etmenize yardımcı olur.
2. Bir web uygulaması oluşturun. [Azure portal](/azure/app-service/configure-common)'ı veya aşağıdaki `az` Azure CLI komutunu kullanabilirsiniz:
    ```azurecli-interactive 
    az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
    ```
    
3. Uygulamanız için gerekli Java çalışma zamanı yapılandırmasını ayarlayın. Aşağıdaki Azure CLI komutu, web uygulamasını güncel JDK 8 ve [Apache Tomcat](https://tomcat.apache.org/) 8.0 sürümlerinde çalışacak şekilde yapılandırır:
    ```azurecli-interactive
    az webapp config set \
    --name <myAppName> \
    --resource-group <myResourceGroup> \
    --java-version 1.8 \
    --java-container Tomcat \
    --java-container-version 8.0
    ```

### <a name="set-up-the-jenkins-job"></a>Jenkins işini ayarlama

1. Jenkins Panosunda, **freestyle** (serbest stil) türünde yeni bir proje oluşturun.
2. **Source Code Management** (Kaynak Kod Yönetimi) alanını, [Azure'a yönelik basit Java web uygulamasına](https://github.com/azure-devops/javawebappsample) ilişkin yerel çatalınızı kullanacak şekilde yapılandırın. **Depo URL'si** değerini girin. Örneğin: http:\//github.com/&lt;your_ID >/javawebappsample.
3. **Execute shell** komutunu ekleyerek projeyi Maven ile oluşturmaya yönelik bir adım ekleyin. Bu örnekte, hedef klasördeki \*.war dosyasını **ROOT.war** olarak yeniden adlandırmak için ek bir komuta ihtiyacımız var:   
    ```bash
    mvn clean package
    mv target/*.war target/ROOT.war
    ```

4. **Publish an Azure Web App**'i (Azure Web App Yayımla) seçerek derleme sonrası eylem ekleyin.
5. Azure hizmet sorumlusu olarak **mySp** değerini girin. Bu sorumlu, önceki bir adımda [Azure Kimlik Bilgileri](#service-principal) olarak depolanmıştı.
6. **Uygulama Yapılandırması**bölümünde, aboneliğinizdeki web uygulamasını ve kaynak grubunu seçin. Jenkins eklentisi, web uygulamasının Windows tabanlı mı, Linux tabanlı mı olduğunu otomatik olarak algılar. Windows web uygulamaları için **Publish Files** (Dosyaları Yayımla) seçeneği sunulur.
7. Dağıtmak istediğiniz dosyaları girin. Örneğin, Java'yı kullanıyorsanız WAR paketini belirtin. Dosyayı karşıya yükleme işlemi için kullanılacak kaynak ve hedef klasörleri belirtmek üzere isteğe bağlı **Kaynak Dizin** ve **Hedef Dizin** parametrelerini kullanın. Azure'da Java web uygulamaları bir Tomcat sunucusunda çalıştırılır. Java için, WAR paketinizi webapps klasörüne yüklersiniz. Bu örnek için **Kaynak Dizin** değerini **target**, **Hedef Dizin** değerini **webapps** olarak ayarlayın.
8. production dışında bir yuvaya dağıtım yapmak istiyorsanız **Yuva** adını da ayarlayabilirsiniz.
9. Projeyi kaydedin ve derleyin. Derleme tamamlandığında web uygulamanız Azure'a dağıtılır.

### <a name="deploy-web-apps-by-uploading-files-using-jenkins-pipeline"></a>Jenkins İşlem Hattını kullanarak karşıya dosya yükleme yoluyla Web Apps dağıtımı

Azure App Service Jenkins eklentisi işlem hattında kullanıma hazırdır. Aşağıdaki GitHub deposunda bulunan örneğe göz atabilirsiniz.

1. GitHub arabiriminde **Jenkinsfile_ftp_plugin** dosyasını açın. Dosyayı düzenlemek için kalem simgesini seçin. Web uygulamanız için sırasıyla 11. ve 12. satırlardaki **resourceGroup** ve **webAppName** tanımlarını güncelleştirin:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. 14. satırdaki **withCredentials** tanımını, Jenkins örneğinizdeki kimlik bilgisi olarak ayarlayın:
    ```java
    withCredentials([azureServicePrincipal('<mySp>')]) {
    ```

### <a name="create-a-jenkins-pipeline"></a>Jenkins işlem hattı oluşturma

1. Jenkins'i bir web tarayıcısında açın. **Yeni Öğe**’yi seçin.
2. İş için bir ad girin ve **İşlem Hattı**'nı seçin. **Tamam**’ı seçin.
3. **İşlem Hattı** sekmesini seçin.
4. **Definition** (Tanım) değeri için **Pipeline script from SCM**'yi (SCM'den işlem hattı betiği) seçin.
5. **SCM** değeri olarak **Git**'i seçin. Çatalı oluşturulan deponuzun GitHub URL'sini girin. Örneğin, https://&lt;çatalı_oluşturulan_deponuz>.git.
6. **Script Path** (Betik Yolu) değerini **Jenkinsfile_ftp_plugin** olarak güncelleştirin.
7. **Kaydet**'i seçin ve işi çalıştırın.

## <a name="configure-jenkins-to-deploy-web-app-for-containers"></a>Kapsayıcılar için Web App dağıtmak üzere Jenkins'i yapılandırma

Linux üzerinde Web App, Docker ile dağıtımı destekler. Docker kullanarak Web uygulamanızı dağıtmak için, Web uygulamanızı bir hizmet çalışma zamanı ile Docker görüntüsüne paketleyen bir Dockerfile sağlamanız gerekir. Ardından, Jenkins eklentisi görüntüyü derleyip Docker kayıt defterine gönderir ve görüntüyü web uygulamanıza dağıtır.

Linux üzerinde Web App, yalnızca yerleşik diller (.NET Core, Node.js, PHP ve Ruby) için geçerli olmak üzere Git ve dosya yükleme gibi geleneksel dağıtım yöntemlerini de destekler. Diğer diller için, uygulama kodunuzla hizmet çalışma zamanını birlikte bir Docker görüntüsü olarak paket haline getirmeniz ve dağıtım için Docker'ı kullanmanız gerekir.

İşi Jenkins'de ayarlayabilmeniz için Linux üzerinde bir web uygulamasına ihtiyacınız vardır. Ayrıca özel Docker kapsayıcı görüntülerini depolamak ve yönetmek için bir kapsayıcı kayıt defteri de gereklidir. Kapsayıcı kayıt defterini oluşturmak için DockerHub'ı kullanabilirsiniz. Bu örnekte biz Azure Container Registry'yi kullanacağız.

* [Linux üzerinde web uygulamanızı oluşturun](../app-service/containers/quickstart-nodejs.md).
* Azure Container Registry, açık kaynak Docker Kayıt Defteri 2.0 sürümünü temel alan, yönetilen bir [Docker Kayıt Defteri](https://docs.docker.com/registry/) hizmetidir. [Azure kapsayıcı kayıt defteri oluşturun](/azure/container-registry/container-registry-get-started-azure-cli). DockerHub'ı da kullanabilirsiniz.

### <a name="set-up-the-jenkins-job-for-docker"></a>Docker için Jenkins işini ayarlama

1. Jenkins Panosunda, **freestyle** (serbest stil) türünde yeni bir proje oluşturun.
2. **Source Code Management** (Kaynak Kod Yönetimi) alanını, [Azure'a yönelik basit Java web uygulamasına](https://github.com/azure-devops/javawebappsample) ilişkin yerel çatalınızı kullanacak şekilde yapılandırın. **Depo URL'si** değerini girin. Örneğin: http:\//github.com/&lt;your_ID >/javawebappsample.
3. **Execute shell** komutu ekleyerek projeyi Maven ile oluşturmaya yönelik bir adım ekleyin. Komuta aşağıdaki satırı ekleyin:
    ```bash
    mvn clean package
    ```

4. **Publish an Azure Web App**'i (Azure Web App Yayımla) seçerek derleme sonrası eylem ekleyin.
5. Azure hizmet sorumlusu olarak **mySp** değerini girin. Bu sorumlu, önceki bir adımda [Azure Kimlik Bilgileri](#service-principal) olarak depolanmıştı.
6. **Uygulama Yapılandırması**bölümünde, aboneliğinizdeki bir Linux web uygulamasını ve kaynak grubunu seçin.
7. **Publish via Docker**'ı (Docker aracılığıyla yayımla) seçin.
8. **Dockerfile** yol değerini girin. Varsayılan /Dockerfile değerini tutabilirsiniz.
Azure Container Registry'yi kullanıyorsanız **Docker registry URL** (Docker kayıt defteri URL'si) değeri için URL'yi https://&lt;KayıtDefteriniz>.azurecr.io biçiminde belirtin. DockerHub'ı kullanıyorsanız bu değeri boş bırakın.
9. **Registry credentials** (Kayıt defteri kimlik bilgileri) değeri için kapsayıcı kayıt defterine ilişkin kimlik bilgisini ekleyin. Azure CLI'de aşağıdaki komutları çalıştırarak kullanıcı kimliği ve parola bilgilerini alabilirsiniz. İlk komut, yönetici hesabını etkinleştirir:
    ```azurecli-interactive
    az acr update -n <yourRegistry> --admin-enabled true
    az acr credential show -n <yourRegistry>
    ```

10. **Advanced** (Gelişmiş) sekmesindeki Docker görüntü adı ve etiket değeri isteğe bağlıdır. Varsayılan olarak, görüntü adı değeri, Azure portal'daki **Docker Container** (Docker Kapsayıcısı) ayarlarında yapılandırmış olduğunuz addan alınır. Etiket $BUILD _NUMBER oluşturulur.
    > [!NOTE]
    > Azure portal görüntü adını belirttiğinizden emin olun veya **Gelişmiş** sekmesinde bir **Docker görüntüsü** değeri sağlayın. Bu örnekte, **Docker görüntü** değerini &lt;your_Registry >. azurecr. IO/Hesaplayıcı olarak ayarlayın ve **Docker görüntü etiketi** değerini boş bırakın.

11. Yerleşik bir Docker görüntüsü ayarı kullanırsanız dağıtım başarısız olur. Docker yapılandırmasını, Azure portal'daki **Docker Container** (Docker Kapsayıcısı) ayarlarında özel görüntü kullanacak şekilde değiştirin. Yerleşik görüntüler olduğunda, dağıtım için karşıya dosya yükleme yaklaşımını kullanın.
12. Karşıya dosya yükleme yaklaşımına benzer olarak, **production** dışında farklı bir **Slot** (Yuva) adı seçebilirsiniz.
13. Projeyi kaydedin ve derleyin. Kapsayıcı görüntünüz kayıt defterinize gönderilir ve web uygulaması dağıtılır.

### <a name="deploy-web-app-for-containers-by-using-jenkins-pipeline"></a>Jenkins İşlem Hattını kullanarak Kapsayıcılar için Web App dağıtma

1. GitHub arabiriminde **Jenkinsfile_container_plugin** dosyasını açın. Dosyayı düzenlemek için kalem simgesini seçin. Web uygulamanız için sırasıyla 11. ve 12. satırlardaki **resourceGroup** ve **webAppName** tanımlarını güncelleştirin:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. 13. satırı, kapsayıcı kayıt defteriniz ile değiştirin:
    ```java
    def registryServer = '<registryURL>'
    ```

3. 16. satıcı, Jenkins örneğinizdeki kimlik bilgileri kullanılacak şekilde değiştirin:
    ```java
    azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
    ```

### <a name="create-a-jenkins-pipeline"></a>Jenkins işlem hattı oluşturma    

1. Jenkins'i bir web tarayıcısında açın. **Yeni Öğe**’yi seçin.
2. İş için bir ad girin ve **İşlem Hattı**'nı seçin. **Tamam**’ı seçin.
3. **İşlem Hattı** sekmesini seçin.
4. **Definition** (Tanım) değeri için **Pipeline script from SCM**'yi (SCM'den işlem hattı betiği) seçin.
5. **SCM** değeri olarak **Git**'i seçin. Çatalı oluşturulan deponuzun GitHub URL'sini girin. Örneğin, https://&lt;çatalı_oluşturulan_deponuz>.git.
7. **Script Path** (Script Path) değerini **Jenkinsfile_container_plugin** olarak değiştirin.
8. **Kaydet**'i seçin ve işi çalıştırın.

## <a name="verify-your-web-app"></a>Web uygulamanızı doğrulama

1. WAR dosyasının web uygulamanıza başarıyla dağıtıldığını doğrulamak için bir web tarayıcısı açın.
2. http://&lt;uygulamanızın_adı>.azurewebsites.net/api/calculator/ping sayfasına gidin. &lt;uygulamanızın_adı> kısmını web uygulamanızın adıyla değiştirin. Şu iletiyle karşılaşırsınız:
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jun 17 16:39:10 UTC 2017
    ```

3. http://&lt;uygulamanızın_adı>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> sayfasına gidin. &lt;x> ve &lt;y> değerlerini, x + y toplamını elde etmek istediğiniz sayılarla değiştirin. Hesaplayıcı şu toplamı görüntüler: ![Calculator: add](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-azure-app-service-on-linux"></a>Linux üzerinde Azure App Service için

1. Web uygulamanızı doğrulamak için Azure CLI'da aşağıdaki komutu çalıştırın:
    ```CLI
    az acr repository list -n <myRegistry> -o json
    ```
    Aşağıdaki ileti görüntülenir:
    ```CLI
    ["calculator"]
    ```
    
2. http://&lt;uygulamanızın_adı>.azurewebsites.net/api/calculator/ping sayfasına gidin. &lt;uygulamanızın_adı> kısmını web uygulamanızın adıyla değiştirin. Şu iletiyle karşılaşırsınız: 
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jul 09 16:39:10 UTC 2017
    ```

3. http://&lt;uygulamanızın_adı>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> sayfasına gidin. &lt;x> ve &lt;y> değerlerini, x + y toplamını elde etmek istediğiniz sayılarla değiştirin.
    
## <a name="troubleshooting-the-jenkins-plugin"></a>Jenkins eklentisiyle ilgili sorunlarını giderme

Jenkins eklentileriyle ilgili hatalarla karşılaşırsanız [Jenkins JIRA](https://issues.jenkins-ci.org/) sayfasında söz konusu bileşenle ilgili sorun bildirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure'a dağıtım yapmak için Azure App Service Jenkins eklentisini kullandınız.

Şunları öğrendiniz:

> [!div class="checklist"]
> * Jenkins'i dosya yükleme üzerinden Azure App Service'a dağıtım yapacak şekilde yapılandırma 
> * Jenkins'i Kapsayıcılar için Web App'e dağıtım yapacak şekilde yapılandırma 
