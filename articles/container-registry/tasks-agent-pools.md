---
title: Görevi çalıştırmak için adanmış havuzu kullan-görevler
description: Azure Container Registry bir görevi çalıştırmak için kayıt defterinizde adanmış bir işlem havuzu (aracı havuzu) ayarlayın.
ms.topic: article
ms.date: 10/12/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: c23d2ab866f621db27488860ab62a41765faef40
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763712"
---
# <a name="run-an-acr-task-on-a-dedicated-agent-pool"></a>Adanmış bir aracı havuzunda ACR görevi çalıştırma

[Azure Container Registry görevlerinizi][acr-tasks] ayrılmış bir işlem ortamında çalıştırmaya olanak tanımak için Azure tarafından YÖNETILEN bir VM Havuzu (*Aracı havuzu*) ayarlayın. Kayıt defterinizde bir veya daha fazla havuz yapılandırdıktan sonra, bir havuzu seçerek hizmetin varsayılan işlem ortamının yerine bir görevi çalıştırabilirsiniz.

Bir aracı havuzu şunları sağlar:

- **Sanal ağ desteği** -bir kapsayıcı kayıt defteri, Anahtar Kasası veya depolama gibi VNET 'teki kaynaklara erişim sağlayan bir Azure VNET 'e bir aracı havuzu atayın.
- **Gereken şekilde ölçeklendirin** işlem yoğunluğu yoğun görevler için bir aracı havuzundaki örnek sayısını artırın veya sıfıra ölçeklendirin. Faturalandırma, havuz ayırmayı temel alır. Ayrıntılar için bkz. [fiyatlandırma](https://azure.microsoft.com/pricing/details/container-registry/).
- **Esnek seçenekler** -görev iş yükü ihtiyaçlarınızı karşılamak için farklı [Havuz katmanları](#pool-tiers) ve ölçek seçenekleri arasından seçim yapın.
- **Azure Yönetim** -görev havuzları, Azure tarafından düzeltme ve bakım yapılır ve tek tek VM 'lerin bakımını gerektirmeden ayrılmış ayırma sağlar.

Bu özellik **Premium** kapsayıcı kayıt defteri hizmet katmanında kullanılabilir. Kayıt defteri hizmeti katmanları ve limitleri hakkında bilgi için bkz. [Azure Container Registry SKU 'lar][acr-tiers].

> [!IMPORTANT]
> Bu özellik şu anda önizleme aşamasındadır ve bazı [sınırlamalar geçerlidir](#preview-limitations). Önizlemeler, [ek kullanım koşullarını][terms-of-use] kabul etmeniz şartıyla kullanımınıza sunulur. Bu özelliğin bazı yönleri genel kullanıma açılmadan önce değişebilir.
>

## <a name="preview-limitations"></a>Önizleme sınırlamaları

- Görev aracısı havuzları Şu anda Linux düğümlerini destekliyor. Windows düğümleri Şu anda desteklenmiyor.
- Görev aracısı havuzları önizlemede şu bölgelerde kullanılabilir: Batı ABD 2, Orta Güney ABD, Doğu ABD 2, Doğu ABD, Orta ABD, Batı Avrupa, Kuzey Avrupa, Kanada Orta, USGov Arizona, USGov Texas ve USGov Virginia.
- Her kayıt defteri için, varsayılan toplam vCPU (çekirdek) kotası tüm standart aracı havuzları için 16, yalıtılmış aracı havuzları için ise 0 ' dır. Ek ayırma için bir [destek isteği][open-support-ticket] açın.
- Şu anda aracı havuzunda çalıştırılan bir görevi iptal edemezsiniz.

## <a name="prerequisites"></a>Önkoşullar

* Bu makalede Azure CLı adımlarını kullanmak için Azure CLı sürüm 2.3.1 veya üzeri gereklidir. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli]. Veya [Azure Cloud Shell](../cloud-shell/quickstart.md)içinde çalıştırın.
* Zaten bir kapsayıcı kayıt defteriniz yoksa, bir önizleme bölgesinde (Premium katman gereklidir) [bir tane oluşturun][create-reg-cli] .

## <a name="pool-tiers"></a>Havuz katmanları

Aracı havuzu katmanları, havuzda örnek başına aşağıdaki kaynakları sağlar.

|Katman    | Tür  |  CPU  |Bellek (GB)  |
|---------|---------|---------|---------|
|S1     |  Stand    | 2       |    3     |
|S2     |  Stand    | 4       |    8     |
|S3     |  Stand    | 8       |   16     |
|I6     |  ayrılır    | 64     |   216     |


## <a name="create-and-manage-a-task-agent-pool"></a>Görev Aracısı havuzu oluşturma ve yönetme

### <a name="set-default-registry-optional"></a>Varsayılan kayıt defteri ayarla (isteğe bağlı)

Aşağıdaki Azure CLı komutlarını basitleştirmek için, [az configure][az-configure] komutunu çalıştırarak varsayılan kayıt defterini ayarlayın:

```azurecli
az configure --defaults acr=<registryName>
```

Aşağıdaki örneklerde varsayılan kayıt defteri belirlediğiniz varsayılır. Aksi takdirde, `--registry <registryName>` her komutta bir parametre geçirin `az acr` .

### <a name="create-agent-pool"></a>Aracı Havuzu Oluştur

[Az ACR agentpool Create][az-acr-agentpool-create] komutunu kullanarak bir aracı havuzu oluşturun. Aşağıdaki örnek, bir S2 Pool Havuzu (4 CPU/örnek) oluşturur. Varsayılan olarak, havuz 1 örnek içerir.

```azurecli
az acr agentpool create \
    --name myagentpool \
    --tier S2
```

> [!NOTE]
> Bir aracı havuzu ve diğer havuz yönetimi işlemlerinin oluşturulması birkaç dakika sürer.

### <a name="scale-pool"></a>Ölçek havuzu

[Az ACR agentpool Update][az-acr-agentpool-update] komutuyla havuz boyutunu yukarı veya aşağı ölçeklendirin. Aşağıdaki örnek, havuzu 2 örneğe ölçeklendirir. 0 örneğe ölçeklendirebilirsiniz.

```azurecli
az acr agentpool update \
    --name myagentpool \
    --count 2
```

## <a name="create-pool-in-a-virtual-network"></a>Sanal ağ içinde havuz oluşturma

### <a name="add-firewall-rules"></a>Güvenlik duvarı kuralları ekleme

Görev Aracısı havuzlarının aşağıdaki Azure hizmetlerine erişmesi gerekir. Aşağıdaki güvenlik duvarı kuralları var olan ağ güvenlik gruplarına veya Kullanıcı tanımlı yollara eklenmelidir.

| Yön | Protokol | Kaynak         | Kaynak Bağlantı Noktası | Hedef          | Hedef bağlantı noktası | Kullanılan    |
|-----------|----------|----------------|-------------|----------------------|-----------|---------|
| Giden  | TCP      | VirtualNetwork | Herhangi biri         | AzureKeyVault        | 443       | Varsayılan |
| Giden  | TCP      | VirtualNetwork | Herhangi biri         | Depolama              | 443       | Varsayılan |
| Giden  | TCP      | VirtualNetwork | Herhangi biri         | EventHub             | 443       | Varsayılan |
| Giden  | TCP      | VirtualNetwork | Herhangi biri         | AzureActiveDirectory | 443       | Varsayılan |
| Giden  | TCP      | VirtualNetwork | Herhangi biri         | AzureMonitor         | 443       | Varsayılan |

> [!NOTE]
> Görevleriniz ortak internet 'ten ek kaynaklar gerektiriyorsa, karşılık gelen kuralları ekleyin. Örneğin, Docker Hub 'ından temel görüntüleri çeken bir Docker Build görevi çalıştırmak için ek kurallar gerekir veya bir NuGet paketini geri yükler.

### <a name="create-pool-in-vnet"></a>VNet 'te havuz oluşturma

Aşağıdaki örnek, ağ *myvnet*'in *mysubnet* alt ağında bir aracı havuzu oluşturur:

```azurecli
# Get the subnet ID
subnetId=$(az network vnet subnet show \
        --resource-grop myresourcegroup \
        --vnet-name myvnet \
        --name mysubnetname \
        --query id --output tsv)

az acr agentpool create \
    --name myagentpool \
    --tier S2 \
    --subnet-id $subnetId
```

## <a name="run-task-on-agent-pool"></a>Görevi aracı havuzunda Çalıştır

Aşağıdaki örneklerde, bir görev sıraya alınırken bir aracı havuzunun nasıl belirtilmesinin gösterilmektedir.

> [!NOTE]
> Bir ACR görevinde bir aracı havuzu kullanmak için, havuzun en az 1 örnek içerdiğinden emin olun.
>

### <a name="quick-task"></a>Hızlı görev

[Az ACR Build][az-acr-build] komutunu kullanarak aracı havuzunda hızlı bir görev sıraya alın ve `--agent-pool` parametreyi geçirin:

```azurecli
az acr build \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --file Dockerfile \
    https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
```

### <a name="automatically-triggered-task"></a>Otomatik olarak tetiklenen görev

Örneğin, aracı havuzunda [az ACR Task Create][az-acr-task-create]ile bir zamanlanmış görev oluşturun ve `--agent-pool` parametresini geçirerek.

```azurecli
az acr task create \
    --name mytask \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --schedule "0 21 * * *" \
    --file Dockerfile \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --commit-trigger-enabled false
```

Görev kurulumunu doğrulamak için [az ACR görev çalıştırmasını][az-acr-task-run]çalıştırın:

```azurecli
az acr task run \
    --name mytask
```

### <a name="query-pool-status"></a>Sorgu havuzu durumu

Aracı havuzunda Şu anda zamanlanan çalışma sayısını bulmak için [az ACR agentpool Show][az-acr-agentpool-show]' ı çalıştırın.

```azurecli
az acr agentpool show \
    --name myagentpool \
    --queue-count
```

## <a name="next-steps"></a>Sonraki adımlar

Bulutta kapsayıcı görüntüsü derlemeleri ve bakım hakkında daha fazla örnek için [ACR görevler öğretici serisini](container-registry-tutorial-quick-task.md)inceleyin.



[acr-tasks]:           container-registry-tasks-overview.md
[acr-tiers]:           container-registry-skus.md
[azure-cli]:           /cli/azure/install-azure-cli
[open-support-ticket]: https://aka.ms/acr/support/create-ticket
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[az-configure]: /cli/azure#az_configure
[az-acr-agentpool-create]: /cli/azure/acr/agentpool#az_acr_agentpool_create
[az-acr-agentpool-update]: /cli/azure/acr/agentpool#az_acr_agentpool_update
[az-acr-agentpool-show]: /cli/azure/acr/agentpool#az_acr_agentpool_show
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[create-reg-cli]: container-registry-get-started-azure-cli.md
