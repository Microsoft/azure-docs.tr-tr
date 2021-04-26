---
title: Azure Kubernetes Service (AKS) kümesini yükseltme
description: En son özellikleri ve güvenlik güncelleştirmelerini almak için bir Azure Kubernetes hizmeti (AKS) kümesini nasıl yükselteceğinizi öğrenin.
services: container-service
ms.topic: article
ms.date: 12/17/2020
ms.openlocfilehash: d6a5ed468541090d433dba732707a59841e6ff41
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779624"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Azure Kubernetes Service (AKS) kümesini yükseltme

AKS kümesi yaşam döngüsünün bir parçası, en son Kubernetes sürümüne düzenli güncelleştirmeler gerçekleştirmeyi içerir. En son güvenlik sürümlerini uygulamanız veya en son özellikleri almak için yükseltmeniz önemlidir. Bu makalede, bir AKS kümesindeki ana bileşenlerin veya tek bir varsayılan düğüm havuzunun nasıl yükseltileceğini gösterir.

Birden çok düğüm havuzu veya Windows Server düğümü kullanan AKS kümelerinde bkz. [aks 'de düğüm havuzunu yükseltme][nodepool-upgrade].

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, Azure CLı sürüm 2.0.65 veya üstünü çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli-install].

> [!WARNING]
> AKS kümesi yükseltmesi, düğümlerinizin bir eş ve drenajı tetikler. Kullanılabilir düşük bir işlem kotası varsa, yükseltme başarısız olabilir. Daha fazla bilgi için bkz. [kotaları artırma](../azure-portal/supportability/resource-manager-core-quotas-request.md)

## <a name="check-for-available-aks-cluster-upgrades"></a>Kullanılabilir AKS kümesi yükseltmelerini denetle

Kümeniz için hangi Kubernetes sürümlerinin kullanılabilir olduğunu denetlemek için [az aks Get-yükseltmeler][az-aks-get-upgrades] komutunu kullanın. Aşağıdaki örnek *Myresourcegroup* Içinde *Myakscluster* için kullanılabilir yükseltmeleri denetler:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Desteklenen bir AKS kümesini yükselttiğinizde, Kubernetes ikincil sürümleri atlanamaz. Tüm yükseltmelerin ana sürüm numarasına göre sıralı olarak gerçekleştirilmesi gerekir. Örneğin, *1.14. x*  ->  *1.15. x* veya *1.15. x*  ->  *1.16. x* arasındaki yükseltmelere izin verilir, ancak *1.14. x*  ->  *1.16. x* buna izin verilmez. 
> > Birden çok sürüm atlanması, yalnızca _Desteklenmeyen bir sürümden_ _desteklenen bir sürüme_ yükseltilirken yapılabilir. Örneğin, desteklenmeyen bir *1,10. x* --> desteklenen bir *1.15. x* için bir yükseltme tamamlanabilir.

Aşağıdaki örnek çıktı, kümenin *1.19.1* ve *1.19.3* sürümlerine yükseltileceğini gösterir:

```console
Name     ResourceGroup    MasterVersion    Upgrades
-------  ---------------  ---------------  --------------
default  myResourceGroup  1.18.10          1.19.1, 1.19.3
```

Kullanılabilir bir yükseltme yoksa şu iletiyi alırsınız:

