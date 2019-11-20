---
title: Azure Batch havuzları için Azure dosya paylaşma | Microsoft Docs
description: Azure Batch içindeki bir Linux veya Windows havuzundaki işlem düğümlerinden Azure dosyaları paylaşımından bağlama.
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/24/2018
ms.author: lahugh
ms.custom: ''
ms.openlocfilehash: cd185035640bf0beaa54fa6a0f4d92a33837442b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093977"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Bir Batch havuzu ile Azure dosya paylaşma kullanma

[Azure dosyaları](../storage/files/storage-files-introduction.md) , bulutta sunucu ileti bloğu (SMB) protokolü aracılığıyla erişilebilen tam olarak yönetilen dosya paylaşımları sunar. Bu makalede, havuz işlem düğümlerinde bir Azure dosya paylaşımının bağlanması ve kullanılması için bilgi ve kod örnekleri sağlanmaktadır. Kod örnekleri Batch .NET ve Python SDK 'larını kullanır, ancak diğer Batch SDK 'larını ve araçlarını kullanarak benzer işlemler gerçekleştirebilirsiniz.

Batch, verileri okumak ve yazmak için Azure Storage bloblarını kullanmaya yönelik yerel API desteği sağlar. Ancak, bazı durumlarda havuz işlem düğümlerinizin bir Azure dosya paylaşımında erişmek isteyebilirsiniz. Örneğin, bir SMB dosya paylaşımının bağlı olduğu eski bir iş yükünüz veya görevleriniz paylaşılan verilere erişmesi veya paylaşılan çıkış üretmeniz gerekir. 

## <a name="considerations-for-use-with-batch"></a>Batch ile kullanım konuları

* Görece düşük sayıda paralel görevi çalıştıran havuzlarınız varsa Azure dosya paylaşımının kullanılması önerilir. Beklenen havuz boyutunuz ve varlık dosyası sayısı verildiğinde, Azure dosyalarının (bir Azure depolama hesabı kullanan) kullanılması gerekip gerekmediğini öğrenmek için [performans ve ölçek hedeflerini](../storage/files/storage-files-scale-targets.md) gözden geçirin. 

