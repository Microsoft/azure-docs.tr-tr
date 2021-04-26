---
title: Özel görüntü havuzu oluşturmak için yönetilen görüntü kullanma
description: Uygulamanıza yönelik yazılım ve verilerle işlem düğümleri sağlamak için yönetilen görüntüden Batch özel görüntü havuzu oluşturun.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 9baa65c0f1c1844ea10e3d5b4f0b48924912d233
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023886"
---
# <a name="use-a-managed-image-to-create-a-custom-image-pool"></a>Özel görüntü havuzu oluşturmak için yönetilen görüntü kullanma

Batch havuzunuzun sanal makineleri (VM 'Ler) için özel bir görüntü havuzu oluşturmak üzere paylaşılan bir görüntü [Galerisi görüntüsü](batch-sig-images.md)oluşturmak için yönetilen bir görüntü kullanabilirsiniz. Yalnızca yönetilen bir görüntünün kullanılması de desteklenir, ancak yalnızca 2019-08-01 olan API sürümleri için de geçerlidir.

> [!IMPORTANT]
> Çoğu durumda, paylaşılan görüntü Galerisi 'Ni kullanarak özel görüntüler oluşturmanız gerekir. Paylaşılan görüntü Galerisi 'ni kullanarak, havuzları daha hızlı sağlayabilir, daha büyük miktarlarda VM 'Ler ölçeklendirebilir ve VM 'Ler sağlanırken güvenilirliği daha iyi bir şekilde sağlayabilirsiniz. Daha fazla bilgi edinmek için bkz. [paylaşılan görüntü galerisini kullanarak özel bir havuz oluşturma](batch-sig-images.md).

Bu konuda, yalnızca yönetilen bir görüntü kullanılarak özel bir görüntü havuzunun nasıl oluşturulduğu açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

- **Yönetilen bir görüntü kaynağı**. Özel bir görüntü kullanarak bir sanal makine havuzu oluşturmak için, Batch hesabıyla aynı Azure aboneliği ve bölgesinde bir yönetilen görüntü kaynağı oluşturmanız veya oluşturmanız gerekir. Görüntü, sanal makinenin işletim sistemi diskinin anlık görüntülerinden ve isteğe bağlı olarak bağlı veri diskine oluşturulmalıdır.
  - Oluşturduğunuz her havuz için benzersiz bir özel görüntü kullanın.
  - Batch API 'Lerini kullanarak görüntüyle bir havuz oluşturmak için, bu formdan olan görüntünün **kaynak kimliğini** belirtin `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage` .
  - Yönetilen görüntü kaynağı, havuzun kullanım ömrü için mevcut olmalıdır ve havuz silindikten sonra kaldırılabilirler.

- **Azure Active Directory (Azure AD) kimlik doğrulaması**. Batch istemcisi API 'SI, Azure AD kimlik doğrulaması kullanmalıdır. Azure AD için Azure Batch desteği, [Batch hizmeti çözümlerinin kimliğini Active Directory ile doğrulama](batch-aad-auth.md) makalesinde belirtilmiştir.

## <a name="prepare-a-managed-image"></a>Yönetilen bir görüntü hazırlama

Azure 'da, yönetilen bir görüntüyü şuradan hazırlayabilirsiniz:

- Azure VM 'nin işletim sistemi ve veri disklerinin anlık görüntüleri
- Yönetilen disklere sahip genelleştirilmiş bir Azure VM
- Buluta yüklenen genelleştirilmiş bir şirket içi VHD

Toplu Iş havuzlarını yönetilen bir görüntüyle güvenilir bir şekilde ölçeklendirmek için, yönetilen görüntünün *yalnızca* ilk yöntemi kullanarak oluşturulmasını ÖNERIRIZ: VM disklerinin anlık görüntülerini kullanma. Aşağıdaki adımlarda, bir VM 'yi hazırlama, anlık görüntü alma ve anlık görüntüden yönetilen bir görüntü oluşturma işlemlerinin nasıl yapılacağı gösterilmektedir.

### <a name="prepare-a-vm"></a>VM hazırlama

Görüntü için yeni bir VM oluşturuyorsanız, yönetilen görüntünüz için temel görüntü olarak Batch tarafından desteklenen ilk taraf Azure Marketi görüntüsünü kullanın. Yalnızca ilk taraf görüntüleri temel görüntü olarak kullanılabilir. Azure Batch tarafından desteklenen Azure Market görüntüsü başvurularının tam listesini almak için bkz. [düğüm Aracısı SKU 'Larını Listele](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) işlemi.

> [!NOTE]
> Temel görüntünüz için ek lisans ve satın alma koşullarına sahip bir üçüncü taraf görüntüsü kullanamazsınız. Bu market görüntüleri hakkında daha fazla bilgi için bkz. [Linux](../virtual-machines/linux/cli-ps-findimage.md#check-the-purchase-plan-information) veya [Windows](../virtual-machines/windows/cli-ps-findimage.md#view-purchase-plan-properties)VM 'leri Kılavuzu.

- VM 'nin yönetilen bir disk ile oluşturulduğundan emin olun. Bu, bir VM oluşturduğunuzda varsayılan depolama ayarıdır.
- Özel Betik uzantısı gibi Azure uzantılarını VM 'ye yüklemeyin. Görüntü önceden yüklenmiş bir uzantı içeriyorsa, Azure Batch havuzunu dağıttığınızda sorunlarla karşılaşabilir.
- Bağlı veri diskleri kullanılırken, bunları kullanmak için diskleri bir VM içinden bağlamanız ve biçimlendirmeniz gerekir.
- Sağladığınız temel işletim sistemi görüntüsünün varsayılan geçici sürücüyü kullandığından emin olun. Batch düğüm Aracısı Şu anda varsayılan geçici sürücüyü bekliyor.
- İşletim sistemi diskinin şifrelenmediğinden emin olun.
- VM çalışmaya başladıktan sonra RDP (Windows için) veya SSH (Linux için) aracılığıyla buna bağlanın. Gerekli yazılımları yükler veya istenen verileri kopyalayın.  

### <a name="create-a-vm-snapshot"></a>VM anlık görüntüsü oluşturma

Anlık görüntü, bir VHD 'nin tam ve salt okunurdur kopyasıdır. Bir sanal makinenin işletim sistemi veya veri disklerinin anlık görüntüsünü oluşturmak için Azure portal veya komut satırı araçlarını kullanabilirsiniz. Anlık görüntü oluşturma adımları ve seçenekleri için bkz. [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) veya [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) VM 'leri Kılavuzu.

### <a name="create-an-image-from-one-or-more-snapshots"></a>Bir veya daha fazla anlık görüntüden görüntü oluşturma

Bir anlık görüntüden yönetilen bir görüntü oluşturmak için [az Image Create](/cli/azure/image) komutu gibi Azure komut satırı araçlarını kullanın. Bir işletim sistemi disk anlık görüntüsü ve isteğe bağlı olarak bir veya daha fazla veri diski anlık görüntüsü belirterek görüntü oluşturabilirsiniz.

## <a name="create-a-pool-from-a-managed-image"></a>Yönetilen görüntüden havuz oluşturma

Yönetilen görüntünüzün kaynak KIMLIĞINI bulduktan sonra, bu görüntüden özel bir görüntü havuzu oluşturun. Aşağıdaki adımlarda, Batch hizmeti ya da Batch yönetimi kullanarak nasıl özel bir görüntü havuzu oluşturacağınız gösterilmektedir.

> [!NOTE]
> Azure AD kimlik doğrulaması için kullandığınız kimliğin görüntü kaynağı için izinlere sahip olduğundan emin olun. Bkz. [Active Directory Batch hizmeti çözümlerini kimlik doğrulama](batch-aad-auth.md).
>
> Yönetilen görüntünün kaynağı havuzun ömrü için mevcut olmalıdır. Temeldeki kaynak silinirse, havuz ölçeklenmeyebilir.

### <a name="batch-service-net-sdk"></a>Batch hizmeti .NET SDK 'Sı

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
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image definition name}");
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
```

### <a name="batch-management-rest-api"></a>Batch Yönetimi REST API’si

REST API URI'si

```http
 PUT https://management.azure.com/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Batch/batchAccounts/{account name}/pools/{pool name}?api-version=2020-03-01
```

İstek Gövdesi

```json
 {
   "properties": {
     "vmSize": "{VM size}",
     "deploymentConfiguration": {
       "virtualMachineConfiguration": {
         "imageReference": {
           "id": "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image name}"
         },
         "nodeAgentSkuId": "{Node Agent SKU ID}"
       }
     }
   }
 }
```

## <a name="considerations-for-large-pools"></a>Büyük havuzlara dikkat edilecek noktalar

Özel bir görüntü kullanarak yüzlerce VM veya daha fazlasını içeren bir havuz oluşturmayı planlıyorsanız, bir VM anlık görüntüsünden oluşturulmuş bir görüntüyü kullanmak için yukarıdaki yönergeleri izlemeniz önemlidir.

Ayrıca aşağıdaki noktalara dikkat edin:

- **Boyut limitleri** -özel bir görüntü kullandığınızda toplu işlem havuz boyutunu 2500 adanmış işlem düğümlerine veya 1000 düşük öncelikli düğümlere sınırlar.

  Birden çok havuz oluşturmak için aynı görüntüyü (veya aynı temel anlık görüntüye dayalı birden çok görüntüyü) kullanırsanız, havuzlardaki toplam işlem düğümleri önceki limitleri aşamaz. Tek bir havuzun daha fazla olması için bir görüntünün veya temel anlık görüntüsünün kullanılması önerilmez.

  Havuzu [gelen NAT havuzlarıyla](pool-endpoint-configuration.md)yapılandırırsanız sınırlar azaltılabilir.

- **Yeniden boyutlandırma zaman aşımı** -havuzunuz sabit sayıda düğüm içeriyorsa (otomatik ölçeklendirme yapmaz), havuzun resizeTimeout özelliğini 20-30 dakika gibi bir değere yükseltin. Havuzunuz, zaman aşımı süresi içinde hedef boyutuna ulaşmazsa, başka bir [yeniden boyutlandırma işlemi](/rest/api/batchservice/pool/resize)gerçekleştirin.

  300 ' den fazla işlem düğümüne sahip bir havuz planlıyorsanız, hedef boyutuna ulaşmak için havuzu birden çok kez yeniden boyutlandırmanız gerekebilir.
  
[Paylaşılan görüntü galerisini](batch-sig-images.md)kullanarak, daha fazla paylaşılan görüntü çoğaltmalarıyla birlikte özelleştirilmiş görüntülerinize sahip daha büyük havuzlar da oluşturabilirsiniz. Paylaşılan görüntüleri kullanarak havuzun kararlı duruma ulaşması için gereken süre %25 daha hızlı ve VM boşta kalma gecikmesi %30 ' a kadar daha kısadır.

## <a name="considerations-for-using-packer"></a>Packer kullanma konuları

Doğrudan Packer ile yönetilen bir görüntü kaynağı oluşturmak yalnızca kullanıcı aboneliği modu Batch hesaplarıyla yapılabilir. Batch hizmeti modu hesaplarında, önce bir VHD oluşturmanız ve ardından VHD 'yi yönetilen bir görüntü kaynağına aktarmanız gerekir. Havuz ayırma moduna (Kullanıcı aboneliği veya Batch hizmeti) bağlı olarak, yönetilen bir görüntü kaynağı oluşturma adımlarınız farklılık gösterecektir.

Yönetilen görüntüyü oluşturmak için kullanılan kaynağın, Özel görüntüye başvuran herhangi bir havuzun yaşam süreleri için mevcut olduğundan emin olun. Bunun yapılmaması, havuz ayırma hatalarına ve/veya yeniden boyutlandırmaya neden olabilir.

Görüntü veya temel alınan kaynak kaldırılırsa şuna benzer bir hata alabilirsiniz: `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed` . Bu hatayı alırsanız, temeldeki kaynağın kaldırılmadığından emin olun.

Sanal makine oluşturmak için Packer kullanma hakkında daha fazla bilgi için bkz. [Packer Ile Linux görüntüsü oluşturma](../virtual-machines/linux/build-image-with-packer.md) veya [Packer Ile Windows görüntüsü oluşturma](../virtual-machines/windows/build-image-with-packer.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Paylaşılan görüntü Galerisi](batch-sig-images.md) 'ni kullanarak özel bir havuz oluşturma hakkında bilgi edinin.
- Toplu Iş hakkında ayrıntılı genel bakış için bkz. [Batch hizmeti iş akışı ve kaynaklar](batch-service-workflow-features.md).
