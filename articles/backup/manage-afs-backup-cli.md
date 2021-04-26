---
title: Azure CLı ile Azure dosya paylaşma yedeklemelerini yönetme
description: Azure Backup tarafından yedeklenen Azure dosya paylaşımlarını yönetmek ve izlemek için Azure CLı 'yi nasıl kullanacağınızı öğrenin.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: e389f5cde12734ef4bf0be4ecfba69ba33f5e030
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773612"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>Azure CLı ile Azure dosya paylaşma yedeklemelerini yönetme

Azure CLı, Azure kaynaklarını yönetmek için bir komut satırı deneyimi sağlar. Azure kaynaklarını kullanmak için özel otomasyon oluşturmaya yönelik harika bir araçtır. Bu makalede, [Azure Backup](./backup-overview.md)tarafından yedeklenen Azure dosya paylaşımlarını yönetmek ve izlemek için görevlerin nasıl gerçekleştirileceği açıklanır. Ayrıca [Azure Portal](https://portal.azure.com/)bu adımları uygulayabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede, zaten [Azure Backup](./backup-overview.md)tarafından yedeklenen bir Azure dosya paylaşımınız olduğu varsayılır. Bir tane yoksa, dosya paylaşımlarınız için yedeklemeyi yapılandırmak üzere [CLI Ile Azure dosya paylaşımlarını](backup-afs-cli.md) yedekleme bölümüne bakın. Bu makalede aşağıdaki kaynakları kullanırsınız:
   -  **Kaynak grubu**: *azurefiles*
   -  **Recoveryserviceskasası**: *azurefilesvault*
   -  **Depolama hesabı**: *afsaccount*
   -  **Dosya paylaşma**: *azurefiles*
  
  [!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
   - Bu öğretici, Azure CLı 'nin sürüm 2.0.18 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="monitor-jobs"></a>İşleri izleme

Yedekleme veya geri yükleme işlemlerini tetiklemeniz durumunda, yedekleme hizmeti izleme için bir iş oluşturur. Tamamlanmış veya çalışmakta olan işleri izlemek için [az Backup Job List](/cli/azure/backup/job#az_backup_job_list) cmdlet 'ini kullanın. CLı ile, çalışmakta [olan bir işi askıya](/cli/azure/backup/job#az_backup_job_stop) alabilir veya [bir iş bitene kadar bekleyebilirsiniz](/cli/azure/backup/job#az_backup_job_wait).

Aşağıdaki örnek, *azurefilesvault* kurtarma hizmetleri Kasası için yedekleme işlerinin durumunu gösterir:

```azurecli-interactive
az backup job list --resource-group azurefiles --vault-name azurefilesvault
```

```output
[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "location": null,
    "name": "d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "properties": {
      "actionsInfo": null,
      "activityId": "3cef43ed-0af4-43e2-b9cb-1322c496ccb4",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:29.718011",
      "endTime": "2020-01-13T08:05:29.180606+00:00",
      "entityFriendlyName": "azurefiles",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.462595+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "location": null,
    "name": "1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "properties": {
      "actionsInfo": null,
      "activityId": "2663449c-94f1-4735-aaf9-5bb991e7e00c",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:28.145216",
      "endTime": "2020-01-13T08:05:27.519826+00:00",
      "entityFriendlyName": "azurefilesresource",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.374610+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  }
]
```

## <a name="modify-policy"></a>İlkeyi Değiştir

Yedekleme sıklığını veya bekletme aralığını değiştirmek için [az Backup Item Set-Policy](/cli/azure/backup/item#az_backup_item_set_policy)' i kullanarak bir yedekleme ilkesini değiştirebilirsiniz.

İlkeyi değiştirmek için aşağıdaki parametreleri tanımlayın:

* **--Container-Name**: dosya paylaşımının barındıracağı depolama hesabının adı. Kapsayıcının **adını** veya **kolay adını** almak için [az Backup Container List](/cli/azure/backup/container#az_backup_container_list) komutunu kullanın.
* **--Name**: ilkeyi değiştirmek istediğiniz dosya paylaşımının adı. Yedeklenen öğenin **adını** veya **kolay adını** almak için [az Backup Item List](/cli/azure/backup/item#az_backup_item_list) komutunu kullanın.
* **--Policy-Name**: dosya paylaşımınız için ayarlamak istediğiniz yedekleme ilkesinin adı. Kasanıza yönelik tüm ilkeleri görüntülemek için [az Backup Policy List](/cli/azure/backup/policy#az_backup_policy_list) ' i kullanabilirsiniz.

Aşağıdaki örnek, *afsaccount* depolama hesabında bulunan *azurefiles* dosya paylaşımının *schedule2* yedekleme ilkesini ayarlar.

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

Ayrıca, aşağıdaki iki ek parametreyi sağlayarak kapsayıcı ve öğe için kolay adları kullanarak önceki komutu çalıştırabilirsiniz:

* **--Backup-Management-Type**: *azurestorage*
* **--iş yükü-tür**: *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Çıkışdaki **ad** özniteliği, değişiklik ilkesi işlemi için yedekleme hizmeti tarafından oluşturulan işin adına karşılık gelir. İşin durumunu izlemek için [az Backup Job Show](/cli/azure/backup/job#az_backup_job_show) cmdlet 'ini kullanın.

## <a name="stop-protection-on-a-file-share"></a>Dosya paylaşımı üzerindeki korumayı durdurma

Azure dosya paylaşımlarını korumayı durdurmanın iki yolu vardır:

* Gelecekteki tüm yedekleme işlerini durdurun ve tüm kurtarma noktalarını *silin* .
* Gelecekteki tüm yedekleme işlerini durdurun, ancak kurtarma noktalarını *bırakın* .

Azure Backup tarafından oluşturulan temeldeki anlık görüntüler korunduğundan, kurtarma noktalarını depolamada bırakma ile ilişkili bir maliyet olabilir. Kurtarma noktalarını bırakma avantajı, daha sonra isterseniz dosya paylaşımının geri yükleme seçeneğidir. Kurtarma noktalarından ayrılma maliyeti hakkında daha fazla bilgi için bkz. [fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/storage/files). Tüm kurtarma noktalarını silmeyi seçerseniz dosya paylaşımından geri yükleme yapamazsınız.

Dosya paylaşımının korumasını durdurmak için aşağıdaki parametreleri tanımlayın:

* **--Container-Name**: dosya paylaşımının barındıracağı depolama hesabının adı. Kapsayıcının **adını** veya **kolay adını** almak için [az Backup Container List](/cli/azure/backup/container#az_backup_container_list) komutunu kullanın.
* **--öğe-adı**: korumasını durdurmak istediğiniz dosya paylaşımının adı. Yedeklenen öğenin **adını** veya **kolay adını** almak için [az Backup Item List](/cli/azure/backup/item#az_backup_item_list) komutunu kullanın.

### <a name="stop-protection-and-retain-recovery-points"></a>Korumayı durdurun ve kurtarma noktalarını koruyun

Verileri korurken korumayı durdurmak için [az Backup Protection Disable](/cli/azure/backup/protection#az_backup_protection_disable) cmdlet 'ini kullanın.

Aşağıdaki örnek, *azurefiles* dosya paylaşımının korumasını durduruyor ancak tüm kurtarma noktalarını korur.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

Ayrıca, aşağıdaki iki ek parametreyi sağlayarak kapsayıcı ve öğe için kolay adı kullanarak önceki komutu çalıştırabilirsiniz:

* **--Backup-Management-Type**: *azurestorage*
* **--iş yükü-tür**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Çıkışdaki **ad** özniteliği, koruma işleminizi durdur işlemi için yedekleme hizmeti tarafından oluşturulan işin adına karşılık gelir. İşin durumunu izlemek için [az Backup Job Show](/cli/azure/backup/job#az_backup_job_show) cmdlet 'ini kullanın.

### <a name="stop-protection-without-retaining-recovery-points"></a>Kurtarma noktalarını korumadan korumayı durdur

Kurtarma noktalarını korumadan korumayı durdurmak için [az Backup Protection Disable](/cli/azure/backup/protection#az_backup_protection_disable) cmdlet 'ini **Delete-Backup-Data** seçeneğini **true** olarak ayarlanmış şekilde kullanın.

Aşağıdaki örnek, kurtarma noktalarını korumadan *azurefiles* dosya paylaşımının korumasını durduruyor.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

Ayrıca, aşağıdaki iki ek parametreyi sağlayarak kapsayıcı ve öğe için kolay adı kullanarak önceki komutu çalıştırabilirsiniz:

* **--Backup-Management-Type**: *azurestorage*
* **--iş yükü-tür**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>Dosya paylaşımı üzerindeki korumayı sürdürme

Azure dosya paylaşımının korumasını durdurduysanız ancak kurtarma noktalarını koruduysanız, korumayı daha sonra sürdürebilirsiniz. Kurtarma noktalarını korumazsanız korumaya devam edebilirsiniz.

Dosya paylaşımının korumasını sürdürmesini sağlamak için aşağıdaki parametreleri tanımlayın:

* **--Container-Name**: dosya paylaşımının barındıracağı depolama hesabının adı. Kapsayıcının **adını** veya **kolay adını** almak için [az Backup Container List](/cli/azure/backup/container#az_backup_container_list) komutunu kullanın.
* **--öğe-adı**: korumayı yeniden başlatmak istediğiniz dosya paylaşımının adı. Yedeklenen öğenin **adını** veya **kolay adını** almak için [az Backup Item List](/cli/azure/backup/item#az_backup_item_list) komutunu kullanın.
* **--Policy-Name**: dosya paylaşımının korumasını sürdürmesini sağlamak istediğiniz yedekleme ilkesinin adı.

Aşağıdaki örnek, *schedule1* yedekleme ilkesini kullanarak *azurefiles* dosya paylaşımının korumasını sürdürmeniz için [az Backup Protection özgeçmişi](/cli/azure/backup/protection#az_backup_protection_resume) cmdlet 'ini kullanır.

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

Ayrıca, aşağıdaki iki ek parametreyi sağlayarak kapsayıcı ve öğe için kolay adı kullanarak önceki komutu çalıştırabilirsiniz:

* **--Backup-Management-Type**: *azurestorage*
* **--iş yükü-tür**: *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

Çıkışdaki **ad** özniteliği, koruma işlemi için yedekleme hizmeti tarafından oluşturulan işin adına karşılık gelir. İşin durumunu izlemek için [az Backup Job Show](/cli/azure/backup/job#az_backup_job_show) cmdlet 'ini kullanın.

## <a name="unregister-a-storage-account"></a>Depolama hesabının kaydını silme

Farklı bir kurtarma hizmetleri Kasası kullanarak belirli bir depolama hesabındaki dosya paylaşımlarınızı korumak istiyorsanız, önce bu depolama hesabındaki [tüm dosya paylaşımlarının korumasını durdurun](#stop-protection-on-a-file-share) . Daha sonra, koruma için şu anda kullanılan kurtarma hizmetleri kasasından hesabın kaydını silin.

Depolama hesabının kaydını silmek için bir kapsayıcı adı sağlamanız gerekir. Kapsayıcının **adını** veya **kolay adını** almak için [az Backup Container List](/cli/azure/backup/container#az_backup_container_list) komutunu kullanın.

Aşağıdaki örnek, [az Backup Container Unregister](/cli/azure/backup/container#az_backup_container_unregister) cmdlet 'ini kullanarak *afsaccount* depolama hesabının *azurefilesvault* öğesinden kaydını siler.

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

Önceki cmdlet 'i, aşağıdaki ek parametreyi sağlayarak kapsayıcının kolay adını kullanarak da çalıştırabilirsiniz:

* **--Backup-Management-Type**: *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [Azure dosya paylaşımları yedeklemesi sorunlarını giderme](troubleshoot-azure-files.md).
