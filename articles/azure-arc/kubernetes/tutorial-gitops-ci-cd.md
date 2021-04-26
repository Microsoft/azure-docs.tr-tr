---
title: 'Öğretici: Azure Arc özellikli Kubernetes kümelerini kullanarak Gile ile CI/CD uygulama'
description: Bu öğretici, Azure Arc etkinleştirilmiş Kubernetes kümeleriyle Gilar kullanarak bir CI/CD çözümü ayarlamaya yol gösterir. Bu iş akışını kavramsal alma için, Gilar kullanarak CI/CD Iş akışına, Azure Arc etkin Kubernetes makalesine bakın.
author: tcare
ms.author: tcare
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/03/2021
ms.custom: template-tutorial, devx-track-azurecli
ms.openlocfilehash: 9a228ce6f8b18afb77b656765abbad0bb4ae877f
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589151"
---
# <a name="tutorial-implement-cicd-with-gitops-using-azure-arc-enabled-kubernetes-clusters"></a>Öğretici: Azure Arc özellikli Kubernetes kümelerini kullanarak Gile ile CI/CD uygulama


Bu öğreticide, Azure Arc etkinleştirilmiş Kubernetes kümeleriyle Gilar kullanarak bir CI/CD çözümü ayarlayacaksınız. Örnek Azure oy uygulamasını kullanarak şunları yapabilirsiniz:

> [!div class="checklist"]
> * Azure Arc etkin bir Kubernetes kümesi oluşturun.
> * Uygulamanızı ve Gilar depolarını Azure Repos bağlayın.
> * CI/CD işlem hatlarını içeri aktarın.
> * Azure Container Registry (ACR) Azure DevOps ve Kubernetes 'e bağlayın.
> * Ortam değişkeni grupları oluşturun.
> * `dev`Ve ortamlarını dağıtın `stage` .
> * Uygulama ortamlarını test edin.

Bir Azure aboneliğine sahip™, başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="before-you-begin"></a>Başlamadan önce

Bu öğreticide, Azure DevOps, Azure Repos ve işlem hatları ve Azure CLı ile ilgili bir benzerlik vardır.

