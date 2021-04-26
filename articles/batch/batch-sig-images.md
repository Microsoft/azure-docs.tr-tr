---
title: Özel görüntü havuzu oluşturmak için paylaşılan görüntü galerisini kullanma
description: Özel görüntü havuzları, toplu iş yüklerinizi çalıştırmak için işlem düğümlerini yapılandırmanın etkili bir yoludur.
ms.topic: conceptual
ms.date: 03/04/2021
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: a9ff30f52ae19e3d6a7bc58ca81eabeb91d21146
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024073"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-image-pool"></a>Özel görüntü havuzu oluşturmak için paylaşılan görüntü galerisini kullanma

Sanal makine yapılandırmasını kullanarak bir Azure Batch havuzu oluşturduğunuzda, havuzdaki her bir işlem düğümü için işletim sistemini sağlayan bir VM görüntüsü belirtirsiniz. Desteklenen bir Azure Marketi görüntüsüne sahip bir sanal makine havuzu oluşturabilir veya [paylaşılan görüntü Galerisi görüntüsüyle](../virtual-machines/shared-image-galleries.md)özel bir görüntü oluşturabilirsiniz.

## <a name="benefits-of-the-shared-image-gallery"></a>Paylaşılan görüntü galerisinin avantajları

Özel görüntünüz için paylaşılan görüntü galerisini kullandığınızda, işletim sistemi türü ve yapılandırmasının yanı sıra veri disklerinin türü üzerinde denetiminiz vardır. Paylaşılan görüntünüz, tüm toplu havuz düğümlerinde kullanılabilir hale gelen uygulamaları ve başvuru verilerini ve bunların sağlandığı anda bu verileri içerebilir.

Ortamınız için gerektiğinde bir görüntünün birden fazla sürümüne de sahip olabilirsiniz. Bir sanal makine oluşturmak için görüntü sürümü kullandığınızda, sanal makine için yeni diskler oluşturmak üzere görüntü sürümü kullanılır.

