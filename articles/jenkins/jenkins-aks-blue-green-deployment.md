---
title: Jenkins ve mavi/yeşil dağıtım modelini kullanarak Azure Kubernetes hizmetine dağıtın
description: Jenkins ve mavi/yeşil dağıtım düzenini kullanarak Azure Kubernetes Service'e (AKS) nasıl dağıtım yapacağınızı öğrenin.
keywords: jenkins, azure, devops, kubernetes, k8s, aks, mavi yeşil dağıtım, sürekli teslim, cd
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: ae9c496cd820bf1263cac50fb676990ed65ed0ba
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158545"
---
# <a name="deploy-to-azure-kubernetes-service-aks-by-using-jenkins-and-the-bluegreen-deployment-pattern"></a>Jenkins ve mavi/yeşil dağıtım düzenini kullanarak Azure Kubernetes Service'e (AKS) dağıtım yapma

Azure Kubernetes Service (AKS), barındırılan Kubernetes ortamınızı yöneterek kapsayıcılı uygulamaları hızla ve kolayca dağıtma olanağı sunar. Kapsayıcı düzenleme konusunda uzman olmanız gerekmez. AKS ayrıca, kaynakları isteğe bağlı olarak sağlama, yükseltme ve ölçeklendirme işlemlerini yaparak sürekliliği olan işlemlerin ve bakımların yükünü ortadan kaldırır. Uygulamalarınızı çevrimdışı duruma geçirmenize gerek yoktur. AKS hakkında daha fazla bilgi için bkz. [AKS belgeleri](/azure/aks/).

Mavi/yeşil dağıtım, yeni (yeşil) sürüm dağıtıldığı sırada mevcut (mavi) sürümü canlı tutma prensibine dayanan bir Azure DevOps Sürekli Teslim düzenidir. Bu düzen tipik olarak yükselen trafik miktarlarını yeşil dağıtıma yönlendirmek için yük dengeleme kullanır. İzleme bir olay bulursa trafik hala çalışmakta olan mavi dağıtıma yeniden yönlendirilebilir. Sürekli Teslim hakkında daha fazla bilgi için bkz: [What is Continuous Delivery (Sürekli Teslim Nedir?)](/azure/devops/what-is-continuous-delivery).

Bu öğreticide, aşağıdaki görevleri nasıl gerçekleştireceğinizi öğreneceksiniz:

> [!div class="checklist"]
> * Mavi/yeşil dağıtım düzeni hakkında bilgi edinme
> * Yönetilen bir Kubernetes kümesi oluşturma
> * Bir Kubernetes kümesini yapılandırmak için örnek betik çalıştırma
> * Bir Kubernetes kümesini el ile yapılandırma
> * Bir Jenkins işi oluşturma ve çalıştırma

