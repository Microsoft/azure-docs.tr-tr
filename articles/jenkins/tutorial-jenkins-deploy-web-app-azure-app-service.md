---
title: "Öğretici: Jenkins ile Azure App Service GitHub 'dan dağıtma"
description: GitHub ve sürekli dağıtım (CD) için Jenkins 'i, Java Web uygulamaları için Azure App Service ayarlama
ms.topic: tutorial
ms.date: 10/23/2019
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 9fcf178b71ac1f07bfb58cd2502701ae5392b472
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158391"
---
# <a name="tutorial-deploy-from-github-to-azure-app-service-with-jenkins-continuous-integration-and-deployment"></a>Öğretici: Jenkins sürekli tümleştirme ve dağıtım ile Azure App Service GitHub 'dan dağıtma

Bu öğretici, Jenkins 'te sürekli tümleştirme (CI) ve sürekli dağıtım (CD) ayarlayarak GitHub 'dan [Linux 'a Azure App Service](/azure/app-service/containers/app-service-linux-intro) için örnek bir Java Web uygulaması dağıtır. Yürütmeleri GitHub 'a ileterek uygulamayı güncelleştirdiğinizde Jenkins Azure App Service için uygulamanızı otomatik olarak oluşturur ve yeniden oluşturur. Bu öğreticideki örnek uygulama [Spring Boot](https://projects.spring.io/spring-boot/) Framework kullanılarak geliştirilmiştir. 

![Dağıtıma Azure App Service için GitHub 'a genel bakış](media/tutorial-jenkins-deploy-web-app-azure-app-service/azure-continuous-integration-deployment-overview.png)

Bu öğreticide, şu görevleri tamamlayacaksınız:

> [!div class="checklist"]
> * GitHub 'dan derlemek, Azure App Service dağıtmak ve diğer ilgili görevler için Jenkins eklentilerini yükleyebilirsiniz.
> * Örnek GitHub deposu, çalışan bir kopyaya sahip olacak şekilde çatalla.
> * Jenkins 'i GitHub 'a bağlayın.
> * Jenkins 'in kimlik bilgilerinizi kullanmadan Azure 'a erişebilmesi için bir Azure hizmet sorumlusu oluşturun.
> * Hizmet sorumlunuzu Jenkins 'e ekleyin.
> * GitHub 'da uygulamayı her güncelleştirdiğinizde örnek uygulamayı oluşturup dağıtan Jenkins ardışık düzenini oluşturun.
> * Jenkins işlem hattı için derleme ve dağıtım dosyaları oluşturun.
> * Jenkins işlem hattınızı derleme ve dağıtım betiğine getirin.
> * El ile oluşturulmuş bir derlemeyi çalıştırarak örnek uygulamanızı Azure 'a dağıtın.
> * Azure 'da derleme ve yeniden dağıtmaya yönelik Jenkins 'i tetikleyen GitHub 'da bir uygulama güncelleştirmesi gönderin.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için şu öğeler gereklidir:

* Java Development Kit (JDK) ve Azure Linux VM 'de yüklü Maven araçları içeren bir [Jenkins](https://jenkins.io/) sunucusu

  Jenkins sunucunuz yoksa şu adımları Azure portal: [Azure LINUX VM üzerinde Jenkins sunucusu oluşturma](/azure/jenkins/install-jenkins-solution-template)

* Örnek Java Web uygulaması için çalışma kopyası (çatal) alabilmeniz için bir [GitHub](https://github.com) hesabı. 

* Yerel komut satırınızdan veya [Azure Cloud Shell](/azure/cloud-shell/overview) ÇALıŞTıRABILECEĞINIZ [Azure CLI](/cli/azure/install-azure-cli)

## <a name="install-jenkins-plug-ins"></a>Jenkins eklentilerini yükleme

1. Jenkins Web konsolunuzda şu konumda oturum açın:

   `https://<Jenkins-server-name>.<Azure-region>.cloudapp.azure.com`

1. Jenkins ana sayfasında, **Eklentileri yönet** > **Jenkins 'i Yönet** ' i seçin.

   ![Jenkins eklentilerini yönetme](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-plugins.png)

1. **Kullanılabilir** sekmesinde şu eklentileri seçin:

   - [Azure Uygulama Hizmeti](https://plugins.jenkins.io/azure-app-service)
   - [GitHub dal kaynağı](https://plugins.jenkins.io/github-branch-source)
   - Jenkins [ortamı Injector eklentisi](https://plugins.jenkins.io/envinject)
   - [Azure kimlik bilgileri](https://plugins.jenkins.io/azure-credentials)

   Bu eklentiler görünmezse, **yüklü** sekmesini denetleyerek bu bileşenlerin zaten yüklü olmadığından emin olun.

1. Seçtiğiniz eklentileri yüklemek için **Şimdi İndir ' i ve yeniden başlattıktan sonra Yükle**' yi seçin.

1. İşiniz bittiğinde, Jenkins menüsünde, sonraki adımlar için Jenkins yönetim sayfasına geri dönebilmeniz için **Jenkins 'ı Yönet** ' i seçin.

## <a name="fork-sample-github-repo"></a>Çatal örnek GitHub deposu

1. [Spring Boot örnek uygulaması Için GitHub deposu ' nda oturum açın](https://github.com/spring-guides/gs-spring-boot). 

1. GitHub 'daki sağ üst köşede **çatal**' ı seçin.

   ![GitHub 'dan çatalla örnek depo](media/tutorial-jenkins-deploy-web-app-azure-app-service/fork-github-repo.png)

1. GitHub hesabınızı seçmek ve bu adımları izlemek için istemleri izleyin.

Sonra, GitHub kimlik bilgilerinizle Jenkins 'i ayarlayın.

## <a name="connect-jenkins-to-github"></a>Jenkins 'i GitHub 'a bağlama

Jenkins 'in GitHub çatalınızda Web uygulamanıza yeni işlemeler itilmesi durumunda GitHub ve yanıt verme işlemlerini izlemesini sağlamak için Jenkins 'te [GitHub Web kancalarını](https://developer.github.com/webhooks/) etkinleştirin.

> [!NOTE]
> 
> Bu adımlar, GitHub Kullanıcı adınızı ve parolanızı kullanarak GitHub ile çalışmak için Jenkins için kişisel erişim belirteci kimlik bilgileri oluşturur. 
> Ancak, GitHub hesabınız iki öğeli kimlik doğrulaması kullanıyorsa, belirtecinizi GitHub 'da oluşturun ve bu belirteci kullanmak için Jenkins 'i ayarlayın. 
> Daha fazla bilgi için bkz. [Jenkins GitHub eklentisi](https://wiki.jenkins.io/display/JENKINS/GitHub+Plugin) belgeleri.

1. **Jenkins 'ı Yönet** sayfasında, **sistemi Yapılandır**' ı seçin. 

   ![Jenkins 'te sistem yapılandırma](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-configure-system.png)

1. **GitHub** bölümünde GitHub sunucunuzun ayrıntılarını sağlayın. **GitHub sunucusu Ekle** listesinden **GitHub sunucusu**' nu seçin. 

   ![Jenkins 'te GitHub sunucusu ekleme](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-GitHub-server.png)

1. **Kancaları Yönet** özelliği seçili değilse, bu özelliği seçin. Diğer ayarları belirtmek için **Gelişmiş** ' i seçin. 

   ![GitHub sunucusu için gelişmiş Jenkins ayarlarını belirtin](media/tutorial-jenkins-deploy-web-app-azure-app-service/advanced-GitHub-settings.png)

1. **Ek GitHub eylemlerini yönet** listesinden **oturum açma ve parolayı belirtece Dönüştür**' ü seçin.

   ![GitHub için oturum açma ve parolayı belirtece Dönüştür](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-additional-actions.png)

1. GitHub Kullanıcı adınızı ve parolanızı girebilmeniz için **oturum açma ve parola '** yı seçin. İşiniz bittiğinde, [GitHub kişisel erişim belirteci (Pat)](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)oluşturan **belirteç kimlik bilgileri oluştur**' u seçin.   

   ![Oturum açma ve paroladan GitHub PAT oluştur](media/tutorial-jenkins-deploy-web-app-azure-app-service/create-github-token-credentials.png)

1. **GitHub sunucusu** bölümünde, **kimlik bilgileri** listesinden yeni belirtecinizi seçin. **Bağlantıyı Sına**' yı seçerek kimlik doğrulamasının çalıştığını denetleyin.

   ![Yeni PAT ile GitHub sunucusu bağlantısını denetleyin](media/tutorial-jenkins-deploy-web-app-azure-app-service/check-github-connection.png)

Daha sonra Jenkins 'in Azure kaynaklarına kimlik doğrulaması ve erişim için kullandığı Azure hizmet sorumlusunu oluşturun.

## <a name="create-service-principal"></a>Hizmet sorumlusu oluşturma

Daha sonraki bir bölümde, GitHub 'dan uygulamanızı oluşturan ve uygulamanızı Azure App Service dağıtan bir Jenkins ardışık düzen işi oluşturursunuz. Jenkins 'in kimlik bilgilerinizi girmeden Azure 'a erişmesini sağlamak için, Jenkins için Azure Active Directory ' de bir [hizmet sorumlusu](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) oluşturun. Hizmet sorumlusu, Jenkins 'in Azure kaynaklarına erişim kimlik doğrulaması için kullanabileceği ayrı bir kimliktir. Bu hizmet sorumlusunu oluşturmak için, yerel komut satırınızdan ya da Azure Cloud Shell [ **`az ad sp create-for-rbac`** ](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)Azure CLI komutunu çalıştırın, örneğin: 

```azurecli-interactive
az ad sp create-for-rbac --name "yourAzureServicePrincipalName" --password yourSecurePassword
```

Hizmet sorumlusu adının etrafında tırnak işaretleri kullandığınızdan emin olun. Ayrıca, [Azure Active Directory parola kuralları ve kısıtlamalarına](/azure/active-directory/active-directory-passwords-policy)göre güçlü bir parola oluşturun. Parola sağlamazsanız, Azure CLı sizin için bir parola oluşturur. 

İşte **`create-for-rbac`** komutu tarafından oluşturulan çıktı: 

```json
{
   "appId": "yourAzureServicePrincipal-ID", // A GUID such as AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA
   "displayName": "yourAzureServicePrincipalName", // A user-friendly name for your Azure service principal
   "name": "http://yourAzureServicePrincipalName",
   "password": "yourSecurePassword",
   "tenant": "yourAzureActiveDirectoryTenant-ID" // A GUID such as BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB
}
```

> [!TIP]
> 
> Zaten bir hizmet sorumlunuz varsa, bunun yerine kimliği yeniden kullanabilirsiniz.
> Kimlik doğrulaması için hizmet sorumlusu değerleri sağlarken, `appId`, `password`ve `tenant` özellik değerlerini kullanın. 
> Mevcut bir hizmet sorumlusunu ararken `displayName` özellik değerini kullanın.

## <a name="add-service-principal-to-jenkins"></a>Jenkins 'e hizmet sorumlusu ekleme

1. Jenkins ana sayfasında **kimlik bilgileri** > **sistem**' i seçin. 

1. **Sistem** sayfasında, **etki alanı**altında **Küresel kimlik bilgileri (Kısıtlanmamış)** seçeneğini belirleyin.

1. Sol menüden **kimlik bilgileri ekle**' yi seçin.

1. **Tür** listesinden **Azure hizmet sorumlusu**' nı seçin.

1. Bu adımdaki tabloda açıklanan özelliklerde hizmet sorumlusu ve Azure aboneliğiniz için bilgi sağlayın:

   ![Azure hizmet sorumlusu kimlik bilgilerini ekleme](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-service-principal-credentials.png)

   | Özellik | Değer | Açıklama | 
   |----------|-------|-------------| 
   | **Abonelik KIMLIĞI** | <*Yourazuyeniden komut dosyası kimliği*> | Azure aboneliğinizin GUID değeri <p>**İpucu**: Azure abonelik kimliğinizi bilmiyorsanız, komut satırından veya Cloud Shell bu Azure CLI komutunu çalıştırın ve `id` GUID değerini kullanın: <p>`az account list` | 
   | **İstemci kimliği** | <*yourAzureServicePrincipal-kimliği*> | Azure hizmet sorumlusu için önceden oluşturulan `appId` GUID değeri | 
   | **İstemci parolası** | <*Yoursecurepassword*> | Azure hizmet sorumlusu için verdiğiniz `password` değeri veya "gizli dizi" | 
   | **Kiracı KIMLIĞI** | <*yourAzureActiveDirectoryTenant-kimliği*> | Azure Active Directory kiracınız için `tenant` GUID değeri | 
   | **ID** | <*yourAzureServicePrincipalName*> | Azure hizmet sorumlunuz için `displayName` değeri | 

1. Hizmet sorumlunun çalıştığını doğrulamak için **hizmet sorumlusunu doğrula**' yı seçin. İşiniz bittiğinde **Tamam**’ı seçin.

Ardından, uygulamanızı oluşturup dağıtan Jenkins işlem hattını oluşturun.

## <a name="create-jenkins-pipeline"></a>Jenkins işlem hattı oluşturma

Jenkins 'de, uygulamanızı oluşturmak ve dağıtmak için işlem hattı işi oluşturun.

1. Jenkins giriş sayfanıza dönün ve **Yeni öğe**' yi seçin. 

   ![Jenkins işlem hattı oluşturma](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-new-item.png)

1. İşlem hattı işiniz için bir ad sağlayın (örneğin, "My-Java-Web-App") ve işlem **hattı**' nı seçin. En altta **Tamam**' ı seçin.  

   ![Jenkins ardışık düzen işini adlandırın](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-pipeline.png)

1. Jenkins 'in kendi kimlik bilgilerinizi kullanmadan Azure 'a dağıtabilmesi için, hizmet sorumlusu ile Jenkins 'i ayarlayın.

   1. **Genel** sekmesinde, **çalıştırma Için bir ortam hazırla**' yı seçin. 

   1. Görüntülenen **Özellikler içerik** kutusunda bu ortam değişkenlerini ve değerlerini ekleyin. 

      ```ini
      AZURE_CRED_ID=yourAzureServicePrincipalName
      RES_GROUP=yourWebAppAzureResourceGroupName
      WEB_APP=yourWebAppName
      ```

      ![Çalıştırma için bir ortam hazırlayın ve ortam değişkenlerini ayarlayın](media/tutorial-jenkins-deploy-web-app-azure-app-service/prepare-environment-for-jenkins-run.png)

1. İşiniz bittiğinde **Kaydet**'i seçin.

Daha sonra Jenkins için derleme ve dağıtım betikleri oluşturun.

## <a name="create-build-and-deployment-files"></a>Derleme ve dağıtım dosyaları oluştur

Artık Jenkins 'in uygulamanızı oluşturmak ve dağıtmak için kullandığı dosyaları oluşturun.

1. GitHub çatalınızın `src/main/resources/` klasöründe, bu XML içeren, ancak `$(JAR_FILE_NAME)` ' i `gs-spring-boot-0.1.0.jar`ile `web.config`adlı bu uygulama yapılandırma dosyasını oluşturun:

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration>
      <system.webServer>
         <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
         </handlers>
         <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\${JAR_FILE_NAME}&quot;"></httpPlatform>
      </system.webServer>
   </configuration>
   ```

1. GitHub çatalınızın kök klasöründe, bu metni içeren `Jenkinsfile`adlı bu derleme ve dağıtım betiğini oluşturun ([burada GitHub 'da kaynak](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/doc/resources/jenkins/Jenkinsfile-webapp-se)):

   ```groovy
   node {
      stage('init') {
         checkout scm
      }
      stage('build') {
         sh '''
            mvn clean package
            cd target
            cp ../src/main/resources/web.config web.config
            cp todo-app-java-on-azure-1.0-SNAPSHOT.jar app.jar 
            zip todo.zip app.jar web.config
         '''
      }
      stage('deploy') {
         azureWebAppPublish azureCredentialsId: env.AZURE_CRED_ID,
         resourceGroup: env.RES_GROUP, appName: env.WEB_APP, filePath: "**/todo.zip"
      }
   }
   ```

1. Hem `web.config` hem de `Jenkinsfile` dosyalarını GitHub Çatalınıza işleyin ve değişikliklerinizi gönderin.

## <a name="point-pipeline-at-script"></a>Betikteki nokta işlem hattı

Şimdi Jenkins 'in kullanmasını istediğiniz derleme ve dağıtım betiğini belirtin.

1. Jenkins 'de, daha önce oluşturduğunuz işlem hattı işini seçin. 

   ![Web uygulamanız için Jenkins ardışık düzen işini seçin](media/tutorial-jenkins-deploy-web-app-azure-app-service/select-pipeline-job.png)

1. Sol taraftaki menüden **Yapılandır**' ı seçin.

1. **Ardışık düzen** sekmesinde, **tanım** listesinden, **SCM 'den işlem hattı betiği**' ni seçin.

   1. Görüntülenen **SCM** kutusunda kaynak denetiminiz olarak **Git** ' i seçin. 

   1. **Depolar** bölümünde, **Depo URL**'si için GitHub çatalınızın URL 'sini girin, örneğin: 

      `https://github.com/<your-GitHub-username>/gs-spring-boot`

   1. **Kimlik bilgileri**için önceden oluşturduğunuz GitHub kişisel erişim belirtecinizi seçin.

   1. **Betik yolu** kutusunda "Jenkinsfile" betiğinizin yolunu ekleyin.

   İşiniz bittiğinde, işlem hattı tanımınız Şu örneğe benzer şekilde görünür: 

   ![Jenkins işlem hattınızı betiğe işaret edin](media/tutorial-jenkins-deploy-web-app-azure-app-service/set-up-jenkins-github.png)

1. İşiniz bittiğinde **Kaydet**'i seçin.

Sonra, Azure App Service için uygulamanızı derleyin ve dağıtın. 

## <a name="build-and-deploy-to-azure"></a>Oluşturma ve Azure 'a dağıtma

1. Azure CLı ile, komut satırından veya Azure Cloud Shell, bir derlemeyi tamamladıktan sonra Jenkins 'in Web uygulamanızı dağıttığı [Linux üzerinde bir Azure App Service Web uygulaması](/azure/app-service/containers/app-service-linux-intro) oluşturun. Web uygulamanızın benzersiz bir adı olduğundan emin olun.

   ```azurecli-interactive
   az group create --name yourWebAppAzureResourceGroupName --location yourAzureRegion
   az appservice plan create --name yourLinuxAppServicePlanName --resource-group yourWebAppAzureResourceGroupName --is-linux
   az webapp create --name yourWebAppName --resource-group yourWebAppAzureResourceGroupName --plan yourLinuxAppServicePlanName --runtime "java|1.8|Tomcat|8.5"
   ```

   Bu Azure CLı komutları hakkında daha fazla bilgi için şu sayfalara bakın:

   * [**`az group create`**](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)

   * [**`az appservice plan create`**](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)

   * [**`az webapp create`**](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)

1. Jenkins 'de işlem hattı işinizi seçin ve **Şimdi Oluştur**' u seçin.

   Derleme tamamlandıktan sonra Jenkins, artık Azure 'da yayın URL 'sindeki canlı olan uygulamanızı dağıtır, örneğin: 

   `http://<your-Java-web-app>.azurewebsites.net`

   ![Dağıtılan uygulamanızı Azure’da görüntüleme](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-unedited.png)

## <a name="push-changes-and-redeploy"></a>Değişiklikleri gönderme ve yeniden dağıtma

1. Web tarayıcınızda, Web uygulamanızın GitHub çatalınızda şu konuma gidin:

   `complete/src/main/java/Hello/Application.java`
   
1. GitHub 'daki sağ üst köşesinden **bu dosyayı Düzenle**' yi seçin.

1. `commandLineRunner()` yöntemde bu değişikliği yapın ve değişikliği deponun `master` dalına yürütün. `master` dalındaki bu kayıt, Jenkins 'te bir derleme başlatır. 
   
   ```java
   System.out.println("Let's inspect the beans provided by Spring Boot on Azure");
   ```

1. Oluşturma tamamlandıktan sonra Jenkins, Azure 'a yeniden dağıtıyor ve şimdi güncelleştirmenizi gösteren uygulamanızı yenileyin.

   ![Dağıtılan uygulamanızı Azure’da görüntüleme](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-edited.png)

## <a name="troubleshooting-the-jenkins-plug-in"></a>Jenkins eklentisinin sorunlarını giderme

Jenkins eklentileriyle herhangi bir hatayla karşılaşırsanız, belirli bir bileşen için [Jenkins JIRA](https://issues.jenkins-ci.org/) 'de bir sorun verin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure VM’lerini derleme aracısı olarak kullanma](/azure/jenkins/jenkins-azure-vm-agents)