```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade"></a>Düğüm aşırı gerilim yükseltmesini özelleştirme

> [!Important]
> Düğüm dalgalanmalarına, her yükseltme işlemi için istenen en fazla aşırı gerilim sayısı için abonelik kotası gerekir. Örneğin, 5 düğüm havuzu olan, her birinde 4 düğüm sayısına sahip bir kümede toplam 20 düğüm vardır. Her düğüm havuzunda en fazla %50 bir aşırı gerilim değeri varsa, yükseltmeyi tamamlaması için 10 düğümün ek işlem ve IP kotası (2 düğüm * 5 havuz) gereklidir.
>
> Azure CNı kullanıyorsanız, alt ağda kullanılabilir IP 'Lerin yanı sıra [Azure CNI 'nin IP gereksinimlerini karşılayacak](configure-azure-cni.md)şekilde doğrulayın.

Varsayılan olarak, AKS, bir ek düğüm ile aşırı gerilim yükseltmeleri yapılandırır. En fazla aşırı gerilim ayarları için bir varsayılan değeri, eski bir sürümlü düğümü değiştirmek üzere mevcut uygulamaların eşinden önce ek bir düğüm oluşturarak iş yükü kesintisini en aza indirmek için AKS 'i etkinleştirir. Yükseltme hızı ve yükseltme kesintisi arasında bir denge sağlamak için, en fazla dalgalanma değeri düğüm havuzu başına özelleştirilebilir. En fazla dalgalanma değeri arttırılarak, yükseltme işlemi daha hızlı tamamlanır, ancak en fazla aşırı gerilim için büyük bir değer ayarlamak yükseltme işlemi sırasında kesintiler oluşmasına neden olabilir. 

Örneğin, en fazla %100 bir dalgalanma değeri, olası en hızlı yükseltme işlemini (düğüm sayısı katlama) sağlar, ancak düğüm havuzundaki tüm düğümlerin aynı anda bırakılmasına neden olur. Ortamları test etmek için bu gibi daha yüksek bir değer kullanmak isteyebilirsiniz. Üretim düğüm havuzları için %33 max_surge bir ayar önerilir.

AKS, en fazla dalgalanma için hem tamsayı değerlerini hem de yüzde değerini kabul eder. "5" gibi bir tamsayı, aşırı yapılacak beş ek düğümü gösterir. "%50" değeri, havuzdaki geçerli düğüm sayısının yarısını bir dalgalanma değerini gösterir. En fazla dalgalanma yüzdesi değeri en az %1, en fazla %100 olabilir. Yüzde değeri en yakın düğüm sayısına yuvarlanır. En fazla dalgalanma değeri, yükseltme sırasında geçerli düğüm sayısından düşükse, en fazla dalgalanma değeri için geçerli düğüm sayısı kullanılır.

Yükseltme sırasında, en fazla dalgalanma değeri en az 1, düğüm havuzunuzdaki düğüm sayısına eşit bir en büyük değer olabilir. Daha büyük değerler ayarlayabilirsiniz, ancak en fazla dalgalanma için kullanılan en fazla düğüm sayısı, yükseltme sırasında havuzdaki düğüm sayısından daha yüksek olmayacaktır.

> [!Important]
> Düğüm havuzundaki en fazla aşırı gerilim ayarı kalıcıdır.  Sonraki Kubernetes yükseltmeleri veya düğüm sürümü yükseltmeleri, bu ayarı kullanır. Düğüm havuzlarınızın en fazla dalgalanma değerini istediğiniz zaman değiştirebilirsiniz. Üretim düğüm havuzları için %33 olan en fazla% ayarını öneririz.

Yeni veya var olan düğüm havuzları için en fazla dalgalanma değerlerini ayarlamak için aşağıdaki komutları kullanın.

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>AKS kümesini yükseltme

AKS kümeniz için kullanılabilir sürümlerin bir listesi ile yükseltmek için [az aks Upgrade][az-aks-upgrade] komutunu kullanın. Yükseltme işlemi sırasında AKS 'ler şunları yapar: 
- Belirtilen Kubernetes sürümünü çalıştıran kümeye yeni bir arabellek düğümü (veya [en fazla dalgalanma](#customize-node-surge-upgrade)göre yapılandırılan düğüm sayısı) ekleyin. 
- çalışan uygulamalar için kesintiyi en aza indirmek için eski düğümlerden birini [beğenin][kubernetes-drain] (en fazla dalgalanma kullanıyorsanız, belirtilen arabellek düğümleri sayısıyla aynı anda [çok sayıda düğüm olarak çalışır)][kubernetes-drain] . 
- Eski düğüm tamamen boşaltılırsa, yeni sürümü alacak şekilde yeniden görüntülenir ve aşağıdaki düğümün yükseltilmesi için arabellek düğümü olur. 
- Bu işlem, kümedeki tüm düğümler yükseltilene kadar yinelenir. 
- İşlemin sonunda, son arabellek düğümü silinecek, mevcut Aracı düğüm sayısı ve bölge bakiyesi korunacaktır.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

Kaç tane düğüme sahip olduğunuza bağlı olarak, kümeyi yükseltmek birkaç dakika sürer.

> [!IMPORTANT]
> Herhangi `PodDisruptionBudgets` bir (pdb 'leri), en az 1 Pod çoğaltmasının tek seferde taşınmasına izin verildiğinden emin olun, aksi takdirde boşalt/çıkar işlemi başarısız olur.
> Boşaltma işlemi başarısız olursa, uygulamaların çökmemesini sağlamak için yükseltme işlemi tasarım tarafından başarısız olur. Lütfen işlemin durmasının ne olduğunu (yanlış pdb 'leri, kotanın olmaması vb.) düzeltin ve işlemi yeniden deneyin.

Yükseltmenin başarılı olduğunu doğrulamak için [az aks Show][az-aks-show] komutunu kullanın:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Aşağıdaki örnek çıktıda, kümenin artık *1.18.10* çalıştığını gösterilmektedir:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.18.10              Succeeded            myakscluster-dns-379cbbb9.hcp.eastus.azmk8s.io
```

