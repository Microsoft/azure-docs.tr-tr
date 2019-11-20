---
title: Öğretici-Jenkins ile GitHub 'dan Azure Kubernetes Service 'e (AKS) dağıtma
description: GitHub ve sürekli dağıtım (CD) ile Azure Kubernetes Service (AKS) ile sürekli tümleştirme (CI) için Jenkins 'i ayarlama
services: container-service
ms.service: container-service
author: zr-msft
ms.author: zarhoads
ms.topic: article
ms.date: 01/09/2019
ms.openlocfilehash: e46e2c2933ee9afda860b68b10c135ac75a5d247
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263933"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-aks-with-jenkins-continuous-integration-and-deployment"></a>Öğretici: Jenkins sürekli tümleştirme ve dağıtım ile GitHub 'dan Azure Kubernetes Service 'e (AKS) dağıtma

Bu öğreticide, Jenkins 'te sürekli tümleştirme (CI) ve sürekli dağıtım (CD) ayarlayarak GitHub 'dan bir [Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes) kümesine örnek bir uygulama dağıtılır. Bu şekilde, yürütmeleri GitHub 'a ileterek Uygulamanızı güncelleştirdiğinizde, Jenkins otomatik olarak yeni bir kapsayıcı derlemesini çalıştırır, kapsayıcı görüntülerini Azure Container Registry (ACR) öğesine gönderir ve ardından uygulamanızı AKS 'de çalıştırır. 

Bu öğreticide, şu görevleri tamamlayacaksınız:

> [!div class="checklist"]
> * Bir AKS kümesine örnek bir Azure oy uygulaması dağıtın.
> * Temel bir Jenkins projesi oluşturun.
> * Jenkins için kimlik bilgilerini, ACR ile etkileşime geçmek üzere ayarlayın.
> * Otomatik derlemeler için Jenkins derleme işi ve GitHub Web kancası oluşturun.
> * AKS 'deki bir uygulamayı GitHub kod yürütmelerine göre güncelleştirmek için CI/CD işlem hattını test edin.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için şu öğeler gereklidir:

- Kubernetes, git, CI/CD ve kapsayıcı görüntülerinin temel olarak anlaşılmasına

- Aks kümesi [kimlik bilgileriyle][aks-credentials] yapılandırılan [aks kümesi][aks-quickstart] ve `kubectl`

- [ACR kayıt defteriyle kimlik doğrulaması][acr-authentication] yapacak şekilde yapılandırılmış bir [Azure Container Registry (ACR) kayıt defteri][acr-quickstart], ACR oturum açma sunucusu adı ve aks kümesi

- Azure CLı sürüm 2.0.46 veya üzeri yüklendi ve yapılandırıldı. Sürümü bulmak için @ no__t-0 ' i çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse bkz. [Azure CLI 'Yı yüklemek][install-azure-cli].

- Geliştirme sisteminizde [yüklü Docker][docker-install]

- Geliştirme sisteminizde yüklü bir GitHub hesabı, [GitHub kişisel erişim belirteci][git-access-token]ve git istemcisi

- Jenkins 'i dağıtmak için bu örnek komut dosyası yolu yerine kendi Jenkins örneğinizi sağlarsanız, Jenkins Örneğinizde [Docker yüklü ve yapılandırılmış][docker-install] ve [kubectl][kubectl-install]gerekir.

## <a name="prepare-your-app"></a>Uygulamanızı hazırlama

Bu makalede, bir veya daha fazla pods 'de barındırılan bir Web arabirimi içeren örnek bir Azure oyu uygulaması, geçici veri depolaması için de Reddir 'ın ikinci Pod barındırılması. Otomatik dağıtımlar için Jenkins ve AKS 'i tümleştirmenizden önce, Azure oy uygulamasını el ile kendi AKS kümenize hazırlayın ve dağıtın. Bu el ile dağıtım, uygulamanın bir sürümüdür ve uygulamayı eylemde görmenizi sağlar.

> [!NOTE]
> Örnek Azure oy uygulaması, Linux düğümünde çalışmak üzere zamanlanmış bir Linux Pod kullanır. Bu makalede özetlenen akış, Windows Server düğümünde zamanlanan bir Windows Server pod için de geçerlidir.

Örnek uygulama için aşağıdaki GitHub deposunu çatal- [https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis). Depo için GitHub hesabınızda çatal oluşturmak üzere sağ üst köşedeki **Fork** (Çatal Oluştur) düğmesini seçin.

Çatalınızı geliştirme sisteminize kopyalayın. Bu depoyu kopyalarken çatalınızın URL 'sini kullandığınızdan emin olun:

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Klonlanan çatalınızın dizinine geçin:

