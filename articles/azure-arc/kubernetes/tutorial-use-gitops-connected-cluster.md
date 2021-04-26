---
title: 'Öğretici: Azure Arc etkin bir Kubernetes kümesinde giler kullanarak yapılandırma dağıtma'
description: Bu öğretici, Azure Arc etkin bir Kubernetes kümesinde yapılandırmaların uygulanmasını gösterir. Bu işlemi bir kavramsal alma için, bkz. konfigürasyonlar ve Gilar-Azure Arc etkin Kubernetes makalesi.
author: shashankbarsin
ms.author: shasb
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial , devx-track-azurecli
ms.openlocfilehash: 0f1172ffa0d29734e7ec005bf2812eeca215aa0d
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484175"
---
# <a name="tutorial-deploy-configurations-using-gitops-on-an-azure-arc-enabled-kubernetes-cluster"></a>Öğretici: Azure Arc etkin bir Kubernetes kümesinde giler kullanarak yapılandırma dağıtma 

Bu öğreticide, Azure Arc etkin bir Kubernetes kümesinde Gilar kullanarak yapılandırma uygulayacaksınız. Şunları öğrenirsiniz:

> [!div class="checklist"]
> * Örnek bir git deposu kullanarak Azure Arc etkin bir Kubernetes kümesinde yapılandırma oluşturma.
> * Yapılandırmanın başarıyla oluşturulduğunu doğrulayın.
> * Özel bir git deposundan yapılandırma uygulayın.
> * Kubernetes yapılandırmasını doğrulayın.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Mevcut bir Azure Arc, Kubernetes bağlı kümesini etkinleştirdi.
    - Henüz bir küme bağlanamadıysanız, [Azure Arc özellikli bir Kubernetes kümesi hızlı başlangıç](quickstart-connect-cluster.md)' i inceleyin.
- Bu özelliğin avantajları ve mimarisi hakkında bilgiler. Yapılandırmalarda daha fazla bilgi edinin [ve Azure Arc etkin Kubernetes makalesini](conceptual-configurations.md)okuyun.
- `k8s-configuration`Sürüm >= 1.0.0 Azure CLI uzantısını yükler:
  
  ```azurecli
  az extension add --name k8s-configuration
  ```

    >[!TIP]
    > `k8s-configuration`Uzantı zaten yüklüyse, aşağıdaki komutu kullanarak en son sürüme güncelleştirebilirsiniz.`az extension update --name k8s-configuration`

## <a name="create-a-configuration"></a>Yapılandırma oluşturma