## <a name="set-auto-upgrade-channel"></a>Otomatik yükseltme kanalını ayarla

Bir kümeyi el ile yükseltmenin yanı sıra, kümenizde bir otomatik yükseltme kanalı da ayarlayabilirsiniz. Aşağıdaki yükseltme kanalları kullanılabilir:

|Kanal| Eylem | Örnek
|---|---|---|
| `none`| Otomatik yükseltmeleri devre dışı bırakır ve kümeyi geçerli Kubernetes sürümünde tutar| Değiştirilmemiş olarak bırakılırsa varsayılan ayar|
| `patch`| ikincil sürümü aynı tutarken, kullanılabilir hale geldiğinde kümeyi desteklenen son düzeltme eki sürümüne otomatik olarak yükseltin.| Örneğin, bir küme sürüm *1.17.7* çalıştırıyorsa ve *1.17.9*, *1.18.4*, *1.18.6* ve *1.19.1* sürümleri kullanılabiliyorsa, kümeniz *1.17.9* olarak yükseltilir.|
| `stable`| kümeyi, alt sürüm *n-1*' deki en son desteklenen yama sürümüne otomatik olarak yükseltin, burada *N* desteklenen en son alt sürümdür.| Örneğin, bir küme sürüm *1.17.7* çalıştırıyorsa ve *1.17.9*, *1.18.4*, *1.18.6* ve *1.19.1* sürümleri kullanılabiliyorsa, kümeniz *1.18.6* olarak yükseltilir.
| `rapid`| kümeyi desteklenen en son alt sürümde bulunan en son desteklenen yama sürümüne otomatik olarak yükseltin.| Kümenin en son desteklenen alt sürüm olduğu *n-2* alt *sürümünde yer alan* Kubernetes 'nin bir sürümünde olduğu durumlarda, küme ilk olarak *n-1* alt sürümünde desteklenen en son düzeltme eki sürümüne yükseltir. Örneğin, bir küme sürüm *1.17.7* çalıştırıyorsa ve *1.17.9*, *1.18.4*, *1.18.6* ve *1.19.1* sürümleri kullanılabiliyorsa, kümeniz önce *1.18.6* olarak yükseltilir, sonra *1.19.1* olarak yükseltilir.

> [!NOTE]
> Küme otomatik yükseltme yalnızca Kubernetes 'in GA sürümlerine güncelleştirmeler sağlar ve önizleme sürümleri için güncelleştirmeyecektir.

Bir kümeyi otomatik olarak yükseltme, bir kümeyi el ile yükseltirken aynı işlemi izler. Daha ayrıntılı bilgi için bkz. [AKS kümesini yükseltme][upgrade-cluster].

AKS kümeleri için küme otomatik yükseltmesi bir önizleme özelliğidir.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

`AutoUpgradePreview`Aşağıdaki örnekte gösterildiği gibi, [az Feature Register][az-feature-register] komutunu kullanarak özellik bayrağını kaydedin:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n AutoUpgradePreview
```

Durumun *kayıtlı* gösterilmesi birkaç dakika sürer. [Az Feature List][az-feature-list] komutunu kullanarak kayıt durumunu doğrulayın:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AutoUpgradePreview')].{Name:name,State:properties.state}"
```

Hazırlandığınızda, [az Provider Register][az-provider-register] komutunu kullanarak *Microsoft. Containerservice* kaynak sağlayıcısı kaydını yenileyin:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Bir küme oluştururken otomatik yükseltme kanalını ayarlamak için, aşağıdaki örneğe benzer şekilde *otomatik yükseltme kanalı* parametresini kullanın.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable --generate-ssh-keys
```

Mevcut kümede otomatik yükseltme kanalını ayarlamak için, aşağıdaki örneğe benzer şekilde *otomatik yükseltme kanalı* parametresini güncelleştirin.

```azurecli-interactive
az aks update --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, mevcut bir AKS kümesini nasıl yükselteceğiniz açıklanır. AKS kümelerini dağıtma ve yönetme hakkında daha fazla bilgi için bkz. öğreticiler kümesi.

> [!div class="nextstepaction"]
> [AKS öğreticileri][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-provider-register]: /cli/azure/provider#az_provider_register
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[upgrade-cluster]:  #upgrade-an-aks-cluster