```console
cd azure-voting-app-redis
```

Örnek uygulama için gereken kapsayıcı görüntülerini oluşturmak için *Docker-Compose. YAML* dosyasını `docker-compose` ile kullanın:

```console
docker-compose up -d
```

Gerekli temel görüntüler çekilir ve uygulama kapsayıcıları oluşturulur. Ardından, oluşturulan görüntüyü görmek için [Docker görüntüleri][docker-images] komutunu kullanabilirsiniz. Üç görüntü indirilir veya oluşturulur. `azure-vote-front` görüntüsü uygulamayı içerir ve temel olarak `nginx-flask` görüntüsünü kullanır. @No__t-0 görüntüsü bir Redsıs örneği başlatmak için kullanılır:

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

*Azure-oyönitekap* görüntüsünü ACR 'ye gönderebilmeniz için önce [az ACR LIST][az-acr-list] komutuyla ACR oturum açma sunucunuzu alın. Aşağıdaki örnek, *Myresourcegroup*adlı kaynak grubundaki bir kayıt defteri için ACR oturum açma sunucusu adresini alır:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Resmi, ACR oturum açma sunucusu adı ve @no__t sürüm numarası ile etiketlemek için [Docker Tag][docker-tag] komutunu kullanın. Önceki adımda elde edilen `<acrLoginServer>` adını sağlayın:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Son olarak, *Azure-oyön* görüntüsünü ACR Kayıt defterinize gönderin. Yeniden `<acrLoginServer>` değerini kendi ACR kayıt defterinizin oturum açma sunucusu adıyla değiştirin; Örneğin, `myacrregistry.azurecr.io`:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>Örnek uygulamayı AKS 'e dağıtma

Örnek uygulamayı AKS kümenize dağıtmak için, Azure oy deposu deposunun kökündeki Kubernetes bildirim dosyasını kullanabilirsiniz. *Azure-oy-hepsi-redin. YAML* bildirim dosyasını `vi` gibi bir düzenleyiciyle açın. `microsoft` yerine ACR oturum açma sunucunuzun adını yazın. Bu değer, bildirim dosyasının **47** . satırında bulunur:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Ardından, uygulamayı AKS kümenize dağıtmak için [kubectl Apply][kubectl-apply] komutunu kullanın:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Bir Kubernetes Yük Dengeleyici Hizmeti, uygulamayı internet 'te kullanıma sunmak için oluşturulur. Bu işlem birkaç dakika sürebilir. Yük dengeleyici dağıtımının ilerlemesini izlemek için, [kubectl Get Service][kubectl-get] komutunu `--watch` bağımsız değişkeniyle birlikte kullanın. *EXTERNAL-IP* adresi *pending* durumundan *IP address* değerine değiştiğinde kubectl izleme işlemini durdurmak için `Control + C` komutunu kullanın.

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

Uygulamayı eylemde görmek için, hizmetinizin dış IP adresine bir Web tarayıcısı açın. Aşağıdaki örnekte gösterildiği gibi Azure oy uygulaması görüntülenir:

