---
title: Azure CLı ile Azure dosya paylaşımlarını yedekleme
description: Azure CLı kullanarak kurtarma hizmetleri kasasındaki Azure dosya paylaşımlarını nasıl yedekleyeceğinizi öğrenin
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: a5f7472c511a5a50415a6ceb47497dd6f4f1e60b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773630"
---
# <a name="back-up-azure-file-shares-with-azure-cli"></a>Azure CLı ile Azure dosya paylaşımlarını yedekleme

Azure komut satırı arabirimi (CLı), Azure kaynaklarını yönetmek için bir komut satırı deneyimi sağlar. Azure kaynaklarını kullanmak için özel otomasyon oluşturmaya yönelik harika bir araçtır. Bu makalede Azure CLı ile Azure dosya paylaşımlarını yedekleme hakkında bilgi yer aldığı açıklanır. Bu adımları [Azure PowerShell](./backup-azure-afs-automation.md) veya [Azure portalı](backup-afs.md) ile de gerçekleştirebilirsiniz.

Bu öğreticinin sonuna kadar, Azure CLı ile aşağıdaki işlemleri gerçekleştirmeyi öğreneceksiniz:

* Kurtarma Hizmetleri kasası oluşturma
* Azure dosya paylaşımları için yedeklemeyi etkinleştir
* Dosya paylaşımları için isteğe bağlı yedekleme tetikleyin

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Bu öğretici, Azure CLı 'nin sürüm 2.0.18 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasası oluşturma

Kurtarma Hizmetleri Kasası, tüm yedekleme öğelerinde birleştirilmiş bir görünüm ve yönetim özelliği sunan bir varlıktır. Koruma altındaki bir kaynak için yedekleme işi çalıştığında Kurtarma Hizmetleri kasasının içinde bir kurtarma noktası oluşturulur. Daha sonra bu kurtarma noktalarından birini kullanarak verileri dilediğiniz zaman geri yükleyebilirsiniz.

Kurtarma Hizmetleri Kasası oluşturmak için aşağıdaki adımları izleyin:

1. Kasa, bir kaynak grubuna yerleştirilir. Mevcut bir kaynak grubunuz yoksa, [az Group Create](/cli/azure/group#az_group_create) komutuyla yeni bir tane oluşturun. Bu öğreticide, Doğu ABD bölgesinde yeni kaynak grubu *azurefiles* oluşturacağız.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

1. Kasayı oluşturmak için [az Backup Kasası Create](/cli/azure/backup/vault#az_backup_vault_create) cmdlet 'ini kullanın. Kaynak grubu için kullanılan kasa için aynı konumu belirtin.

    Aşağıdaki örnek, Doğu ABD bölgesinde *azurefilesvault* adlı bir kurtarma hizmetleri Kasası oluşturur.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Azure dosya paylaşımları için yedeklemeyi etkinleştir

Bu bölümde, yedeklemesini yapılandırmak istediğiniz bir Azure dosya paylaşımınız olduğu varsayılır. Yoksa, [az Storage Share Create](/cli/azure/storage/share#az_storage_share_create) komutunu kullanarak bir Azure dosya paylaşma oluşturun.

Dosya paylaşımları için yedeklemeyi etkinleştirmek üzere, bir yedekleme işinin ne zaman çalışacağını ve kurtarma noktalarının ne kadar süreyle depolandığını tanımlayan bir koruma ilkesi oluşturmanız gerekir. [Az Backup Policy Create](/cli/azure/backup/policy#az_backup_policy_create) cmdlet 'ini kullanarak bir yedekleme ilkesi oluşturabilirsiniz.

Aşağıdaki örnek, *Schedule 1* yedekleme ilkesini kullanarak *afsaccount* depolama hesabındaki *azurefiles* dosya paylaşımının yedeklemesini etkinleştirmek için [az Backup Protection Enable-for-azurefileshare](/cli/azure/backup/protection#az_backup_protection_enable_for_azurefileshare) cmdlet 'ini kullanır:

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

Çıkışdaki **ad** özniteliği yedekleme hizmeti tarafından **yedekleme işleminizi etkinleştirmek** için oluşturulan işin adına karşılık gelir. İşin durumunu izlemek için [az Backup Job Show](/cli/azure/backup/job#az_backup_job_show) cmdlet 'ini kullanın.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Dosya paylaşma için isteğe bağlı yedekleme tetikleyin

Yedekleme ilkesinin işi zamanlanan saatte çalıştırmasını beklemek yerine dosya paylaşımınız için isteğe bağlı bir yedekleme tetiklemeniz istiyorsanız [az Backup Protection Backup-Now](/cli/azure/backup/protection#az_backup_protection_backup_now) cmdlet 'ini kullanın.

İsteğe bağlı yedeklemeyi tetiklemek için aşağıdaki parametreleri tanımlamanız gerekir:

* **--Container-Name** , dosya paylaşımının barındırıldığı depolama hesabının adıdır. Kapsayıcının **adını** veya **kolay adını** almak için [az Backup Container List](/cli/azure/backup/container#az_backup_container_list) komutunu kullanın.
* **--öğe-adı** , isteğe bağlı yedeklemeyi tetiklemek istediğiniz dosya paylaşımının adıdır. Yedeklenen öğenin **adını** veya **kolay adını** almak için [az Backup Item List](/cli/azure/backup/item#az_backup_item_list) komutunu kullanın.
* **--sakla-Until** , kurtarma noktasını bekletene kadar olan tarihi belirtir. Değer UTC saat biçiminde ayarlanmalıdır (gg-aa-yyyy).

Aşağıdaki örnek, *20-01-2020*'e kadar bekletme ile *afsaccount* depolama hesabındaki *azurefiles* FileShare için isteğe bağlı bir yedeklemeyi tetikler.

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

Çıktıda **ad** özniteliği, "isteğe bağlı yedekleme" işlemi için yedekleme hizmeti tarafından oluşturulan işin adına karşılık gelir. Bir işin durumunu izlemek için [az Backup Job Show](/cli/azure/backup/job#az_backup_job_show) cmdlet 'ini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* [CLI Ile Azure dosya paylaşımlarını geri yüklemeyi](restore-afs-cli.md) öğrenin
* [CLI Ile Azure dosya paylaşma yedeklemelerini yönetme](manage-afs-backup-cli.md) hakkında bilgi edinin