Paylaşılan bir görüntü kullanmak, havuzunuzun işlem düğümlerini toplu iş yükünüzü çalıştırmak için hazırlarken zaman kazandırır. Bir Azure Market görüntüsü kullanmak ve sağlama sonrasında her işlem düğümüne yazılım yüklemek mümkündür, ancak paylaşılan bir görüntü kullanmak genellikle daha etkilidir. Ayrıca, çok sayıda VM (600 VM 'den fazla) olan havuzlar oluştururken, havuz oluşturma sırasında zaman kazanabilirsiniz. bu sayede paylaşılan görüntü için birden çok çoğaltma belirtebilirsiniz.

Senaryonuz için yapılandırılmış paylaşılan bir görüntünün kullanılması çeşitli avantajlar sağlayabilir:

- **Bölgeler genelinde aynı görüntüleri kullanın.** Farklı bölgelerde paylaşılan görüntü çoğaltmaları oluşturabilirsiniz, böylece tüm havuzlarınız aynı görüntüyü kullanır.
- **İşletim sistemini (OS) yapılandırın.** Görüntünün işletim sistemi diskinin yapılandırmasını özelleştirebilirsiniz.
- **Uygulamaları önceden yükler.** İşletim düğümleri bir başlangıç göreviyle sağlandıktan sonra uygulamaları yüklemekten daha verimli ve daha az hata, işletim sistemi diskine önceden yükleme.
- **Büyük miktarlarda veriyi bir kez kopyalayın.** Yönetilen bir görüntünün veri disklerine kopyalayarak, yönetilen paylaşılan görüntünün statik veri parçasını oluşturun. Bu yalnızca bir kez yapılmalıdır ve verileri havuzun her bir düğümü için kullanılabilir hale getirir.
- **Daha büyük boyutlarda havuzları büyütün.** Paylaşılan görüntü Galerisi ile, daha fazla paylaşılan görüntü çoğaltmalarıyla birlikte özelleştirilmiş görüntülerinize sahip daha büyük havuzlar da oluşturabilirsiniz.
- **Özel görüntü olarak yalnızca yönetilen bir görüntü kullanmaktan daha iyi performans.** Paylaşılan görüntü özel görüntü havuzu için, kararlı duruma ulaşma süresi %25 ' e kadar daha hızlı ve VM boşta kalma gecikmesi %30 ' a kadar daha kısadır.
- **Daha kolay yönetim için görüntü sürümü oluşturma ve gruplama.** Görüntü gruplandırma tanımı, görüntünün neden oluşturulduğu, ne kadar işletim sistemi olduğu ve görüntüyü kullanma hakkında bilgi içeren bilgileri içerir. Görüntülerin gruplandırılması, daha kolay görüntü yönetimine olanak tanır. Daha fazla bilgi için bkz. [görüntü tanımları](../virtual-machines/shared-image-galleries.md#image-definitions).

## <a name="prerequisites"></a>Önkoşullar

> [!NOTE]
> Azure AD 'yi kullanarak kimlik doğrulaması yapmanız gerekir. Paylaşılan anahtar kimlik doğrulaması kullanırsanız, bir kimlik doğrulama hatası alırsınız.  

- **Bir Azure Batch hesabı.** Batch hesabı oluşturmak için, [Azure Portal](quick-create-portal.md) veya [Azure CLI](quick-create-cli.md)kullanarak Batch hızlı başlangıçlara bakın.

- **Paylaşılan görüntü Galerisi görüntüsü**. Paylaşılan bir görüntü oluşturmak için, yönetilen bir görüntü kaynağınız olması veya oluşturmanız gerekir. Görüntü, sanal makinenin işletim sistemi diskinin anlık görüntülerinden ve isteğe bağlı olarak bağlı veri diskine oluşturulmalıdır.

> [!NOTE]
> Paylaşılan görüntü, Batch hesabıyla aynı abonelikte değilse, bu abonelik için [Microsoft.Batch kaynak sağlayıcısını kaydetmeniz](../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) gerekir. İki abonelik aynı Azure AD kiracısında olmalıdır.
>
> Görüntü, Batch hesabınızla aynı bölgede çoğaltmalar olduğu sürece farklı bir bölgede olabilir.

Paylaşılan görüntü Galerisi görüntüsüne sahip özel bir görüntü havuzu oluşturmak için bir Azure AD uygulaması kullanıyorsanız, bu uygulamaya paylaşılan görüntüye erişim sağlayan bir [Azure yerleşik rolü](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) verilmiş olmalıdır. Paylaşılan görüntüye gidip, **erişim denetimi (IAM)** seçeneğini belirleyerek ve uygulama için bir rol ataması ekleyerek Azure Portal bu erişime izin verebilirsiniz.

## <a name="prepare-a-shared-image"></a>Paylaşılan bir görüntü hazırlama

Azure 'da, yönetilen bir görüntüden şu kaynaktan oluşturulabilecek paylaşılan bir görüntü hazırlayabilirsiniz:

- Azure VM 'nin işletim sistemi ve veri disklerinin anlık görüntüleri
- Yönetilen disklere sahip genelleştirilmiş bir Azure VM
- Buluta yüklenen genelleştirilmiş bir şirket içi VHD

> [!NOTE]
> Batch yalnızca Genelleştirilmiş paylaşılan görüntüleri destekler; özel bir paylaşılan görüntü, havuz oluşturmak için kullanılamaz.

Aşağıdaki adımlarda, bir VM 'yi hazırlama, anlık görüntü alma ve anlık görüntüden görüntü oluşturma işlemleri gösterilmektedir.

### <a name="prepare-a-vm"></a>VM hazırlama

Görüntü için yeni bir VM oluşturuyorsanız, yönetilen görüntünüz için temel görüntü olarak Batch tarafından desteklenen ilk taraf Azure Marketi görüntüsünü kullanın. Yalnızca ilk taraf görüntüleri temel görüntü olarak kullanılabilir. Azure Batch tarafından desteklenen Azure Market görüntüsü başvurularının tam listesini almak için bkz. [düğüm Aracısı SKU 'Larını Listele](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) işlemi.

> [!NOTE]
> Temel görüntünüz için ek lisans ve satın alma koşullarına sahip bir üçüncü taraf görüntüsü kullanamazsınız. Bu market görüntüleri hakkında daha fazla bilgi için bkz. [Linux](../virtual-machines/linux/cli-ps-findimage.md#check-the-purchase-plan-information) veya [Windows](../virtual-machines/windows/cli-ps-findimage.md#view-purchase-plan-properties)VM 'leri Kılavuzu.

VM 'Ler oluştururken şu yönergeleri izleyin:

- VM 'nin yönetilen bir disk ile oluşturulduğundan emin olun. Bu, bir VM oluşturduğunuzda varsayılan depolama ayarıdır.
- Özel Betik uzantısı gibi Azure uzantılarını VM 'ye yüklemeyin. Görüntü önceden yüklenmiş bir uzantı içeriyorsa, Azure Batch havuzunu dağıttığınızda sorunlarla karşılaşabilir.
- Bağlı veri diskleri kullanılırken, bunları kullanmak için diskleri bir VM içinden bağlamanız ve biçimlendirmeniz gerekir.
- Sağladığınız temel işletim sistemi görüntüsünün varsayılan geçici sürücüyü kullandığından emin olun. Batch düğüm Aracısı Şu anda varsayılan geçici sürücüyü bekliyor.
- İşletim sistemi diskinin şifrelenmediğinden emin olun.
- VM çalışmaya başladıktan sonra RDP (Windows için) veya SSH (Linux için) aracılığıyla buna bağlanın. Gerekli yazılımları yükler veya istenen verileri kopyalayın.
- Daha hızlı havuz sağlama için, VM 'nin işletim sistemi diski için [ReadWrite disk önbelleği ayarını](../virtual-machines/premium-storage-performance.md#disk-caching) kullanın.

### <a name="create-a-vm-snapshot"></a>VM anlık görüntüsü oluşturma

Anlık görüntü, bir VHD 'nin tam ve salt okunurdur kopyasıdır. Bir sanal makinenin işletim sistemi veya veri disklerinin anlık görüntüsünü oluşturmak için Azure portal veya komut satırı araçlarını kullanabilirsiniz. Anlık görüntü oluşturma adımları ve seçenekleri için bkz. [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) veya [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) VM 'leri Kılavuzu.

### <a name="create-an-image-from-one-or-more-snapshots"></a>Bir veya daha fazla anlık görüntüden görüntü oluşturma

Bir anlık görüntüden yönetilen bir görüntü oluşturmak için [az Image Create](/cli/azure/image) komutu gibi Azure komut satırı araçlarını kullanın. Bir işletim sistemi diski anlık görüntüsü ve isteğe bağlı olarak bir veya daha fazla veri diski anlık görüntüsü belirterek görüntü oluşturun.

### <a name="create-a-shared-image-gallery"></a>Paylaşılan Görüntü Galerisi Oluşturma

Yönetilen görüntünüzü başarıyla oluşturduktan sonra, özel görüntünüzü kullanılabilir hale getirmek için paylaşılan bir görüntü Galerisi oluşturmanız gerekir. Görüntüleriniz için paylaşılan bir görüntü Galerisi oluşturmayı öğrenmek için bkz. [Azure CLI Ile paylaşılan görüntü galerisi oluşturma](../virtual-machines/shared-images-cli.md) veya [Azure Portal kullanarak paylaşılan görüntü Galerisi](../virtual-machines/linux/shared-images-portal.md)oluşturma.

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Azure CLı kullanarak paylaşılan görüntüden havuz oluşturma

Azure CLı kullanarak paylaşılan Görüntinizden bir havuz oluşturmak için `az batch pool create` komutunu kullanın. Alanda paylaşılan görüntü KIMLIĞINI belirtin `--image` . İşletim sistemi türü ve SKU 'sunun, tarafından belirtilen sürümlerle eşleştiğinden emin olun `--node-agent-sku-id`

> [!NOTE]
> Azure AD 'yi kullanarak kimlik doğrulaması yapmanız gerekir. Paylaşılan anahtar kimlik doğrulaması kullanırsanız, bir kimlik doğrulama hatası alırsınız.  

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>C kullanarak paylaşılan görüntüden havuz oluşturma #

Alternatif olarak, C# SDK kullanarak paylaşılan görüntüden bir havuz oluşturabilirsiniz.

```csharp
private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
    ...
}
```

## <a name="create-a-pool-from-a-shared-image-using-python"></a>Python kullanarak paylaşılan görüntüden havuz oluşturma

Ayrıca, Python SDK 'sını kullanarak paylaşılan görüntüden bir havuz oluşturabilirsiniz: 

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch as batch
import azure.batch.models as batchmodels
from azure.common.credentials import ServicePrincipalCredentials

# Specify Batch account and service principal account credentials
account = "{batch-account-name}"
batch_url = "{batch-account-url}"
ad_client_id = "{sp-client-id}"
ad_tenant = "{tenant-id}"
ad_secret = "{sp-secret}"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client with Azure AD authentication
creds = ServicePrincipalCredentials(
    client_id=ad_client_id,
    secret=ad_secret,
    tenant=ad_tenant,
    resource="https://batch.core.windows.net/"
)
client = batch.BatchServiceClient(creds, batch_url)

# Configure the start task for the pool
start_task = batchmodels.StartTask(
    command_line="printenv AZ_BATCH_NODE_STARTUP_DIR"
)
start_task.run_elevated = True

# Create an ImageReference which specifies the image from
# Shared Image Gallery to install on the nodes.
ir = batchmodels.ImageReference(
    virtual_machine_image_id="/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}"
)

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04"
)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(
    id=pool_id,
    vm_size=vm_size,
    target_dedicated_nodes=node_count,
    virtual_machine_configuration=vmc,
    start_task=start_task
)

# Create pool in the Batch service
client.pool.add(new_pool)
```

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>Azure portal kullanarak paylaşılan görüntüden havuz oluşturma

Azure portal paylaşılan görüntüden bir havuz oluşturmak için aşağıdaki adımları kullanın.

1. [Azure portalını](https://portal.azure.com) açın.
1. **Batch hesaplarına** gidin ve hesabınızı seçin.
1. **Havuzlar** ' ı seçin ve ardından **Ekle** ' yi seçerek yeni bir havuz oluşturun.
1. **Görüntü türü** bölümünde **paylaşılan görüntü Galerisi**' ni seçin.
1. Kalan bölümleri, yönetilen görüntünüz hakkındaki bilgilerle doldurun.
1. **Tamam**’ı seçin.

![Portalla paylaşılan görüntüden sahip bir havuz oluşturun.](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>Büyük havuzlara dikkat edilecek noktalar

Paylaşılan bir görüntü kullanarak yüzlerce veya binlerce VM veya daha fazlasını içeren bir havuz oluşturmayı planlıyorsanız, aşağıdaki kılavuzu kullanın.

- **Paylaşılan görüntü Galerisi çoğaltma numaraları.**  En fazla 300 örneği olan her havuz için en az bir çoğaltma tutmanız önerilir. Örneğin, 3000 VM 'Ler içeren bir havuz oluşturuyorsanız, resminizin en az 10 çoğaltmasını tutmanız gerekir. Daha iyi performans için en düşük gereksinimlerden her zaman daha fazla çoğaltma tutulması önerilir.

- **Yeniden boyutlandırma zaman aşımı.** Havuzunuz sabit sayıda düğüm içeriyorsa (otomatik ölçeklendirme yoksa) havuz `resizeTimeout` boyutuna bağlı olarak havuzun özelliğini arttırın. Her 1000 VM için önerilen yeniden boyutlandırma zaman aşımı en az 15 dakikadır. Örneğin, 2000 VM içeren bir havuz için önerilen yeniden boyutlandırma zaman aşımı süresi en az 30 dakikadır.

## <a name="next-steps"></a>Sonraki adımlar

- Toplu Iş hakkında ayrıntılı genel bakış için bkz. [Batch hizmeti iş akışı ve kaynaklar](batch-service-workflow-features.md).
- [Paylaşılan görüntü Galerisi](../virtual-machines/shared-image-galleries.md)hakkında bilgi edinin.