![AKS 'de çalışan Azure örnek oy uygulaması](media/aks-jenkins/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>Jenkins 'i bir Azure VM 'ye dağıtma

Jenkins 'i Bu makalede kullanmak üzere hızlı bir şekilde dağıtmak için aşağıdaki betiği kullanarak bir Azure sanal makinesini dağıtabilir, ağ erişimini yapılandırabilir ve Jenkins 'in temel bir yüklemesini tamamlayabilirsiniz. Jenkins ve AKS kümesi arasında kimlik doğrulaması için, betik Kubernetes yapılandırma dosyanızı geliştirme sisteminizden Jenkins sistemine kopyalar.

> [!WARNING]
> Bu örnek betik, Azure VM 'de çalışan bir Jenkins ortamını hızlıca sağlamak için tanıtım amaçlı amaçlıdır. Bir VM yapılandırmak için Azure Özel Betik uzantısı 'nı kullanır ve ardından gerekli kimlik bilgilerini görüntüler. *~/5kube/config* , Jenkins sanal makinesine kopyalanır.

Betiği indirmek ve çalıştırmak için aşağıdaki komutları çalıştırın. Çalıştırmadan önce herhangi bir betiğin içeriğini gözden geçirmeniz gerekir- [https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh).

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

VM 'nin oluşturulması ve Docker ve Jenkins için gerekli bileşenlerin dağıtılması birkaç dakika sürer. Betik tamamlandığında, aşağıdaki örnek çıktıda gösterildiği gibi, Jenkins sunucusu için bir adres ve panonun kilidini açmak için bir anahtar verir:

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

Görüntülenecek URL 'ye bir Web tarayıcısı açın ve kilit açma anahtarını girin. Jenkins yapılandırmasını gerçekleştirmek için ekrandaki istemleri izleyin:

- **Önerilen eklentileri yüklemeyi** seçin
- İlk yönetici kullanıcıyı oluşturun. *Azureuser*gibi bir Kullanıcı adı girin ve sonra kendi güvenli parolanızı sağlayın. Son olarak, bir tam ad ve e-posta adresi yazın.
- **Kaydet ve Bitir**’i seçin
- Jenkins hazır olduktan sonra **Jenkins kullanmaya başla**’yı seçin
    - Jenkins kullanmaya başladığınızda web tarayıcınız boş bir sayfa görüntülerse, Jenkins hizmetini yeniden başlatın. Hizmeti yeniden başlatmak için Jenkins örneğinizin genel IP adresine SSH ve `sudo service jenkins restart` yazın. Hizmet yeniden başlatıldıktan sonra, Web tarayıcınızı yenileyin.
- Jenkins 'de, yüklemek için oluşturduğunuz Kullanıcı adı ve parolayla oturum açın.

## <a name="create-a-jenkins-environment-variable"></a>Jenkins ortam değişkeni oluşturma

Bir Jenkins ortam değişkeni, ACR oturum açma sunucusu adını tutmak için kullanılır. Bu değişkene Jenkins derleme işi sırasında başvurulur. Bu ortam değişkenini oluşturmak için aşağıdaki adımları izleyin:

- Jenkins portalının sol tarafındaki **Jenkins 'ı yönet** > **sistemi Yapılandır** ' ı seçin.
- **Genel Özellikler**altında **ortam değişkenleri**' ni seçin. Adı `ACR_LOGINSERVER` ve ACR oturum açma sunucunuzun değeri olan bir değişken ekleyin.

    ![Jenkins ortam değişkenleri](media/aks-jenkins/env-variables.png)

- Tamamlandığında, Jenkins yapılandırma sayfasının alt kısmındaki **Kaydet** ' e tıklayın.

## <a name="create-a-jenkins-credential-for-acr"></a>ACR için Jenkins kimlik bilgileri oluşturma

Jenkins 'in güncelleştirilmiş kapsayıcı görüntülerini oluşturup daha sonra da ACR 'ye gönderebilsin, ACR için kimlik bilgilerini belirtmeniz gerekir. Bu kimlik doğrulaması, hizmet sorumlularını Azure Active Directory kullanabilir. Ön koşullar ' da, AKS kümeniz için hizmet sorumlusu 'nı ACR kayıt defteriniz için *okuyucu* izinleriyle yapılandırdınız. Bu izinler, AKS kümesinin, ACR kayıt defterinden görüntü *çekmesine* olanak tanır. CI/CD işlemi sırasında Jenkins, uygulama güncelleştirmelerine göre yeni kapsayıcı görüntüleri oluşturur ve bu görüntüleri daha sonra ACR kayıt defterine *itmesi* gerekir. Rol ve izinlerin ayrılması için, şimdi de ACR Kayıt defterinize *katkıda bulunan* Izinleri olan Jenkins için bir hizmet sorumlusu yapılandırın.

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>ACR 'yi kullanmak için Jenkins için bir hizmet sorumlusu oluşturma

İlk olarak, [az ad SP Create-for-RBAC][az-ad-sp-create-for-rbac] komutunu kullanarak bir hizmet sorumlusu oluşturun:

```azurecli
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "626dd8ea-042d-4043-a8df-4ef56273670f",
  "displayName": "azure-cli-2018-09-28-22-19-34",
  "name": "http://azure-cli-2018-09-28-22-19-34",
  "password": "1ceb4df3-c567-4fb6-955e-f95ac9460297",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Çıktıda gösterilen *AppID* ve *parolayı* bir yere unutmayın. Bu değerler, Jenkins 'te kimlik bilgisi kaynağını yapılandırmak için aşağıdaki adımlarda kullanılır.

[Az ACR Show][az-acr-show] komutunu kullanarak ACR kayıt DEFTERINIZIN kaynak kimliğini alın ve değişken olarak depolayın. Kaynak grubu adınızı ve ACR adınızı belirtin:

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

Şimdi ACR kayıt defterine hizmet sorumlusu *katkıda bulunan* haklarını atamak için bir rol ataması oluşturun. Aşağıdaki örnekte, hizmet sorumlusunu oluşturmak için önceki bir komutun çıktısında gösterilen kendi *uygulama kimliği* ' ni sağlayın:

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>ACR hizmet sorumlusu için Jenkins 'te bir kimlik bilgisi kaynağı oluşturma

Azure 'da rol atama ile, artık ACR kimlik bilgilerinizi bir Jenkins kimlik bilgileri nesnesinde depolayın. Bu kimlik bilgilerine Jenkins derleme işi sırasında başvurulur.

Jenkins portalının sol tarafında, **kimlik bilgileri** > **jenkins** > **genel kimlik bilgileri (Kısıtlamasız)**  > **kimlik bilgileri ekle** ' ye tıklayın

Kimlik bilgisi türünün **parola Ile Kullanıcı adı** olduğundan emin olun ve aşağıdaki öğeleri girin:

- **Kullanıcı adı** -ACR kayıt defterinizde kimlik doğrulaması için oluşturulan hizmet sorumlusunun *uygulama kimliği* .
- **Parola** -ACR kayıt defterinizde kimlik doğrulaması için oluşturulan hizmet sorumlusu *parolası* .
- **Kimlik-** *ACR-kimlik bilgileri* gibi kimlik bilgisi tanımlayıcısı

Bu tamamlandığında, kimlik bilgileri formu aşağıdaki örneğe benzer şekilde görünür:

![Hizmet sorumlusu bilgileriyle bir Jenkins kimlik bilgisi nesnesi oluşturma](media/aks-jenkins/acr-credentials.png)

**Tamam** ' a tıklayın ve Jenkins portalına geri dönün.

## <a name="create-a-jenkins-project"></a>Jenkins projesi oluşturma

Jenkins portalınızın ana sayfasında, sol taraftaki **Yeni öğe** ' yi seçin:

1. *Azure-oy* ' i iş adı olarak girin. **Freestyle projesi**seçin, sonra **Tamam** ' ı seçin.
1. **Genel** bölümünde, **GitHub projesi** ' ni seçin ve *https: \//GitHub. com/\<' i-GitHub-Account @ no__t-5/Azure-oylama-App-redsıs* gibi, çatallanmış depo URL 'nizi girin.
1. **Kaynak kodu yönetimi** bölümünde **Git**' i seçin. *https: \//GitHub. com/\<sizin-GitHub-Account @ no__t-6/Azure-Voting-App-Redis. git* ' i seçin *.* git URL 'sini girin.

1. **Derleme tetikleyicileri** bölümünde, **Gıtscm yoklaması yoklaması için GitHub kanca tetikleyicisi** ' ni seçin.
1. **Yapı ortamı**altında **Gizli metinleri veya dosyaları kullan** ' ı seçin.
1. **Bağlamalar**altında, @no__t **Ekle**-2**Kullanıcı adı ve parola (ayrılmış)** seçeneğini belirleyin
   - **Kullanıcı adı değişkeni**için `ACR_ID` ve **parola değişkeni** için-2 @no__t girin

     ![Jenkins bağlamaları](media/aks-jenkins/bindings.png)

1. **Execute Shell** türünde bir **yapı adımı** eklemeyi seçin ve aşağıdaki metni kullanın. Bu betik yeni bir kapsayıcı görüntüsü oluşturur ve ACR Kayıt defterinize gönderir.

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. **Execute Shell** türünde başka bir **yapı adımı** ekleyin ve aşağıdaki metni kullanın. Bu betik, AKS 'teki uygulama dağıtımını ACR 'den yeni kapsayıcı görüntüsü ile güncelleştirir.

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. Tamamlandıktan sonra **Kaydet**' e tıklayın.

## <a name="test-the-jenkins-build"></a>Jenkins derlemesini test etme

GitHub yürütmelerine göre işi otomatikleştirmeye başlamadan önce, ilk olarak Jenkins derlemesini el ile test edin. Bu el ile derleme, işin doğru şekilde yapılandırıldığını, doğru Kubernetes kimlik doğrulama dosyasının hazır olduğunu ve ACR ile kimlik doğrulamanın çalışıp çalışmadığını doğrular.

Projenin sol menüsünde **Şimdi Oluştur**' u seçin.

![Jenkins test derlemesi](media/aks-jenkins/test-build.png)

Docker görüntüsü katmanları Jenkins sunucusuna çekildiğinden ilk derleme bir dakika veya iki kez sürer. Sonraki derlemeler, derleme sürelerini geliştirmek için önbelleğe alınmış görüntü katmanlarını kullanabilir.

Oluşturma işlemi sırasında, GitHub deposu Jenkins derleme sunucusuna kopyalanır. Yeni bir kapsayıcı görüntüsü oluşturulur ve ACR kayıt defterine gönderilir. Son olarak, AKS kümesinde çalışan Azure oy uygulaması yeni görüntüyü kullanacak şekilde güncelleştirilir. Uygulama kodunda hiçbir değişiklik yapılmadığından, örnek uygulamayı bir Web tarayıcısında görüntülediğinizde uygulama değiştirilmez.

Yapı işi tamamlandıktan sonra yapı geçmişi altında **derleme #1** ' ne tıklayın. **Konsol çıkışı** ' nı seçin ve derleme işlemindeki çıktıyı görüntüleyin. Son satır başarılı bir derlemeyi göstermelidir.

## <a name="create-a-github-webhook"></a>GitHub Web kancası oluşturma

Başarılı bir el ile derleme tamamlandı, Şimdi GitHub 'ı Jenkins derlemesi ile tümleştirin. GitHub 'da kod yürütmesi her yapıldığında Jenkins derleme işini çalıştırmak için Web kancası kullanılabilir. GitHub Web kancasını oluşturmak için aşağıdaki adımları izleyin:

1. Bir Web tarayıcısında, çatallanmış GitHub deponuza gidin.
1. **Ayarlar**' ı seçin ve ardından sol taraftaki **Web kancaları** ' nı seçin.
1. **Web kancası eklemeyi**seçin. *Yük URL 'si*için `http://<publicIp:8080>/github-webhook/` girin; burada `<publicIp>`, Jenkins sunucusunun IP adresidir. Sondaki/dahil ettiğinizden emin olun. İçerik türü için diğer varsayılan değerleri bırakın ve *anında iletme* olayları tetikleyin.
1. **Web kancası Ekle**' yi seçin.

    ![Jenkins için GitHub Web kancası oluşturma](media/aks-jenkins/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>Tüm CI/CD işlem hattını test etme

Artık CI/CD işlem hattının tamamını test edebilirsiniz. GitHub 'a kod yürütmeyi gönderdiğinizde, aşağıdaki adımlar gerçekleşir:

1. GitHub Web kancası Jenkins 'e ulaşır.
1. Jenkins, derleme işini başlatır ve en son kod yürütmeyi GitHub 'dan çeker.
1. Bir Docker derlemesi, güncelleştirilmiş kod kullanılarak başlatılır ve yeni kapsayıcı görüntüsü en son yapı numarasıyla etiketlenir.
1. Bu yeni kapsayıcı görüntüsü Azure Container Registry gönderilir.
1. Uygulamanız Azure Kubernetes hizmet güncelleştirmelerine dağıtıldı Azure Container Registry kayıt defterinden en son kapsayıcı görüntüsü ile.

Geliştirme makinenizde, kopyalanmış uygulamayı bir kod düzenleyicisiyle açın. */Azure-Vote/Azure-Vote* dizininde, **config_file. cfg**adlı dosyayı açın. Aşağıdaki örnekte gösterildiği gibi, bu dosyadaki oy değerlerini kediler ve köpekler dışında bir şeye güncelleştirin:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Güncelleştirilirken dosyayı kaydedin, değişiklikleri işleyin ve GitHub deposunun Çatalınıza gönderin. GitHub Web kancası Jenkins içinde yeni bir yapı işi tetikler. Jenkins Web panosunda, yapı sürecini izleyin. En son kodu çekmek, güncelleştirilmiş görüntüyü oluşturmak ve göndermek ve güncelleştirilmiş uygulamayı AKS 'te dağıtmak birkaç saniye sürer.

Oluşturma işlemi tamamlandıktan sonra, örnek Azure oyu uygulamasının Web tarayıcınızı yenileyin. Aşağıdaki örnekte gösterildiği gibi değişiklikleriniz görüntülenir:

![Jenkins derleme işi tarafından güncelleştirilmiş bir AKS 'de örnek Azure oyları](media/aks-jenkins/azure-vote-updated.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Jenkins 'in bir CI/CD çözümünün parçası olarak nasıl kullanılacağını öğrendiniz. AKS 'ler, [Azure DevOps projesi][azure-devops] gıbı diğer CI/CD çözümleri ve otomasyon araçlarıyla tümleştirilebilir ve [anyalabilen bir aks kümesi][aks-ansible]oluşturabilir.

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[git-access-token]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[docker-install]: https://docs.docker.com/install/
[kubectl-install]: https://kubernetes.io/docs/tasks/tools/install-kubectl/

<!-- LINKS - internal -->
[az-acr-list]: /cli/azure/acr#az-acr-list
[acr-authentication]: cluster-container-registry-integration.md
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[azure-devops]: ../devops-project/azure-devops-project-aks.md
[aks-ansible]: ../ansible/ansible-create-configure-aks.md
