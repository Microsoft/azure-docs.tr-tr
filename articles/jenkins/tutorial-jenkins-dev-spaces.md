---
title: Azure Kubernetes hizmeti ile Jenkins için Azure Dev Spaces eklentisini kullanma
description: Azure Dev Spaces eklentisinin sürekli tümleştirme ardışık düzeninde nasıl kullanılacağını öğrenin.
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 42d732cda26f0c34f0a54fffc0b1b9c54def94ad
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158739"
---
# <a name="tutorial-using-the-azure-dev-spaces-plug-in-for-jenkins-with-azure-kubernetes-service"></a>Öğretici: Azure Kubernetes hizmeti ile Jenkins için Azure Dev Spaces eklentisi kullanma 

Azure Dev Spaces, Azure Kubernetes hizmeti 'nde (AKS) çalışan mikro hizmet uygulamanızı, bağımlılıkları çoğaltma veya sahte olarak kullanmanıza gerek kalmadan test etmenize ve yineleyebilir. Jenkins için Azure Dev Spaces eklentisi, geliştirme alanlarını sürekli tümleştirme ve teslim (CI/CD) ardışık düzeninde kullanmanıza yardımcı olur.

Bu öğretici Ayrıca Azure Container Registry (ACR) kullanır. ACR görüntüleri depolar ve bir ACR görevi Docker ve Helm yapıtları oluşturur. Yapıt oluşturma için ACR ve ACR görevi kullanmak, Jenkins sunucunuza Docker gibi ek yazılım yüklemenize gerek ortadan kaldırır. 

Bu öğreticide, şu görevleri tamamlayacaksınız:

> [!div class="checklist"]
> * Azure Dev Spaces etkinleştirilmiş bir AKS kümesi oluşturma
> * AKS 'e çoklu hizmet uygulaması dağıtma
> * Jenkins sunucunuzu hazırlama
> * Kod değişikliklerini projeye birleştirmeden önce önizlemek için Jenkins ardışık düzeninde Azure Dev Spaces eklentisini kullanın