## <a name="prerequisites"></a>Önkoşullar
- [GitHub hesabı](https://github.com) : Örnek deposunu kopyalamak için bir GitHub hesabınız olması gerekir.
- [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) : Kubernetes kümesi oluşturmak için Azure CLI 2.0 kullanılır.
- [Chocolatey](https://chocolatey.org): kubectl yüklemek için kullanılan bir paket yöneticisi.
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/): Kubernetes kümelerine karşı komut çalıştırmak için kullanılan bir komut satırı arabirimi.
- [jq](https://stedolan.github.io/jq/download/): Basit ve komut satırı için kullanılan bir JSON işlemcisi.

## <a name="clone-the-sample-app-from-github"></a>Örnek uygulamayı GitHub'dan kopyalama

GitHub'daki Microsoft deposundan Jenkins ve mavi/yeşil düzen kullanarak AKS'nin nasıl dağıtılacağını gösteren bir örnek uygulama bulabilirsiniz. Bu bölümde, GitHub'ınızdaki deponun çatalını oluşturup uygulamayı yerel sisteminize kopyalayacaksınız.

1. GitHub deposunda [todo-app-java-on-azure](https://github.com/microsoft/todo-app-java-on-azure.git) örnek uygulamasına göz atın.

    ![Microsoft GitHub deposundaki örnek uygulamanın ekran görüntüsü](./media/jenkins-aks-blue-green-deployment/github-sample-msft.png)

1. Sayfanın sağ üst bölümünden **Çatal**'ı seçerek deponun çatalını oluşturun ve GitHub hesabınızda deponun çatalını oluşturmaya yönelik talimatları takip edin.

    ![GitHub çatal oluşturma seçeneğinin ekran görüntüsü](./media/jenkins-aks-blue-green-deployment/github-sample-msft-fork.png)

1. Deponun çatalını oluşturduktan sonra hesap adı kendi hesap adınız olarak değişir ve depo çatalının nereden oluşturulduğunu (Microsoft) belirten bir not gösterilir.

    ![GitHub hesap adı ve notunun ekran görüntüsü](./media/jenkins-aks-blue-green-deployment/github-sample-msft-forked.png)

1. **Clone or download**'u (Kopyala veya indir) seçin.

    ![GitHub'da depoyu kopyalama veya indirme seçeneğinin ekran görüntüsü](./media/jenkins-aks-blue-green-deployment/github-sample-clone.png)

1. **Clone with HTTPS** (HTTPS ile kopyala) penceresinde **kopyala** simgesini seçin.

    ![GitHub'da kopya URL'yi panoya kopyalama seçeneğinin ekran görüntüsü](./media/jenkins-aks-blue-green-deployment/github-sample-copy.png)

1. Bir terminal veya Git Bash penceresini açın.

1. Dizinleri, deponun yerel kopyasını (kopya) depolamak istediğiniz konuma değiştirin.

1. `git clone` komutunu kullanarak önceden kopyasını oluşturduğunuz URL'yi kopyalayın.

    ![Git Bash git clone komutunun ekran görüntüsü](./media/jenkins-aks-blue-green-deployment/git-clone-command.png)

1. Kopyalama işlemini başlatmak için Enter tuşuna basın.

    ![Git Bash git clone komutu sonuçlarının ekran görüntüsü](./media/jenkins-aks-blue-green-deployment/git-clone-results.png)

1. Dizinleri, uygulama kaynağının kopyasını içeren yeni oluşturulmuş dizine değiştirin.

## <a name="create-and-configure-a-managed-kubernetes-cluster"></a>Yönetilen bir Kubernetes kümesi oluşturma ve yapılandırma

Bu bölümde, aşağıdaki adımları gerçekleştireceksiniz:

- Yönetilen bir Kubernetes kümesi oluşturmak için Azure CLI 2.0 kullanma.
- Kümenin kurulum betiği kullanarak veya el ile nasıl ayarlanacağını öğrenme.
- Azure Container Registry hizmetinin bir örneğini oluşturma.

### <a name="use-the-azure-cli-20-to-create-a-managed-kubernetes-cluster"></a>Yönetilen bir Kubernetes kümesi oluşturmak için Azure CLI 2.0 kullanma
[Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) kullanarak yönetilen bir Kubernetes kümesi oluşturmak için Azure CLI 2.0.25 veya sonraki bir sürümü kullandığınızdan emin olun.

1. Azure hesabınızda oturum açın. Aşağıdaki komutu girdikten sonra oturum açma işleminin nasıl tamamlanacağını açıklayan talimatları alacaksınız. 
    
    ```bash
    az login
    ```

1. Önceki adımda `az login` komutunu çalıştırdığınızda tüm Azure aboneliklerinizin bir listesi (abonelik kimlikleri ile beraber) görüntülenir. Bu adımda, varsayılan Azure aboneliğini ayarlayacaksınız. &lt;kullanıcı-kimliğiniz> yer tutucusunu istediğiniz Azure abonelik kimliği ile değiştirin. 

    ```bash
    az account set -s <your-subscription-id>
    ```

1. Bir kaynak grubu oluşturun. &lt;kaynak-grubu-adınız> yer tutucusunu yeni kaynak grubunuzun adı ile değiştirin ve &lt;konumunuz> yer tutucusunu konum ile değiştirin. `az account list-locations` komutu tüm Azure konumları görüntüler. AKS önizlemesi sırasında tüm konumlar kullanılabilir değildir. O anda geçerli olmayan bir konum girerseniz, kullanılabilir konumları listeleyen bir hata mesajı verilir.

    ```bash
    az group create -n <your-resource-group-name> -l <your-location>
    ```

1. Kubernetes kümesi oluşturun. &lt;kaynak-grubu-adınız> yer tutucusunu önceki adımda oluşturulan kaynak grubunun adı ile değiştirin ve &lt;kubernetes-kümesi-adınız> yer tutucusunu yeni kümenizin adı ile değiştirin. (Bu işlemin tamamlanması birkaç dakika sürebilir.)

    ```bash
    az aks create -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --generate-ssh-keys --node-count 2
    ```

### <a name="set-up-the-kubernetes-cluster"></a>Kubernetes kümesini ayarlama

Mavi/yeşil dağıtımı AKS'de el ile veya önceden kopyalanan örnekte sağlanan bir kurulum betiği ile ayarlayabilirsiniz. Bu bölümde her iki yöntemi de öğreneceksiniz.

#### <a name="set-up-the-kubernetes-cluster-via-the-sample-setup-script"></a>Kubernetes kümesini örnek kurulum betiği ile ayarlama
1. Aşağıdaki yer tutucuları ortamınız için uygun değerler ile değiştirerek **deploy/aks/setup/setup.sh** dosyasını düzenleyin: 

   - **&lt;kaynak-grubu-adınız>**
   - **&lt;kubernetes-kümesi-adınız>**
   - **&lt;konumunuz>**
   - **&lt;dns-adı-sonekiniz>**

     ![Bazı yer tutucuları vurgulanmış olan bir bash setup.sh betiğinin ekran görüntüsü](./media/jenkins-aks-blue-green-deployment/edit-setup-script.png)

1. Kurulum betiğini çalıştırın.

    ```bash
    sh setup.sh
    ```

#### <a name="set-up-a-kubernetes-cluster-manually"></a>Kubernetes kümesini el ile ayarlama 
1. Kubernetes yapılandırmasını profil klasörünüze indirin.

    ```bash
    az aks get-credentials -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --admin
    ```

1. Dizini **deploy/aks/setup** dizinine değiştirin. 

1. Hizmetleri genel uç nokta ve iki test uç noktası için ayarlamak üzere aşağıdaki **kubectl** komutlarını çalıştırın.

    ```bash
    kubectl apply -f  service-green.yml
    kubectl apply -f  test-endpoint-blue.yml
    kubectl apply -f  test-endpoint-green.yml
    ```

1. Genel ve test uç noktaları için DNS adını güncelleştirin. Bir Kubernetes kümesi oluşturduğunuzda, aynı zamanda adlandırma düzeni [MC_](https://github.com/Azure/AKS/issues/3)kaynak-grubu-adınız> **&lt;kubernetes-kümesi-adınız> _&lt;konumunuz>_ olan bir &lt;ek kaynak grubu** da oluşturmuş olursunuz.

    Kaynak grubundaki genel IP'leri bulun.

    ![Kaynak grubundaki genel IP'lerin ekran görüntüsü](./media/jenkins-aks-blue-green-deployment/publicip.png)

    Hizmetlerin her biri için aşağıdaki komutu çalıştırarak dış IP adresini bulun:
    
    ```bash
    kubectl get service todoapp-service
    ```
    
    Karşılık gelen IP adresinin DNS adını aşağıdaki komut ile güncelleştirin:

    ```bash
    az network public-ip update --dns-name aks-todoapp --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    Çağrıyı `todoapp-test-blue` ve `todoapp-test-green` için yineleyin:

    ```bash
    az network public-ip update --dns-name todoapp-blue --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>

    az network public-ip update --dns-name todoapp-green --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    DNS adı aboneliğinizde benzersiz olmalıdır. Benzersiz olmasını sağlamak için `<your-dns-name-suffix>` kullanabilirsiniz.

### <a name="create-an-instance-of-container-registry"></a>Container Registry örneği oluşturma

1. Container Registry örneği oluşturmak için `az acr create` komutunu çalıştırın. Sonraki bölümde, Docker kayıt defteri URL'si olarak `login server` kullanabilirsiniz.

    ```bash
    az acr create -n <your-registry-name> -g <your-resource-group-name>
    ```

1. Container Registry kimlik bilgilerinizi göstermek için `az acr credential` komutunu çalıştırın. Sonraki bölümde gerekeceği için Docker kayıt defteri kullanıcı adını ve parolasını not alın.

    ```bash
    az acr credential show -n <your-registry-name>
    ```

## <a name="prepare-the-jenkins-server"></a>Jenkins sunucusunu hazırlama

Bu bölümde, Jenkins sunucusunu test için kullanılan bir derlemeyi çalıştırmak üzere hazırlamayı öğreneceksiniz. Ancak, kendi derlemelerinizi çalıştırmak üzere Azure'da bir aracı hızlandırmak için bir [Azure VM aracısı](https://plugins.jenkins.io/azure-vm-agents) veya [Azure Container aracısı](https://plugins.jenkins.io/azure-container-agents) kullanmanız gerekir. Daha fazla bilgi için [ana sunucuda derleme çalıştırmanın güvenlik sonuçları](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master) konulu Jenkins makalesine bakın.

1. [Azure üzerinde bir Jenkins Ana Sunucusu](https://aka.ms/jenkins-on-azure) dağıtın.

1. Sunucuya SSH aracılığıyla bağlanın ve sunucu üzerinde derlemenizi çalıştırdığınız yere derleme araçlarını yükleyin.
   
   ```bash
   sudo apt-get install git maven 
   ```
   
1. [Docker'ı yükleyin](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce). `jenkins` kullanıcısının `docker` komutlarını çalıştırmak için izni olduğundan emin olun.

1. [kubectl yükleyin](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

1. [jq'yu indirin](https://stedolan.github.io/jq/download/).

1. Aşağıdaki komutla jq'yu yükleyin:

   ```bash
   sudo apt-get install jq
   ```
   
1. Jenkins panosunda aşağıdaki adımları uygulayarak Jenkins'de eklentileri yükleyin:

    1. **Manage Jenkins > Manage Plugins > Available**'ı (Jenkins’i yönet > Eklentileri yönet > Kullanılabilir) seçin.
    1. Azure Container Service eklentisini arayın ve yükleyin.

1. Azure'da kaynakları yönetmek için kimlik bilgilerini ekleyin. Zaten eklentiniz yoksa, **Azure kimlik bilgisi** eklentisini yükleyebilirsiniz.

1. Azure Hizmet Sorumlusu kimlik bilgilerinizi **Microsoft Azure Service Principal** (Microsoft Azure Hizmet Sorumlusu) türünde ekleyin.

1. Azure Docker kayıt defteri kullanıcı adınızı ve parolanızı ("Container Registry örneği oluşturma" bölümünde alınan) **Username with password** (Parola ile kullanıcı adı) türünde ekleyin.

## <a name="edit-the-jenkinsfile"></a>Jenkinsfile'ı düzenleme

1. Kendi deponuzda `/deploy/aks/` yoluna gidin ve `Jenkinsfile` öğesini açın.

2. Dosyayı aşağıdaki gibi güncelleştirin:

    ```groovy
    def servicePrincipalId = '<your-service-principal>'
    def resourceGroup = '<your-resource-group-name>'
    def aks = '<your-kubernetes-cluster-name>'

    def cosmosResourceGroup = '<your-cosmodb-resource-group>'
    def cosmosDbName = '<your-cosmodb-name>'
    def dbName = '<your-dbname>'

    def dockerRegistry = '<your-acr-name>.azurecr.io'
    ```
    
    Container Registry kimlik bilgisini güncelleştirin:
    
    ```groovy
    def dockerCredentialId = '<your-acr-credential-id>'
    ```

## <a name="create-the-job"></a>İşi oluşturma
1. **Pipeline** (İşlem hattı) türünde yeni bir iş ekleyin.

1. **Pipeline** > **Definition** > **Pipeline script from SCM**'yi (İşlem hattı > Tanım > SCM'den işlem hattı betiği) seçin.

1. SCM depo URL'sini kendi &lt;çatal-oluşturulan-deponuz> değerinizle girin.

1. Betik yolunu `deploy/aks/Jenkinsfile` olarak girin.

## <a name="run-the-job"></a>İşi çalıştırma

1. Projenizi yerel ortamınızda başarıyla çalıştırabildiğinizi doğrulayın. Bunu yapmak için: [Yerel makinede proje çalıştırma](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/README.md#run-it).

1. Jenkins işini çalıştırın. İşi ilk kez çalıştırdığınızda Jenkins, todo uygulamasını varsayılan etkin olmayan ortam olan mavi ortama dağıtır. 

1. İşin çalıştırıldığını doğrulamak için bu URL'lere gidin:
    - Genel uç noktası: `http://aks-todoapp<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Mavi uç noktası - `http://aks-todoapp-blue<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Yeşil uç noktası - `http://aks-todoapp-green<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`

Genel ve mavi test uç noktaları aynı güncelleştirmeye sahiptir, yeşil uç noktası ise varsayılan görüntüsünü gösterir. 

Derlemeyi bir kereden fazla çalıştırırsanız, mavi ve yeşil dağıtımlar arasında geçiş yapar. Diğer bir deyişle, geçerli ortamın mavi olması durumunda iş yeşil ortama dağıtılır ve burada test edilir. Testler başarılıysa, iş uygulamanın genel uç noktasını trafiği yeşil ortama yönlendirecek şekilde güncelleştirir.

## <a name="additional-information"></a>Ek bilgiler

Sıfır kesinti süreli dağıtım hakkında daha fazla bilgi için bu [hızlı başlangıç şablonuna](https://github.com/Azure/azure-quickstart-templates/tree/master/301-jenkins-aks-zero-downtime-deployment) bakın. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticide oluşturduğunuz kaynaklara artık ihtiyacınız olmadığında kaynakları silebilirsiniz.

```bash
az group delete -y --no-wait -n <your-resource-group-name>
```

## <a name="troubleshooting"></a>Sorun giderme

Jenkins eklentileriyle ilgili hatalarla karşılaşırsanız [Jenkins JIRA](https://issues.jenkins-ci.org/) sayfasında söz konusu bileşenle ilgili sorun bildirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Jenkins ve mavi/yeşil dağıtım düzenini kullanarak AKS'ye nasıl dağıtım yapacağınızı öğrendiniz. Azure Jenkins sağlayıcısı hakkında daha fazla bilgi için Azure üzerinde Jenkins sitesine bakın.

> [!div class="nextstepaction"]
> [Azure üzerinde Jenkins](/azure/jenkins/)