* Azure dosya paylaşımları uygun [maliyetli](https://azure.microsoft.com/pricing/details/storage/files/) ve başka bir bölgeye veri çoğaltma ile yapılandırılabilir, böylece küresel olarak yedekli olur. 

* Bir Azure dosya paylaşımından şirket içi bir bilgisayardan aynı anda bağlayabilirsiniz.

* Ayrıca bkz. Azure dosya paylaşımları için genel [Planlama konuları](../storage/files/storage-files-planning.md) .


## <a name="create-a-file-share"></a>Dosya paylaşımı oluşturma

Batch hesabınıza veya ayrı bir depolama hesabına bağlı bir depolama hesabında bir [dosya paylaşma oluşturun](../storage/files/storage-how-to-create-file-share.md) .

## <a name="mount-a-share-on-a-windows-pool"></a>Windows havuzunda bir paylaşma bağlama

Bu bölümde, bir Windows düğümleri havuzunda Azure dosya paylaşımının bağlanması ve kullanılması için adım ve kod örnekleri sağlanmaktadır. Ek arka plan için bkz. Windows 'da Azure dosya paylaşımının bağlanması için [belgelere](../storage/files/storage-how-to-use-files-windows.md) bakın. 

Batch 'de, bir görev Windows düğümünde her çalıştırıldığında paylaşıma bağlamanız gerekir. Şu anda Windows düğümlerinde görevler arasında ağ bağlantısını sürdürmek mümkün değildir.

Örneğin, her görev komut `net use` satırının bir parçası olarak dosya paylaşımının bağlanması için bir komut ekleyin. Dosya paylaşımının bağlanması için aşağıdaki kimlik bilgileri gereklidir:

* **Kullanıcı adı**: Azure\\\\storageAccountName, örneğin, Azure*mystorageaccountname* \>\<
* **Parola**: \<Storageaccountkeytedhends in = = >, örneğin, *xxxxxxxxxxxxxxxxxxxxx = =*

Aşağıdaki komut, mystorageaccountname depolama hesabındaki bir dosya paylaşımını *S:* sürücüsü olarak takar:

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

Kolaylık sağlaması için buradaki örneklerde, kimlik bilgileri doğrudan metne geçer. Uygulamada, ortam değişkenlerini, sertifikaları veya Azure Key Vault gibi bir çözümü kullanarak kimlik bilgilerini yönetmeyi kesinlikle öneririz.

Bağlama işlemini basitleştirmek için, isteğe bağlı olarak düğümlerdeki kimlik bilgilerini kalıcı hale getirin. Daha sonra, kimlik bilgileri olmadan paylaşıma bağlayabilirsiniz. Aşağıdaki iki adımı uygulayın:

1. Havuz yapılandırmasındaki bir başlangıç görevini kullanarak komutsatırıyardımcıprogramınıçalıştırın.`cmdkey` Bu, her bir Windows düğümündeki kimlik bilgilerini devam ettirir. Başlangıç görevi komut satırı şuna benzerdir:

   ```
   cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

   ```

2. Her bir düğümde, kullanarak `net use`her bir görevin parçası olarak paylaşıma bağlayın. Örneğin, aşağıdaki görev komut satırı dosya paylaşımının *S:* sürücüsü olarak takar. Bunun ardından paylaşıma başvuran bir komut veya komut dosyası gelmelidir. Önbelleğe alınan kimlik bilgileri, çağrısında `net use`kullanılır. Bu adım, havuzdaki başlangıç görevinde kullandığınız görevler için aynı kullanıcı kimliğini kullandığınızı varsayar, bu, tüm senaryolar için uygun değildir.

   ```
   cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
   ```

### <a name="c-example"></a>C#örneğinde
Aşağıdaki C# örnekte, bir başlangıç görevi kullanılarak bir Windows havuzunda kimlik bilgilerinin nasıl kalıcı yapılacağı gösterilmektedir. Depolama dosya hizmeti adı ve depolama kimlik bilgileri tanımlanmış sabitler olarak geçirilir. Burada, başlangıç görevi havuz kapsamı ile standart (yönetici olmayan) bir otomatik Kullanıcı hesabı altında çalışır.

```csharp
...
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: PoolId,
    targetDedicatedComputeNodes: PoolNodeCount,
    virtualMachineSize: PoolVMSize,
    virtualMachineConfiguration: virtualMachineConfiguration);

// Start task to store credentials to mount file share
string startTaskCommandLine = String.Format("cmd /c \"cmdkey /add:{0} /user:AZURE\\{1} /pass:{2}\"", StorageFileService, StorageAccountName, StorageAccountKey);

pool.StartTask = new StartTask
{
    CommandLine = startTaskCommandLine,
    UserIdentity = new UserIdentity(new AutoUserSpecification(
        elevationLevel: ElevationLevel.NonAdmin, 
        scope: AutoUserScope.Pool))
};

pool.Commit();
```

Kimlik bilgilerini depolamadan sonra, paylaşma 'yı bağlamak ve paylaşıma okuma veya yazma işlemlerinde başvurmak için görev komut satırları ' nı kullanın. Temel bir örnek olarak, aşağıdaki kod parçacığındaki görev komut satırı, dosya paylaşımındaki `dir` dosyaları listelemek için komutunu kullanır. Havuzdaki başlangıç görevini çalıştırmak için kullandığınız [Kullanıcı kimliğini](batch-user-accounts.md) kullanarak her bir iş görevini çalıştırdığınızdan emin olun. 

```csharp
...
string taskId = "myTask";
string taskCommandLine = String.Format("cmd /c \"net use {0} {1} & dir {2}\"", ShareMountPoint, StorageFileShare, ShareMountPoint);

CloudTask task = new CloudTask(taskId, taskCommandLine);
task.UserIdentity = new UserIdentity(new AutoUserSpecification(
    elevationLevel: ElevationLevel.NonAdmin,
    scope: AutoUserScope.Pool));
tasks.Add(task);
```

## <a name="mount-a-share-on-a-linux-pool"></a>Bir Linux havuzunda bir paylaşma bağlama

Azure dosya paylaşımları, [CIFS çekirdek istemcisi](https://wiki.samba.org/index.php/LinuxCIFS)kullanılarak Linux dağıtımları ile bağlanabilir. Aşağıdaki örnek, Ubuntu 16,04 LTS işlem düğümlerinin havuzunda bir dosya paylaşımının nasıl bağlanacağını göstermektedir. Farklı bir Linux dağıtımı kullanırsanız, genel adımlar benzerdir, ancak dağıtım için uygun paket yöneticisini kullanır. Ayrıntılar ve ek örnekler için bkz. [Azure dosyalarını Linux Ile kullanma](../storage/files/storage-how-to-use-files-linux.md).

İlk olarak, bir yönetici kullanıcı kimliği altında, `cifs-utils` paketini yükledikten sonra yerel dosya sisteminde bağlama noktasını (örneğin, */mnt/myazurefileshare*) oluşturun. Bağlama noktası için bir klasör, dosya sisteminde herhangi bir yerde oluşturulabilir, ancak bunu `/mnt` klasörü altında oluşturmak yaygın bir kuraldır. Doğrudan `/mnt` (Ubuntu 'da) veya `/mnt/resource` (diğer dağıtımlarda) bağlama noktası oluşturmadığından emin olun.

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Ardından, aşağıdaki kimlik `mount` bilgilerini sağlayarak dosya paylaşımının bağlanması için komutunu çalıştırın:

* **Kullanıcı adı**: \<storageAccountName\>, örneğin, *mystorageaccountname*
* **Parola**: \<Storageaccountkeytedhends in = = >, örneğin, *xxxxxxxxxxxxxxxxxxxxx = =*

Aşağıdaki komut, */mnt/myazurefileshare*konumundaki *mystorageaccountname* depolama hesabındaki bir dosya paylaşımını takar: 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

Kolaylık sağlaması için buradaki örneklerde, kimlik bilgileri doğrudan metne geçer. Uygulamada, ortam değişkenlerini, sertifikaları veya Azure Key Vault gibi bir çözümü kullanarak kimlik bilgilerini yönetmeyi kesinlikle öneririz.

Bir Linux havuzunda, bu adımların tümünü tek bir başlangıç görevinde birleştirebilir veya bir betikte çalıştırabilirsiniz. Başlangıç görevini havuzda yönetici kullanıcı olarak çalıştırın. Paylaşıma başvuran havuzda daha fazla görev çalıştırmadan önce, başlangıç görevinizi başarıyla tamamlanmasını bekleyecek şekilde ayarlayın.

### <a name="python-example"></a>Python örneği

Aşağıdaki Python örneği, bir Ubuntu havuzunun bir başlangıç görevinde paylaşma bağlamak için nasıl yapılandırılacağını gösterir. Bağlama noktası, dosya paylaşma uç noktası ve depolama kimlik bilgileri tanımlanmış sabitler olarak geçirilir. Başlangıç görevi, havuz kapsamıyla yönetici otomatik Kullanıcı hesabı altında çalışır.

```python
pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="16.04.0-LTS",
            version="latest"),
        node_agent_sku_id="batch.node.ubuntu 16.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install cifs-utils && mkdir -p {} && mount -t cifs {} {} -o vers=3.0,username={},password={},dir_mode=0777,file_mode=0777,serverino\"".format(
            _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_SHARE_ENDPOINT, _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_NAME, _STORAGE_ACCOUNT_KEY),
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(pool)
```

Paylaşma oluşturup bir işi tanımladıktan sonra, görev komut satırlarında paylaşma özelliğini kullanın. Örneğin, aşağıdaki temel komut dosya paylaşımındaki dosyaları `ls` listelemek için kullanır.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>Sonraki adımlar

* Toplu Işteki verileri okuma ve yazma diğer seçenekleri için bkz. [Batch özelliğine genel bakış](batch-api-basics.md) ve [kalıcı iş ve görev çıktısı](batch-task-output.md).

* Toplu iş iş yükleri için dosya sistemleri dağıtmak üzere [shipbahçe tariflerini](https://github.com/Azure/batch-shipyard/tree/master/recipes) de Içeren [Batch shipbahçe](https://github.com/Azure/batch-shipyard) araç seti ' ne bakın.