Bu öğreticide, temel Azure Hizmetleri, AKS, ACR, Azure Dev Spaces, Jenkins işlem [hatları](https://jenkins.io/doc/book/pipeline/) ve eklentileri ve GitHub hakkında ara bilgi varsayılmaktadır. Kubectl ve Held gibi destekleyici araçların temel bilgileri yararlıdır.

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure hesabı. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

* GitHub hesabı. GitHub hesabınız yoksa başlamadan önce [ücretsiz bir hesap](https://github.com/) oluşturun.

* [Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) uzantısı yüklü [Visual Studio Code](https://code.visualstudio.com/download) .

* [Azure CLI yüklü](/cli/azure/install-azure-cli?view=azure-cli-latest), sürüm 2.0.43 veya üzeri.

* Bir Jenkins ana sunucusu. Zaten bir Jenkins ana hesabınız yoksa, bu [hızlı](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template)başlangıçta bulunan adımları izleyerek Azure 'Da [jenkins](https://aka.ms/jenkins-on-azure) dağıtın. 

* Jenkins sunucusunda, Bu öğreticinin ilerleyen kısımlarında açıklandığı gibi, hem helk hem de kubectl yüklü ve Jenkins hesabı için kullanılabilir olmalıdır.

* VS Code, VS Code terminali veya WSL ve Bash. 


## <a name="create-azure-resources"></a>Azure kaynakları oluşturma

Bu bölümde Azure kaynakları oluşturursunuz:

* Bu öğretici için tüm Azure kaynaklarını içeren bir kaynak grubu.
* Bir [Azure Kubernetes hizmeti](https://docs.microsoft.com/azure/aks/) (aks) kümesi.
* Derlenecek (ACR görevlerini kullanarak) ve Docker görüntülerini depolayabilen bir [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) (ACR).

1. Bir kaynak grubu oluşturun.

    ```bash
    az group create --name MyResourceGroup --location westus2
    ```

2. AKS kümesi oluşturma. AKS kümesini [geliştirme alanlarını destekleyen bir bölgede](../dev-spaces/about.md#supported-regions-and-configurations)oluşturun.

    ```bash
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. AKS 'leri geliştirme alanlarını kullanacak şekilde yapılandırın.

    ```bash
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    Bu adım `azds` CLı uzantısını yüklüyor.

4. Bir kapsayıcı kayıt defteri oluşturun.

    ```bash
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>Örnek uygulamaları AKS kümesine dağıtma

Bu bölümde, bir dev alanı ayarlarsınız ve son bölümde oluşturduğunuz AKS kümesine örnek bir uygulama dağıtırsınız. Uygulama, *webön uç* ve *mywebapi*olmak üzere iki bölümden oluşur. Her iki bileşen de bir geliştirme alanında dağıtılır. Bu öğreticide daha sonra Jenkins 'te CI işlem hattını tetiklemek için mywebapi 'e karşı bir çekme isteği göndereceğiz.

Azure Dev Spaces ile Azure Dev Spaces ve çoklu hizmet geliştirmeyi kullanma hakkında daha fazla bilgi için bkz. [Java ile Azure dev Spaces kullanmaya başlama](https://docs.microsoft.com/azure/dev-spaces/get-started-java)ve [Azure dev Spaces ile çoklu hizmet geliştirme](https://docs.microsoft.com/azure/dev-spaces/multi-service-java). Bu öğreticiler burada bulunmayan ek arka plan bilgileri sağlar.

1. https://github.com/Azure/dev-spaces depoyu GitHub 'dan indirin.

2. VS Code `samples/java/getting-started/webfrontend` klasörü açın. (Hata ayıklama varlıkları eklemek veya projeyi geri yüklemek için tüm varsayılan istemleri yoksayabilirsiniz.)

3. Aşağıdaki gibi görünmesi için `/src/main/java/com/ms/sample/webfrontend/Application.java` güncelleştirin:

    ```java
    package com.ms.sample.webfrontend;

    import java.io.*;
    import java.net.*;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.web.bind.annotation.*;

    @SpringBootApplication
    @RestController
    public class Application {
        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }

        @RequestMapping(value = "/greeting", produces = "text/plain")
        public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
            URLConnection conn = new URL("http://mywebapi/").openConnection();
            conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
            try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
            {
                return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
            }
        }
    }
    ```

4. VS Code ' de tümleşik terminali açmak için **görüntüle** **' ye tıklayın** .

5. Uygulamanızı bir geliştirme alanında çalışacak şekilde hazırlamak için `azds prep` komutunu çalıştırın. Uygulamanızı doğru şekilde hazırlamak için bu komutun `dev-spaces/samples/java/getting-started/webfrontend` 'den çalıştırılması gerekir:

    ```bash
    azds prep --public
    ```

    Dev Spaces CLı 'nın `azds prep` komutu, varsayılan ayarlarla Docker ve Kubernetes varlıkları oluşturur. Bu dosyalar projenin ömrü boyunca kalır ve özelleştirilebilir:

    * `./Dockerfile` ve `./Dockerfile.develop` uygulamanın kapsayıcı görüntüsünü ve kaynak kodun, kapsayıcı içinde nasıl oluşturulup çalışacağını açıklamaktadır.
    * [ altındaki ](https://helm.sh/docs/developing_charts/)Helm grafiği`./charts/webfrontend`, kapsayıcının Kubernetes'de nasıl dağıtıldığını açıklar.
    * `./azds.yaml`, Azure Dev Spaces yapılandırma dosyasıdır.

    Daha fazla bilgi için bkz. [Azure dev Spaces nasıl çalıştığını ve nasıl yapılandırılır](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works).

6. `azds up` komutunu kullanarak AKS 'te uygulamayı derleyin ve çalıştırın:

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>`up` komutu tarafından oluşturulan URL hakkında daha fazla bilgi için konsol çıkışını tarayın. Şu biçimde olacaktır:

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    Bu URL 'YI bir tarayıcı penceresinde açın ve Web uygulamasını görmeniz gerekir. Kapsayıcı yürütülürken, terminal penceresine `stdout` ve `stderr` çıkışının akışı yapılır.

8. Ardından, *mywebapi*'i ayarlama ve dağıtma:

    1. Dizini `dev-spaces/samples/java/getting-started/mywebapi` olarak değiştir

    2. Çalıştırın

        ```bash
        azds prep
        ```

    3. Çalıştırın

        ```bash
        azds up -d
        ```

## <a name="prepare-jenkins-server"></a>Jenkins sunucusunu hazırlama

Bu bölümde, Jenkins sunucusunu örnek CI işlem hattını çalıştıracak şekilde hazırlarsınız.

* Eklentileri yükler
* Helk ve Kubernetes CLı 'yı yükler
* Kimlik bilgileri ekle

### <a name="install-plug-ins"></a>Eklentileri yükler

1. Jenkins sunucunuzda oturum açın. **Eklentileri yönet > Jenkins 'ı Yönet**' i seçin.
2. **Kullanılabilir** sekmesinde, aşağıdaki eklentileri seçin:
    * [Azure Dev Spaces](https://plugins.jenkins.io/azure-dev-spaces)
    * [Azure Container Registry görevler](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [Ortam Injector](https://plugins.jenkins.io/envinject)
    * [GitHub tümleştirmesi](https://plugins.jenkins.io/github-pullrequest)

    Bu eklentiler listede görünmezse, zaten yüklü olup olmadığını görmek için **yüklü** sekmesini işaretleyin.

3. Eklentileri yüklemek için **Şimdi İndir ' i ve yeniden başlattıktan sonra Yükle**' yi seçin.

4. Yüklemeyi gerçekleştirmek için Jenkins sunucunuzu yeniden başlatın.

### <a name="install-helm-and-kubectl"></a>Helk ve kubectl 'yi yükler

Örnek işlem hattı, geliştirme alanına dağıtmak için helk ve kubectl kullanır. Jenkins yüklendiğinde, *Jenkins*adlı bir yönetici hesabı oluşturur. Jenkins kullanıcısına hem helk hem de kubectl 'nin erişilebilir olması gerekir.

1. Jenkins ana ile bir SSH bağlantısı oluşturun. 

2. `jenkins` kullanıcısına geç:
    ```bash
    sudo su jenkins
    ```

3. Held CLı 'yı yükler. Daha fazla bilgi için bkz. [Held 'Yi yükleme](https://helm.sh/docs/using_helm/#installing-helm).

4. Kubectl 'yi yükler. Daha fazla bilgi için bkz. [**az ACS Kubernetes Install-CLI**](https://helm.sh/docs/using_helm/#installing-helm).

### <a name="add-credentials-to-jenkins"></a>Jenkins 'e kimlik bilgileri ekleme

1. Jenkins 'in kimlik doğrulaması ve Azure kaynaklarına erişim için bir Azure hizmet sorumlusu olması gerekir. Hizmet sorumlusu oluşturmak için, dağıtım Azure App Service öğreticisindeki [hizmet sorumlusu oluşturma](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) bölümüne bakın. Sonraki adımı tamamlaması için bu bilgilere ihtiyacınız olduğundan çıkışın bir kopyasını `create-for-rbac` kaydettiğinizden emin olun. Çıktı şuna benzer şekilde görünecektir:

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Önceki adımdaki hizmet sorumlusu bilgilerini kullanarak Jenkins 'te bir *Microsoft Azure hizmet sorumlusu* kimlik bilgisi türü ekleyin. Aşağıdaki ekran görüntüsündeki adlar `create-for-rbac`çıktılarına karşılık gelir.

    **Kimlik** alanı, hizmet sorumlunuz Için Jenkins kimlik bilgisi adıdır. Örnek, `displayName` değerini (Bu örnekte, `xxxxxxxjenkinssp`) kullanır, ancak istediğiniz herhangi bir metni kullanabilirsiniz. Bu kimlik bilgisi adı, sonraki bölümde AZURE_CRED_ID ortam değişkeninin değeridir.

    ![Jenkins 'e hizmet sorumlusu kimlik bilgileri ekleme](media/tutorial-jenkins-dev-spaces/add-service-principal-credentials.png)

    **Açıklama** isteğe bağlıdır. Daha ayrıntılı yönergeler için, bkz. Azure App Service öğreticideki [Jenkins 'e hizmet sorumlusu ekleme](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins) bölümüne bakın. 



3. ACR kimlik bilgilerinizi görüntülemek için şu komutu çalıştırın:

    ```bash
    az acr credential show -n <yourRegistryName>
    ```

    JSON çıkışının bir kopyasını oluşturun ve şuna benzer şekilde görünmelidir:

    ```json
    {
      "passwords": [
        {
          "name": "password",
          "value": "vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz"
        },
        {
          "name": "password2",
          "value": "zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz"
        }
      ],
      "username": "acr01"
    }
    ```

4. Jenkins 'te parola kimlik bilgisi türü *olan bir Kullanıcı adı* ekleyin. **Kullanıcı adı** , son adımdaki kullanıcı adıdır, bu örnekte `acr01`. **Parola** , ilk parolanın değeridir, bu örnekte `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`. Bu kimlik bilgisinin **kimliği** ACR_CRED_ID değeridir.

5. AKS kimlik bilgilerini ayarlayın. Jenkins 'te bir *Kubernetes yapılandırma (kubeconfig)* kimlik bilgisi türü ekleyin ("doğrudan gir" seçeneğini kullanın). AKS kümenizin erişim kimlik bilgilerini almak için aşağıdaki komutu çalıştırın:

    ```cmd
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   Bu kimlik bilgisinin **kimliği** , sonraki bölümde KUBE_CONFIG_ID değeridir.

## <a name="create-a-pipeline"></a>İşlem hattı oluşturma

Örnek işlem hattı için seçilen senaryo gerçek bir düzeni temel alır: bir çekme isteği, test ve gözden geçirmek için önerilen değişiklikleri oluşturan ve ardından Azure dev alanına dağıtan bir CI işlem hattını tetikler. İncelemenin sonucuna bağlı olarak, değişiklikler birleştirilir ve AKS 'e dağıtılır ya da atılır. Son olarak, dev alanı kaldırılır.

Jenkins ardışık düzen yapılandırması ve Jenkinsfile, CI işlem hattının aşamalarını tanımlar. Bu akış çizelgesi, Jenkinsfile tarafından tanımlanan işlem hattı aşamalarını ve karar noktalarını gösterir:

![Jenkins ardışık düzen akışı](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-flow.png)

1. https://github.com/azure-devops/mywebapi*mywebapi* projesinin değiştirilmiş bir sürümünü indirin. Bu proje, *Jenkinsfile*, *Dockerfiles*ve helk grafik gibi bir işlem hattı oluşturmak için gereken çeşitli dosyaları içerir.

2. Jenkins 'de oturum açın. Soldaki menüden **öğe Ekle**' yi seçin.

3. İşlem **hattı**' nı seçin ve ardından bir **öğe adı girin** kutusuna bir ad girin. **Tamam**' ı seçin ve ardından işlem hattı yapılandırma ekranı otomatik olarak açılır.

4. **Genel** sekmesinde, **çalıştırma Için bir ortam hazırlayın**' ı işaretleyin. 

5. **Jenkins ortam değişkenlerini koruyun** ve **Jenkins derleme değişkenlerini koruyun**.

6. **Özellikler içerik** kutusuna aşağıdaki ortam değişkenlerini girin:

    ```
    AZURE_CRED_ID=[your credential ID of service principal]
    RES_GROUP=[your resource group of the function app]
    ACR_RES_GROUP=[your ACR resource group]
    ACR_NAME=[your ACR name]
    ACR_REGISTRY=[your ACR registry url, without http schema]
    ACR_CRED_ID=[your credential id of your ACR account]
    AKS_RES_GROUP=[your AKS resource group]
    AKS_NAME=[your AKS name]
    IMAGE_NAME=[name of Docker image you will push to ACR, without registry prefix]
    KUBE_CONFIG_ID=[your kubeconfig id]
    PARENT_DEV_SPACE=[shared dev space name]
    TEST_ENDPOINT=[your web frontend end point for testing. Should be webfrontend.XXXXXXXXXXXXXXXXXXX.xxxxxx.aksapp.io]
    ```

    Önceki bölümlerde verilen örnek değerleri kullanarak, ortam değişkenlerinin listesi şuna benzer görünmelidir:

    ![Jenkins ardışık düzen ortam değişkenleri](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-environment.png)

7. İşlem **hattı > TANıMıNDA** **SCM 'den işlem hattı betiği** seçin.
8. **SCM**'de **Git** ' i SEÇIN ve ardından depo URL 'nizi girin.
9. **Dal belirticisi**' nde `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`girin.
10. SCM deposu URL 'sini ve "Jenkinsfile" komut dosyası yolunu girin.
11. **Hafif kullanıma alma** denetlenmelidir.

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>İşlem hattını tetiklemek için çekme isteği oluşturma

Bu bölümdeki 3. adımı tamamlayabilmeniz için Jenkinsfile ' ın bir kısmına yorum yapmanız gerekir, aksi halde yeni ve eski sürümleri yan yana görüntülemeye çalıştığınızda 404 hatası alırsınız. Varsayılan olarak, çekme isteğini birleştirmeyi seçtiğinizde, mywebapi öğesinin önceki paylaşılan sürümü kaldırılacak ve yeni sürüm tarafından değiştirilmeyecektir. Adım 1 ' i tamamlamadan önce Jenkinsfile dosyasında aşağıdaki değişikliği yapın:

```Groovy
    if (userInput == true) {
        stage('deploy') {
            // Apply the deployment to shared namespace in AKS using acsDeploy, Helm or kubectl   
        }
        
        stage('Verify') {
            // verify the staging environment is working properly
        }
        
        /* Comment the cleanup stage to allow side by side comparison with the new child dev space

        stage('cleanup') {
            devSpacesCleanup aksName: env.AKS_NAME, 
                azureCredentialsId: env.AZURE_CRED_ID, 
                devSpaceName: devSpaceNamespace, 
                kubeConfigId: env.KUBE_CONFIG_ID, 
                resourceGroupName: env.AKS_RES_GROUP,
                helmReleaseName: releaseName
        }
        */

    } else {
        // Send a notification
    }
```

1. `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java`bir değişiklik yapın ve ardından bir çekme isteği oluşturun. Örneğin:

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. Jenkins 'de oturum açın ve işlem hattı adını seçip **Şimdi Oluştur**' u seçin. 

    Ayrıca, Jenkins ardışık düzenini otomatik olarak tetiklemek için bir *Web kancası* da ayarlayabilirsiniz. Bir çekme isteği girildiğinde, GitHub, ardışık düzeni tetikleyen Jenkins 'e bir GÖNDERI yayınlar. Web kancası ayarlama hakkında daha fazla bilgi için bkz. [Jenkins 'ı GitHub 'A bağlama](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service.md#connect-jenkins-to-github).

3. Değişiklikleri geçerli paylaşılan sürümle karşılaştırın:

    1. Tarayıcınızı açın ve `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`paylaşılan sürüme gidin. TEST_ENDPOINT URL 'YI içerir.

    2. Başka bir sekme açın ve ardından PR dev Space URL 'sini girin. `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`benzer olacaktır. Bağlantıyı, Jenkins işi için derleme **geçmişi > < Build # > > konsol çıktısına** bulacaksınız. Sayfada `aksapp`arayın veya yalnızca öneki görmek için, `azdsprefix`arayın.

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>Alt dev alanının URL 'sini oluşturma

Bir çekme isteği oluşturduğunuzda, Jenkins ekibin paylaşılan dev alanını temel alan bir alt dev alanı oluşturur ve bu alt dev alanındaki çekme talebinizden kodu çalıştırır. Alt dev Space URL 'SI `http://$env.azdsprefix.<test_endpoint>`formu alır. 

**$env. azdspredüzeltmesini** , **Devspacescreate**tarafından Azure dev Spaces eklentisinin işlem hattı yürütmesi sırasında ayarlanır:

```Groovy
stage('create dev space') {
    devSpacesCreate aksName: env.AKS_NAME,
        azureCredentialsId: env.AZURE_CRED_ID,
        kubeconfigId: env.KUBE_CONFIG_ID,
        resourceGroupName: env.AKS_RES_GROUP,
        sharedSpaceName: env.PARENT_DEV_SPACE,
        spaceName: devSpaceNamespace
}
```

`test_endpoint`, [AKS kümesine örnek uygulamalar dağıtma '](#test_endpoint)da daha önce `azds up`kullanarak dağıttığınız webön uç uygulamasının URL 'Sidir, adım 7 ' dir. `$env.TEST_ENDPOINT` değeri, işlem hattı yapılandırmasında ayarlanır. 

Aşağıdaki kod parçacığı, `smoketest` aşamada alt dev Space URL 'sinin nasıl kullanıldığını gösterir. Kod, alt dev Space TEST_ENDPOINT kullanılabilir olup olmadığını denetler ve varsa, selamlama metnini stdout 'a indirir:

```Groovy
stage('smoketest') {
    // CI testing against http://$env.azdsprefix.$env.TEST_ENDPOINT" 
    SLEEP_TIME = 30
    SITE_UP = false
    for (int i = 0; i < 10; i++) {
        sh "sleep ${SLEEP_TIME}"
        code = "0"
        try {
            code = sh returnStdout: true, script: "curl -sL -w '%{http_code}' 'http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting' -o /dev/null"
        } catch (Exception e){
            // ignore
        }
        if (code == "200") {
            sh "curl http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting"
            SITE_UP = true
            break
        }
    }
    if(!SITE_UP) {
        echo "The site has not been up after five minutes"
    }
}
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Örnek uygulamayı kullanarak işiniz bittiğinde, kaynak grubunu silerek Azure kaynaklarını temizleyin:

```bash
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Jenkins için Azure Dev Spaces eklentisinin ve bir geliştirme alanına dağıtım ve dağıtım alanı için Azure Container Registry eklentisinin nasıl kullanılacağını öğrendiniz.

Aşağıdaki kaynak listesi Azure Dev Spaces, ACR görevleri ve Jenkins ile CI/CD hakkında daha fazla bilgi sağlar.

Azure Dev Spaces:
* [Azure Dev Spaces çalışma ve yapılandırma süreçleri](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works)

ACR görevleri:
* [ACR Görevleri ile işletim sistemi ve çerçeve düzeltme eki uygulamayı otomatikleştirme](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)
* [Kod işlemede otomatik derleme](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)

Azure 'da Jenkins ile CI/CD:
* [Jenkins sürekli dağıtımı](https://docs.microsoft.com/azure/aks/jenkins-continuous-deployment)