* [Azure DevOps Services](https://dev.azure.com/)oturum açın.
* CI/CD ortamınız için git dağıtmayı öğrenmek için [önceki öğreticiyi](./tutorial-use-gitops-connected-cluster.md) doldurun.
* Bu özelliğin [avantajlarını ve mimarisini](./conceptual-configurations.md) anlayın.
* Şunları doğrulayın:
  * **Arc-cicd-Cluster** adlı [bağlı bir Azure Arc etkin Kubernetes kümesi](./quickstart-connect-cluster.md#connect-an-existing-kubernetes-cluster) .
  * [Aks tümleştirmesi](../../aks/cluster-container-registry-integration.md) veya [aks olmayan küme kimlik doğrulaması](../../container-registry/container-registry-auth-kubernetes.md)ile bağlı bir Azure Container Registry (ACR).
  * [Azure Repos](/azure/devops/repos/get-started/what-is-repos) ve [Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started)için "derleme Yöneticisi" ve "Proje Yöneticisi" izinleri.
* Aşağıdaki Azure Arc etkin Kubernetes CLı uzantılarını yükler >= 1.0.0:

  ```azurecli
  az extension add --name connectedk8s
  az extension add --name k8sconfiguration
  ```
  * Bu uzantıları en son sürüme güncelleştirmek için aşağıdaki komutları çalıştırın:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8sconfiguration
    ```

## <a name="import-application-and-gitops-repos-into-azure-repos"></a>Uygulama ve giler depolarını Azure Repos içine aktarma

Bir [uygulama](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd#application-repo) deposunu ve bir [giler](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd#gitops-repo) deposunu Azure Repos içine aktarın. Bu öğretici için aşağıdaki örnek depoları kullanın:

* **yay-cicd-demo-src** uygulama deposu
   * 'DEKI https://github.com/Azure/arc-cicd-demo-src
   * Gilar kullanarak dağıtacağınız örnek Azure oy uygulamasını içerir.
* **yay-cicd-demo-giüstler** Gilar deposu
   * 'DEKI https://github.com/Azure/arc-cicd-demo-gitops
   * , Azure oy uygulamasını barındıran küme kaynaklarınız için bir temel olarak çalışarak.

[Git depoları içeri aktarma](/azure/devops/repos/git/import-git-repository)hakkında daha fazla bilgi edinin.

>[!NOTE]
> Uygulama ve giler depoları için iki ayrı depoyu içeri aktarıp kullanmak güvenliği ve basitliği iyileştirebilir. Uygulama ve gide depoların izinleri ve görünürlüğü ayrı ayrı ayarlanabilir.
> Örneğin, Küme Yöneticisi, uygulama kodunda kümenin istenen durumuyla ilgili değişiklikleri bulamamayabilir. Buna karşılık, bir uygulama geliştiricisinin her bir ortam için belirli parametreleri bilmeleri gerekmez; parametreler için kapsam sağlayan bir dizi test değeri yeterli olabilir.

## <a name="connect-the-gitops-repo"></a>Gilar deposunu bağlama

Uygulamanızı sürekli olarak dağıtmak için, Gilar 'ı kullanarak uygulama deposu kümenize bağlayın. **Arc-cicd-demo-Gila GIM** deponuz, uygulamanızın **Arc-cicd-Cluster** kümenizde çalışır duruma getirmek için temel kaynakları içerir.

İlk Gilar deposu, yalnızca dağıtım ortamlarına karşılık gelen **geliştirme** ve **aşama** ad alanlarını oluşturan bir [bildirim](https://github.com/Azure/arc-cicd-demo-gitops/blob/master/arc-cicd-cluster/manifests/namespaces.yml) içerir.

Oluşturduğunuz Gilar bağlantısı otomatik olarak şunları oluşturacaktır:
* Bildirim dizininde bildirimleri eşitleyin.
* Küme durumunu güncelleştirin.

CI/CD iş akışı, uygulamayı dağıtmak için bildirim dizinini ek bildirimler ile doldurur.


1. Azure Repos ' de yeni içeri aktarılmış **Arc-cicd-demo-giüstdepolarınız** için [Yeni bir Gila bağlantısı oluşturun](./tutorial-use-gitops-connected-cluster.md) .

   ```azurecli
   az k8sconfiguration create \
      --name cluster-config \
      --cluster-name arc-cicd-cluster \
      --resource-group myResourceGroup \
      --operator-instance-name cluster-config \
      --operator-namespace cluster-config \
      --repository-url https://dev.azure.com/<Your organization>/arc-cicd-demo-gitops \
      --https-user <Azure Repos username> \
      --https-key <Azure Repos PAT token> \
      --scope cluster \
      --cluster-type connectedClusters \
      --operator-params='--git-readonly --git-path=arc-cicd-cluster/manifests'
   ```

1. Flox 'in dizini *yalnızca* `arc-cicd-cluster/manifests` temel yol olarak kullandığından emin olun. Aşağıdaki işleç parametresini kullanarak yolu tanımlayın:

   `--git-path=arc-cicd-cluster/manifests`

   > [!NOTE]
   > HTTPS bağlantı dizesi kullanıyorsanız ve bağlantı sorunları yaşıyorsanız, URL 'deki Kullanıcı adı önekini attığınızdan emin olun. Örneğin, `https://alice@dev.azure.com/contoso/arc-cicd-demo-gitops` `alice@` kaldırılmalıdır. , `--https-user` Örneğin, kullanıcı belirtir `--https-user alice` .

1. Azure portal dağıtım durumunu denetleyin.
   * Başarılı olursa, `dev` kümenizde oluşturulan ve ad alanlarının her ikisini de görürsünüz `stage` .

## <a name="import-the-cicd-pipelines"></a>CI/CD işlem hatlarını içeri aktarma

Bir Gila bağlantısını eşitlemediğiniz için bildirimleri oluşturan CI/CD işlem hatlarını içeri aktarmanız gerekir.

Uygulama deposu, `.pipeline` PR 'ler, CI ve CD için kullanacağınız işlem hatlarını içeren bir klasör içerir. Örnek depoda belirtilen üç işlem hattını içeri aktarıp yeniden adlandırın:

| İşlem hattı dosya adı | Description |
| ------------- | ------------- |
| [`.pipelines/az-vote-pr-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-pr-pipeline.yaml)  | **Yay-cicd-demo-src PR** ADLı Application PR işlem hattı |
| [`.pipelines/az-vote-ci-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-ci-pipeline.yaml) | **Yay-cicd-demo-src CI** ADLı uygulama CI işlem hattı |
| [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml) | **Arc-cicd-demo-src CD** adlı uygulama CD 'si işlem hattı |



## <a name="connect-your-acr"></a>ACR 'nizi bağlama
Ardışık düzen ve kümeniz, Docker görüntülerini depolamak ve almak için ACR 'yi kullanacaktır.

### <a name="connect-acr-to-azure-devops"></a>ACR 'yi Azure DevOps 'a bağlama
CI işlemi sırasında, uygulama Kapsayıcılarınızı bir kayıt defterine dağıtırsınız. Azure hizmet bağlantısı oluşturarak başlayın:

1. Azure DevOps 'da, proje ayarları sayfasından **hizmet bağlantıları** sayfasını açın. TFS 'de, üst menü çubuğundaki **Ayarlar** simgesinden **Hizmetler** sayfasını açın.
2. **+ Yeni hizmet bağlantısı** ' nı seçin ve ihtiyacınız olan hizmet bağlantısı türünü seçin.
3. Hizmet bağlantısı için parametreleri girin. Bu öğretici için:
   * Hizmet bağlantısı **yay-demo-ACR** adını adlandırın. 
   * Kaynak grubu olarak **Myresourcegroup** öğesini seçin.
4. Tüm işlem **hatları için erişim Izni ver**' i seçin. 
   * Bu seçenek, hizmet bağlantıları için YAML ardışık düzen dosyalarını yetkilendirir. 
5. Bağlantıyı oluşturmak için **Tamam ' ı** seçin.

### <a name="connect-acr-to-kubernetes"></a>ACR 'yi Kubernetes 'e bağlama
Kubernetes kümenizi ACR 'ınızdan görüntü çekmek üzere etkinleştirin. Özel ise, kimlik doğrulaması gerekecektir.

#### <a name="connect-acr-to-existing-aks-clusters"></a>ACR 'yi mevcut AKS kümelerine bağlama

Mevcut bir ACR 'yi, aşağıdaki komutu kullanarak mevcut AKS kümeleriyle tümleştirin:

```azurecli
az aks update -n arc-cicd-cluster -g myResourceGroup --attach-acr arc-demo-acr
```

#### <a name="create-an-image-pull-secret"></a>Resim çekme parolası oluşturma

AKS ve yerel kümeleri ACR 'nize bağlamak için bir resim çekme gizli anahtarı oluşturun. Kubernetes, kayıt defterinizde kimlik doğrulaması yapmak için gereken bilgileri depolamak için görüntü çekme gizli dizilerini kullanır.

Aşağıdaki komutla bir resim çekme gizli anahtarı oluşturun `kubectl` . Hem hem de `dev` `stage` ad alanları için yineleyin.
```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```

Her pod için bir ımagepullsecret ayarlamak zorunda kalmamak için, `dev` ve ad alanlarındaki hizmet hesabına ımagepullsecret eklemeyi göz önünde bulundurun `stage` . Daha fazla bilgi için bkz. [Kubernetes öğreticisi](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account) .

## <a name="create-environment-variable-groups"></a>Ortam değişken grupları oluşturma

### <a name="app-repo-variable-group"></a>Uygulama deposu değişken grubu
**Az-oy-app-Dev** adlı [bir değişken grubu oluşturun](/azure/devops/pipelines/library/variable-groups) . Aşağıdaki değerleri ayarlayın:

| Değişken | Değer |
| -------- | ----- |
| AZ_ACR_NAME | (örneğin, ACR örneğiniz. azurearctest.azurecr.io) |
| AZURE_SUBSCRIPTION | (öğreticide daha önce bulunan **yay-demo-ACR** olması gereken Azure hizmet bağlantınız) |
| AZURE_VOTE_IMAGE_REPO | Azure oy uygulaması deposunun tamamına yönelik tam yol, örneğin azurearctest.azurecr.io/azvote |
| ENVIRONMENT_NAME | Geliştirme |
| MANIFESTS_BRANCH | `master` |
| MANIFESTS_REPO | Giüstler deponuz için git bağlantı dizesi |
| PAT | Okuma/yazma kaynağı izinleri ile [oluşturulmuş BIR Pat belirteci](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate#create-a-pat) . Daha sonra değişken grubunu oluştururken kullanmak üzere kaydedin `stage` . |
| SRC_FOLDER | `azure-vote` | 
| TARGET_CLUSTER | `arc-cicd-cluster` |
| TARGET_NAMESPACE | `dev` |

> [!IMPORTANT]
> PAT 'nizi gizli bir tür olarak işaretleyin. Uygulamalarınızda, bir [Azure Keykasasından](/azure/devops/pipelines/library/variable-groups#link-secrets-from-an-azure-key-vault)gizli dizileri bağlamayı göz önünde bulundurun.
>
### <a name="stage-environment-variable-group"></a>Aşama ortam değişken grubu

1. **Az-oy-app-Dev** değişken grubunu kopyalayın.
1. Adı **az-oy-App-Stage** olarak değiştirin.
1. Karşılık gelen değişkenler için aşağıdaki değerleri sağlayın:

| Değişken | Değer |
| -------- | ----- |
| ENVIRONMENT_NAME | Aşama |
| TARGET_NAMESPACE | `stage` |

Artık ve ortamlarına dağıtmaya hazırsınız demektir `dev` `stage` .

## <a name="deploy-the-dev-environment-for-the-first-time"></a>Geliştirme ortamını ilk kez dağıtma
Oluşturulan CI ve CD işlem hatları ile uygulamayı ilk kez dağıtmak için CI işlem hattını çalıştırın.

### <a name="ci-pipeline"></a>CI işlem hattı

İlk CI işlem hattı çalıştırıldığında, hizmet bağlantı adını okurken bir kaynak yetkilendirme hatası alabilirsiniz.
1. Erişilmekte olan değişkenin AZURE_SUBSCRIPTION olduğunu doğrulayın.
1. Kullanım yetkisini verir.
1. İşlem hattını yeniden çalıştırın.

CI işlem hattı:
* Uygulama değişikliğinin, dağıtım için tüm otomatik kalite denetimlerini geçirmelerini sağlar.
* , PR işlem hattında tamamlanmayan fazladan bir doğrulama işlemi yapar.
    * Gilar 'a özel olarak, işlem hattı CD işlem hattı tarafından dağıtılacak işleme için yapıtları de yayımlar.
* Docker görüntüsünün değiştiğini ve yeni görüntünün itildiğini doğrular.

### <a name="cd-pipeline"></a>CD işlem hattı
Başarılı CI işlem hattı çalıştırması, dağıtım işlemini gerçekleştirmek için CD işlem hattını tetikler. Her ortama artımlı olarak dağıtırsınız.

> [!TIP]
> CD işlem hattı otomatik olarak tetiklenemez:
> 1. Adın, içindeki dal tetikleyicisiyle eşleştiğinden emin olun [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml)
>    * `arc-cicd-demo-src CI` olmalıdır.
> 1. CI işlem hattını yeniden çalıştırın.

Bu depo için şablon ve bildirim değişiklikleri oluşturulduktan sonra, CD işlem hattı bir işleme oluşturur, bunu gönderir ve onay için bir çekme isteği oluşturur.
1. Görev çıkışında verilen PR bağlantısını açın `Create PR` .
1. Gite depodaki değişiklikleri doğrulayın. Şunu görmeniz gerekir:
   * Üst düzey Held şablonu değişiklikleri.
   * İstenen durumda alttaki değişiklikleri gösteren alt düzey Kubernetes bildirimleri. Flox bu bildirimleri dağıtır.
1. Her şey iyi görünüyorsa, çekme isteğini onaylayın ve doldurun.

1. Birkaç dakika sonra, Flox değişikliği seçer ve dağıtımı başlatır.
1. Kullanarak bağlantı noktasını yerel olarak iletin `kubectl` ve uygulamayı kullanarak düzgün çalıştığından emin olun:

   `kubectl port-forward -n dev svc/azure-vote-front 8080:80`

1. Konumundaki tarayıcınızda Azure oy uygulamasını görüntüleyin `http://localhost:8080/` .

1. Sık kullanılanlarınız için oy alın ve uygulamada bazı değişiklikler yapmaya hazırlanın.

## <a name="set-up-environment-approvals"></a>Ortam onaylarını ayarlama
Uygulama dağıtımı sonrasında yalnızca kodda veya şablonlarda değişiklik yapamazsınız, ancak yanlışlıkla kümeyi hatalı duruma getirebilirsiniz.

Geliştirme ortamı dağıtımdan sonra bir kesme ortaya çıkarırsa, ortam onaylarını kullanarak daha sonraki ortamlara giderek devam edin.

1. Azure DevOps projenizde korunması gereken ortama gidin.
1. Kaynak için **onaylar ve denetimler '** e gidin.
1. **Oluştur**’u seçin.
1. Onaylayanları ve isteğe bağlı bir ileti girin.
1. El ile onay denetiminin eklenmesini tamamladıktan sonra yeniden **Oluştur** ' u seçin.

Daha fazla ayrıntı için bkz. [onay ve denetimleri tanımlama](/azure/devops/pipelines/process/approvals) öğreticisi.

CD işlem hattının bir sonraki çalıştırılışında, işlem hattı, Gima PR oluşturulduktan sonra duraklatılır. Değişikliğin düzgün şekilde eşitlendiğinden emin olun ve temel işlevselliği geçirir. Değişikliğin sonraki ortama akmasını sağlamak için ardışık düzen denetimini onaylayın.

## <a name="make-an-application-change"></a>Uygulama değişikliği yapma

Bu temel şablon kümesi ve kümedeki durumu temsil eden bildirimler sayesinde, uygulamada küçük bir değişiklik yaparsınız.

1. **Yay-cicd-demo-src** deposunda, [`azure-vote/src/azure-vote-front/config_file.cfg`](https://github.com/Azure/arc-cicd-demo-src/blob/master/azure-vote/src/azure-vote-front/config_file.cfg) dosyayı düzenleyin.

2. "Kediler ve köpekler" yeterli oy içermediğinden, oy sayısını kullanmak için bunu "sekmeler vs Spaces" olarak değiştirin.

3. Değişikliği yeni bir dalda işleyin, gönderin ve bir çekme isteği oluşturun.
   * Bu, CI/CD yaşam döngüsünü başlatacak olan tipik geliştirici akışdır.

## <a name="pr-validation-pipeline"></a>PR doğrulama işlem hattı

PR işlem hattı, hatalı değişikliğe karşı ilk savunma hattınızdır. Olağan uygulama kod kalitesi denetimleri, ve statik analizler içerir. Bir Gite perspektifinden, elde edilen altyapının dağıtılması için de aynı kaliteyi güvence altına almanız gerekir.

Uygulamanın Dockerfile ve hele grafikleri, uygulamaya benzer bir şekilde kullanılabilir.

Şu kaynaktan bir aralıktaki hatalar oluştu:
* Hatalı biçimlendirilmiş YAML dosyaları,
* Uygulamanız için CPU ve bellek sınırları ayarlama gibi en iyi yöntem önerileri.

> [!NOTE]
> Gerçek bir uygulamada sürekli olarak elde edilen en iyi kapsamı almak için, gerçek bir ortamda kullanılanlarla makul şekilde benzeyen değerleri yerine getirmeniz gerekir.

İşlem hattı yürütme sırasında bulunan hatalar çalıştırmanın test sonuçları bölümünde görüntülenir. Buradan şunları yapabilirsiniz:
* Hata türlerinde yararlı istatistikleri izleyin.
* Algılanan ilk yürütmeyi bulun.
* Yığın izleme stili, hataya neden olan kod bölümlerine bağlantılar sağlar.

İşlem hattı çalıştırması tamamlandıktan sonra, uygulama kodunun ve bunu dağıtan şablonun kalitesini garanti edersiniz. Artık PR 'yi onaylayabilir ve tamamlayabilirsiniz. CI, CD işlem hattını tetiklemeden önce şablonları ve bildirimleri yeniden oluşturacak şekilde tekrar çalışır.

> [!TIP]
> Gerçek bir ortamda, çekme isteğinin kalite kontrollerinizi geçirdiğinden emin olmak için dal ilkeleri ayarlamayı unutmayın. Daha fazla bilgi için bkz. [dal Ilkelerini ayarlama](/azure/devops/repos/git/branch-policies) makalesi.

## <a name="cd-process-approvals"></a>CD işlemi onayları

Başarılı bir CI işlem hattı çalıştırması, dağıtım işlemini gerçekleştirmek için CD işlem hattını tetikler. CD ardışık düzenine ilk defa benzer şekilde, her ortama artımlı olarak dağıtırsınız. Bu kez, işlem hattı her dağıtım ortamını onaylamanıza gerek duyar.

1. Dağıtımı `dev` ortama onaylayın.
1. Bu depo için şablon ve bildirim değişiklikleri oluşturulduktan sonra, CD işlem hattı bir işleme oluşturur, bunu gönderir ve onay için bir çekme isteği oluşturur.
1. Görevde verilen PR bağlantısını açın.
1. Gite depodaki değişiklikleri doğrulayın. Şunu görmeniz gerekir:
   * Üst düzey Held şablonu değişiklikleri.
   * İstenen durumda alttaki değişiklikleri gösteren alt düzey Kubernetes bildirimleri.
1. Her şey iyi görünüyorsa, çekme isteğini onaylayın ve doldurun.
1. Dağıtımın tamamlanmasını bekleyin.
1. Temel bir duman testi olarak, uygulama sayfasına gidin ve oylama uygulamasının artık sekmeler vs alanlarını görüntülediğini doğrulayın.
   * Kullanarak bağlantı noktasını yerel olarak iletin `kubectl` ve uygulamayı kullanarak düzgün çalıştığından emin olun: `kubectl port-forward -n dev svc/azure-vote-front 8080:80`
   * Konumundaki tarayıcınızda Azure oy uygulamasını görüntüleyin http://localhost:8080/ ve oylama seçeneklerinin sekmeler ve boşluklar olarak değiştirildiğini doğrulayın. 
1. Ortamda 1-7 arasındaki adımları yineleyin `stage` .

Dağıtımınız artık tamamlanmış. Bu, CI/CD iş akışını sonlandırır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam etmeyecekecekseniz, aşağıdaki adımlarla tüm kaynakları silin:

1. Azure yay Gilar yapılandırma bağlantısını silme:
   ```azurecli
   az k8sconfiguration delete \
   --name cluster-config \
   --cluster-name arc-cicd-cluster \
   --resource-group myResourceGroup \
   --cluster-type connectedClusters
   ```

2. `dev`Ad alanını kaldır:
   * `kubectl delete namespace dev`

3. `stage`Ad alanını kaldır:
   * `kubectl delete namespace stage`

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, dağıtım aracılığıyla uygulama geliştirmede DevOps uygulayan bir tam CI/CD iş akışı ayarlamış olursunuz. Uygulamada yapılan değişiklikler el ile onaylar tarafından geçitli doğrulamayı ve dağıtımı otomatik olarak tetikler.

Azure Arc etkinleştirilmiş Kubernetes ile Gilar ve Konfigürasyonlar hakkında daha fazla bilgi edinmek için kavramsal makalemize ilerleyin.

> [!div class="nextstepaction"]
> [Gilar kullanarak CI/CD Iş akışı-Azure Arc etkin Kubernetes](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd)