Bu makalede kullanılan [örnek depo](https://github.com/Azure/arc-k8s-demo) , bir küme operatörü etrafında yapılandırılmıştır. Bu depodaki bildirimler birkaç ad alanı hazırlar, iş yüklerini dağıtır ve bazı ekibe özel yapılandırma sağlar. Bu depoyu, Gila 'lar ile birlikte kullanmak kümenizdeki aşağıdaki kaynakları oluşturur:

* Ad alanları: `cluster-config` , `team-a` , `team-b`
* Dağıtmak `cluster-config/azure-vote`
* ConfigMap: `team-a/endpoints`

`config-agent`Yeni veya güncelleştirilmiş yapılandırma Için Azure 'ı yoklar. Bu görev 30 saniyeye kadar sürer.

Özel bir depoyu yapılandırmayla ilişkilendirirken, [özel bir git deposundan yapılandırma uygulama](#apply-configuration-from-a-private-git-repository)bölümünde aşağıdaki adımları izleyin.

## <a name="use-azure-cli"></a>Azure CLI kullanma
`k8s-configuration`Bağlı bir kümeyi [örnek git deposuna](https://github.com/Azure/arc-k8s-demo)bağlamak için Azure CLI uzantısını kullanın. 
1. Bu yapılandırmayı adlandırın `cluster-config` .
1. Aracıyı, ad alanında işlecini dağıtmasını bildirin `cluster-config` .
1. İşleç izinlerini verin `cluster-admin` .

    ```azurecli
    az k8s-configuration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
    ```

    ```output
    {
      "complianceStatus": {
      "complianceState": "Pending",
      "lastConfigApplied": "0001-01-01T00:00:00",
      "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
      "messageLevel": "3"
      },
      "configurationProtectedSettings": {},
      "enableHelmOperator": false,
      "helmOperatorProperties": null,
      "id": "/subscriptions/<sub id>/resourceGroups/<group name>/providers/Microsoft.Kubernetes/connectedClusters/<cluster name>/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
      "name": "cluster-config",
      "operatorInstanceName": "cluster-config",
      "operatorNamespace": "cluster-config",
      "operatorParams": "--git-readonly",
      "operatorScope": "cluster",
      "operatorType": "Flux",
      "provisioningState": "Succeeded",
      "repositoryPublicKey": "",
      "repositoryUrl": "https://github.com/Azure/arc-k8s-demo",
      "resourceGroup": "MyRG",
      "sshKnownHostsContents": "",
      "systemData": {
        "createdAt": "2020-11-24T21:22:01.542801+00:00",
        "createdBy": null,
        "createdByType": null,
        "lastModifiedAt": "2020-11-24T21:22:01.542801+00:00",
        "lastModifiedBy": null,
        "lastModifiedByType": null
      },
      "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
      ```

### <a name="use-a-public-git-repository"></a>Ortak bir git deposu kullanma

| Parametre | Biçimlendir |
| ------------- | ------------- |
| `--repository-url` | http [s]:/sunucu/repo [. git] veya git:/sunucu/repo [. git]

### <a name="use-a-private-git-repository-with-ssh-and-flux-created-keys"></a>SSH ve Flox tarafından oluşturulan anahtarlarla özel bir git deposu kullanma

Flox tarafından oluşturulan ortak anahtarı git hizmeti sağlayıcınızdaki Kullanıcı hesabına ekleyin. Anahtar, Kullanıcı hesabı yerine depoya eklenirse, `git@` `user@` URL 'de yerine kullanın. 

Daha fazla ayrıntı için [özel git deposundan yapılandırmaya Uygula](#apply-configuration-from-a-private-git-repository) bölümüne atlayın.


| Parametre | Biçimlendir | Notlar
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[. git] veya user@server:repo [. git] | `git@` değişebilir `user@`

### <a name="use-a-private-git-repository-with-ssh-and-user-provided-keys"></a>SSH ve Kullanıcı tarafından sağlanmış anahtarlarla özel bir git deposu kullanma

Kendi özel anahtarınızı doğrudan veya bir dosyaya sağlayın. Anahtar [pek biçiminde](https://aka.ms/PEMformat) olmalı ve yeni satır (\n) ile bitmelidir. 

İlişkili ortak anahtarı git hizmeti sağlayıcınızdaki Kullanıcı hesabına ekleyin. Anahtar, Kullanıcı hesabı yerine depoya eklenirse, yerine kullanın `git@` `user@` . 

Daha fazla ayrıntı için [özel git deposundan yapılandırmaya Uygula](#apply-configuration-from-a-private-git-repository) bölümüne atlayın.  

| Parametre | Biçimlendir | Notlar |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. git] veya user@server:repo [. git] | `git@` değişebilir `user@` |
| `--ssh-private-key` | [pek biçiminde](https://aka.ms/PEMformat) Base64 kodlamalı anahtar | Anahtarı doğrudan sağlama |
| `--ssh-private-key-file` | yerel dosyanın tam yolu | Yerel dosyanın pek biçimli anahtarını içeren tam yolunu sağlayın

### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>SSH ve Kullanıcı tarafından sağlanmış bilinen konaklarla özel bir git Konağı kullanma

Flox işleci, SSH bağlantısını kurmadan önce git deposunun kimliğini doğrulamak için bilinen ana bilgisayar dosyasındaki ortak git konaklarının listesini tutar. *Yaygın* olmayan bir git deposu veya kendi git ana bilgisayarınızı kullanıyorsanız, akıcı x 'in deponuzu tanımlayabilmesi için ana bilgisayar anahtarını sağlayabilirsiniz. 

Yalnızca özel anahtarlar gibi, known_hosts içeriğinizi doğrudan veya bir dosyada sağlayabilirsiniz. Kendi içeriğinizi sağlarken, yukarıdaki SSH anahtar senaryolarından biriyle birlikte [known_hosts içerik biçimi belirtimlerini](https://aka.ms/KnownHostsFormat)kullanın.

| Parametre | Biçimlendir | Notlar |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. git] veya user@server:repo [. git] | `git@` değişebilir `user@` |
| `--ssh-known-hosts` | base64 kodlu | Bilinen ana bilgisayarlar içeriğini doğrudan sağlama |
| `--ssh-known-hosts-file` | yerel dosyanın tam yolu | Yerel bir dosyada bilinen ana bilgisayarlar içeriği sağlama |

### <a name="use-a-private-git-repository-with-https"></a>HTTPS ile özel bir git deposu kullanma

| Parametre | Biçimlendir | Notlar |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo[. git] | Temel kimlik doğrulaması ile HTTPS |
| `--https-user` | ham veya base64 kodlu | HTTPS Kullanıcı adı |
| `--https-key` | ham veya base64 kodlu | HTTPS kişisel erişim belirteci veya parolası

>[!NOTE]
>* Heln işleç grafik sürümü 1.2.0 +, HTTPS hele sürümü özel kimlik doğrulamasını destekler.
>* HTTPS helb sürümü, AKS tarafından yönetilen kümeler için desteklenmez.
>* Proxy 'niz aracılığıyla Git deposuna erişmek için Flox 'e ihtiyacınız varsa, Azure Arc aracılarını proxy ayarlarıyla güncelleştirmeniz gerekecektir. Daha fazla bilgi için bkz. [giden ara sunucu kullanarak bağlanma](./quickstart-connect-cluster.md#connect-using-an-outbound-proxy-server).


## <a name="additional-parameters"></a>Ek parametreler

Aşağıdaki isteğe bağlı parametrelerle yapılandırmayı özelleştirin:

| Parametre | Açıklama |
| ------------- | ------------- |
| `--enable-helm-operator`| Hele grafik dağıtımları desteğini etkinleştirmek için geçiş yapın. |
| `--helm-operator-params` | Hele işleci için grafik değerleri (etkinse). Örneğin, `--set helm.versions=v3`. |
| `--helm-operator-chart-version` | Hele işlecinin grafik sürümü (etkinse). 1.2.0 + sürümünü kullanın. Varsayılan: ' 1.2.0 '. |
| `--operator-namespace` | İşleç ad alanının adı. Varsayılan: ' default '. En fazla: 23 karakter. |
| `--operator-params` | İşleç parametreleri. Tek tırnak içinde verilmelidir. Örneğin, ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main'``` 

### <a name="options-supported-in----operator-params"></a>Desteklenen Seçenekler  `--operator-params` :

| Seçenek | Açıklama |
| ------------- | ------------- |
| `--git-branch`  | Kubernetes bildirimleri için kullanılacak git deposunun dalı. Varsayılan değer ' Master '. Daha yeni depoların adlı kök dalı vardır `main` ve bu durumda ayarlamanız gerekir `--git-branch=main` . |
| `--git-path`  | Kubernetes bildirimlerini bulmak için Flox için Git deposu içindeki göreli yol. |
| `--git-readonly` | Git deposu salt okunurdur. Flox buna yazmaya çalışmaz. |
| `--manifest-generation`  | Etkinleştirilirse, Flox. Flox. YAML 'yi arayacaktır ve Kustomize veya diğer bildirim oluşturucularını çalıştırır. |
| `--git-poll-interval`  | Yeni işlemeler için git deposunun yoklamak için gereken süre. Varsayılan değer `5m` (5 dakikadır). |
| `--sync-garbage-collection`  | Etkinleştirilirse, Flox oluşturduğu kaynakları silecek, ancak artık git içinde mevcut değil. |
| `--git-label`  | Eşitleme ilerlemesini izlemek için etiket. Git dalını etiketlemek için kullanılır.  `flux-sync` varsayılan değerdir. |
| `--git-user`  | Git yürütmesi için Kullanıcı adı. |
| `--git-email`  | Git yürütmesi için kullanılacak e-posta. 

Flox 'in depoya yazmasını ve `--git-user` ya da `--git-email` ayarlanmamasını istemiyorsanız, `--git-readonly` otomatik olarak ayarlanır.

Daha fazla bilgi için bkz. [Flox belgeleri](https://aka.ms/FluxcdReadme).

> [!TIP]
> Azure Arc etkin Kubernetes kaynağının **Gima** sekmesindeki Azure Portal bir yapılandırma oluşturabilirsiniz.

## <a name="validate-the-configuration"></a>Yapılandırmayı doğrulama

Yapılandırmanın başarıyla oluşturulduğunu doğrulamak için Azure CLı 'yi kullanın.

```azurecli
az k8s-configuration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Yapılandırma kaynağı uyumluluk durumu, iletiler ve hata ayıklama bilgileriyle güncelleştirilir.

```output
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2020-12-10T18:26:52.801000+00:00",
    "message": "...",
    "messageLevel": "Information"
  },
  "configurationProtectedSettings": {},
  "enableHelmOperator": false,
  "helmOperatorProperties": {
    "chartValues": "",
    "chartVersion": ""
  },
  "id": "/subscriptions/<sub id>/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git",
  "resourceGroup": "AzureArcTest",
  "sshKnownHostsContents": null,
  "systemData": {
    "createdAt": "2020-12-01T03:58:56.175674+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2020-12-10T18:30:56.881219+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
},
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

Bir yapılandırma oluşturulduğunda veya güncelleştirilirken birkaç şey meydana gelir:

1. Azure Arc, `config-agent` Yeni veya güncelleştirilmiş yapılandırmalar () için Azure Resource Manager izler `Microsoft.KubernetesConfiguration/sourceControlConfigurations` ve yeni yapılandırmayı izler `Pending` .
1. `config-agent`Yapılandırma özelliklerini okur ve hedef ad alanını oluşturur.
1. Azure Arc `controller-manager` bir Kubernetes hizmet hesabı oluşturur ve uygun izinler (veya kapsam) Için [clusterrolebinding veya rolebinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) ile eşler `cluster` `namespace` . Daha sonra bir örneğini dağıtır `flux` .
1. SSH seçeneğini akışkan x tarafından oluşturulan anahtarlarla birlikte kullanıyorsanız, `flux` BIR SSH anahtarı oluşturur ve ortak anahtarı günlüğe kaydeder.
1. `config-agent`Rapor durumu, Azure 'daki yapılandırma kaynağına geri getirilir.

Sağlama işlemi gerçekleşirken, yapılandırma kaynağı birkaç durum değişikliğine göre hareket eder. Yukarıdaki komutla ilerlemeyi izleyin `az k8s-configuration show ...` :

| Aşama değişikliği | Açıklama |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | İlk ve devam eden durumları temsil eder. |
| `complianceStatus` -> `Installed`  | `config-agent` küme başarıyla yapılandırıldı ve `flux` hata olmadan dağıtıldı. |
| `complianceStatus` -> `Failed` | `config-agent` dağıtımı bir hata ile karşılaştı `flux` . Yanıt gövdesinde Ayrıntılar sunulmaktadır `complianceStatus.message` . |

## <a name="apply-configuration-from-a-private-git-repository"></a>Özel bir git deposundan yapılandırma Uygula

Özel bir git deposu kullanıyorsanız, deponuzda SSH ortak anahtarını yapılandırmanız gerekir. Sağladığınız ya da Flox SSH ortak anahtarını oluşturuyor. Ortak anahtarı belirli git deposunda veya depoya erişimi olan git kullanıcısına yapılandırabilirsiniz. 

### <a name="get-your-own-public-key"></a>Kendi ortak anahtarınızı alın

Kendi SSH anahtarlarınızı oluşturduysanız, özel ve ortak anahtarlarınız zaten vardır.

#### <a name="get-the-public-key-using-azure-cli"></a>Azure CLı kullanarak ortak anahtarı alın 

Flox anahtarları oluşturuyorsa, Azure CLı 'de aşağıdakileri kullanın.

```console
$ az k8s-configuration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>Azure portal ortak anahtarı al

Flox anahtarları oluşturuyorsa Azure portal için aşağıdaki adımları gözden geçir.

1. Azure portal bağlı küme kaynağına gidin.
2. Kaynak sayfasında, "Gilar" ı seçin ve bu küme için yapılandırmaların listesini görüntüleyin.
3. Özel Git deposunu kullanan yapılandırmayı seçin.
4. Açılan bağlam penceresinde, pencerenin alt kısmında **Depo ortak anahtarını** kopyalayın.

#### <a name="add-public-key-using-github"></a>GitHub kullanarak ortak anahtar ekleme

Aşağıdaki seçeneklerden birini kullanın:

* Seçenek 1: Kullanıcı hesabınıza ortak anahtar ekleyin (hesabınızdaki tüm depolar için geçerlidir):  
    1. GitHub ' ı açın ve sayfanın sağ üst köşesindeki profil simgesine tıklayın.
    2. **Ayarlar**’a tıklayın.
    3. **SSH ve GPG anahtarlarına** tıklayın.
    4. **Yenı SSH anahtarı**' na tıklayın.
    5. Bir başlık girin.
    6. Ortak anahtarı çevreleyen tırnak işareti olmadan yapıştırın.
    7. **SSH anahtarı Ekle**' ye tıklayın.

* Seçenek 2: ortak anahtarı git deposuna bir dağıtım anahtarı olarak ekleyin (yalnızca bu depo için geçerlidir):  
    1. GitHub ' u açın ve deponuza gidin.
    1. **Ayarlar**’a tıklayın.
    1. **Anahtarları dağıt**' a tıklayın.
    1. **Dağıtım anahtarı Ekle**' ye tıklayın.
    1. Bir başlık girin.
    1. **Yazma erişimine Izin ver**' i işaretleyin.
    1. Ortak anahtarı çevreleyen tırnak işareti olmadan yapıştırın.
    1. **Anahtar Ekle**' ye tıklayın.

#### <a name="add-public-key-using-an-azure-devops-repository"></a>Azure DevOps deposu kullanarak ortak anahtar ekleme

Anahtarı SSH Anahtarlarınıza eklemek için aşağıdaki adımları kullanın:

1. Sağ üst taraftaki (profil görüntüsünün yanında) **Kullanıcı ayarları** altında **SSH ortak anahtarlar**' a tıklayın.
1. **+ Yeni anahtar**' ı seçin.
1. Bir ad sağlayın.
1. Ortak anahtarı çevreleyen tırnak işareti olmadan yapıştırın.
1. **Ekle**'ye tıklayın.

## <a name="validate-the-kubernetes-configuration"></a>Kubernetes yapılandırmasını doğrulama

Örneği yükledikten sonra `config-agent` `flux` , git deposunda tutulan Kaynaklar kümeye akışa başlamalıdır. Ad alanları, dağıtımlar ve kaynakların aşağıdaki komutla oluşturulup oluşturulmadıysa emin olun:

```console
kubectl get ns --show-labels
```

```output
NAME              STATUS   AGE    LABELS
azure-arc         Active   24h    <none>
cluster-config    Active   177m   <none>
default           Active   29h    <none>
itops             Active   177m   fluxcd.io/sync-gc-mark=sha256.9oYk8yEsRwWkR09n8eJCRNafckASgghAsUWgXWEQ9es,name=itops
kube-node-lease   Active   29h    <none>
kube-public       Active   29h    <none>
kube-system       Active   29h    <none>
team-a            Active   177m   fluxcd.io/sync-gc-mark=sha256.CS5boSi8kg_vyxfAeu7Das5harSy1i0gc2fodD7YDqA,name=team-a
team-b            Active   177m   fluxcd.io/sync-gc-mark=sha256.vF36thDIFnDDI2VEttBp5jgdxvEuaLmm7yT_cuA2UEw,name=team-b
```

,, `team-a` `team-b` `itops` Ve `cluster-config` ad alanlarının oluşturulduğunu görebiliriz.

`flux`İşleci, `cluster-config` yapılandırma kaynağı tarafından yönlendirilerek ad alanına dağıtıldı:

```console
kubectl -n cluster-config get deploy  -o wide
```

```output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>Keşfetmeye devam edin

Yapılandırma deposunun bir parçası olarak dağıtılan diğer kaynakları şu kullanarak keşfedebilirsiniz:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```
## <a name="clean-up-resources"></a>Kaynakları temizleme

Azure CLı veya Azure portal kullanarak bir yapılandırmayı silin. Sil komutunu çalıştırdıktan sonra yapılandırma kaynağı Azure 'da hemen silinir. İlişkili nesnelerin kümeden tam silme işlemi 10 dakika içinde gerçekleşmelidir. Yapılandırma kaldırıldığında, ilişkili nesnelerin tam silinmesi bir saate kadar sürebilir.

Kapsama sahip bir yapılandırma `namespace` silindiğinde, mevcut iş yüklerinin kesilmesini önlemek için ad alanı Azure Arc tarafından silinmez. Gerekirse, kullanarak bu ad alanını el ile silebilirsiniz `kubectl` .

```azurecli
az k8s-configuration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

> [!NOTE]
> Yapılandırma silindiğinde, izlenen git deposundan dağıtımların sonucu olan kümede yapılan değişiklikler silinmez.

## <a name="next-steps"></a>Sonraki adımlar

Gilar ile CI/CD 'yi nasıl uygulayacağınızı öğrenmek için sonraki öğreticiye ilerleyin.
> [!div class="nextstepaction"]
> [Gilar ile CI/CD 'yi uygulama](./tutorial-gitops-ci-cd.md)
