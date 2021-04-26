---
title: CLı kullanarak mevcut VM yedekleme ilkesini güncelleştirme
description: Azure CLı kullanarak mevcut VM yedekleme ilkesini güncelleştirmeyi öğrenin.
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: 33083d6585d2b9296cd184ba258b8d2143d685b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98728587"
---
# <a name="update-the-existing-vm-backup-policy-using-cli"></a>CLı kullanarak mevcut VM yedekleme ilkesini güncelleştirme

Azure CLı kullanarak var olan bir VM yedekleme ilkesini güncelleştirebilirsiniz. Bu makalede, var olan ilkenin bir JSON dosyasına nasıl aktarılacağı, dosyanın nasıl değiştirileceği ve sonra ilkeyi değiştirilen ilkeyle güncelleştirmek için Azure CLı 'nin nasıl kullanılacağı açıklanmaktadır.

## <a name="modify-an-existing-policy"></a>Mevcut ilkeyi değiştirme

Var olan bir VM yedekleme ilkesini değiştirmek için şu adımları izleyin:

1. Güncelleştirmek istediğiniz ilkenin ayrıntılarını almak için [az Backup Policy Show](/cli/azure/backup/policy#az_backup_policy_show) komutunu yürütün.

    Örnek:

    ```azurecli
    az backup policy show --name testing123 --resource-group rg1234 --vault-name testvault
    ```

    Yukarıdaki örnekte, *testing123* ADLı bir VM ilkesinin ayrıntıları gösterilmektedir.

    Çıkış:

    ```json
    {
    "eTag": null,
    "id": "/Subscriptions/efgsf-123-test-subscription/resourceGroups/rg1234/providers/Microsoft.RecoveryServices/vaults/testvault/backupPolicies/testing123",
    "location": null,
    "name": "testing123",
    "properties": {
        "backupManagementType": "AzureIaasVM",
        "instantRpDetails": {
        "azureBackupRgNamePrefix": null,
        "azureBackupRgNameSuffix": null
        },
        "instantRpRetentionRangeInDays": 2,
        "protectedItemsCount": 0,
        "retentionPolicy": {
        "dailySchedule": {
            "retentionDuration": {
            "count": 180,
            "durationType": "Days"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "monthlySchedule": null,
        "retentionPolicyType": "LongTermRetentionPolicy",
        "weeklySchedule": {
            "daysOfTheWeek": [
            "Sunday"
            ],
            "retentionDuration": {
            "count": 30,
            "durationType": "Weeks"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "yearlySchedule": null
        },
        "schedulePolicy": {
        "schedulePolicyType": "SimpleSchedulePolicy",
        "scheduleRunDays": null,
        "scheduleRunFrequency": "Daily",
        "scheduleRunTimes": [
            "2020-08-03T04:30:00+00:00"
        ],
        "scheduleWeeklyFrequency": 0
        },
        "timeZone": "UTC"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
    }
    ```

1. Yukarıdaki çıktıyı bir. JSON dosyasına kaydedin. Örneğin, bunu *üzerindePolicy.js* olarak kaydedelim.
1. Gereksinimlerinize göre JSON dosyasını güncelleştirin ve değişiklikleri kaydedin.

    Örnek: haftalık bekletmenin 60 güne güncelleştirilmesi Için, sayıyı 60 olarak değiştirerek JSON dosyasının aşağıdaki bölümünü güncelleştirin.

    ```json
            "retentionDuration": {
          "count": 60,
          "durationType": "Weeks"
        }

    ```

1. Değişiklikleri kaydedin.
1. [Az Backup Policy set](/cli/azure/backup/policy#az_backup_policy_set) komutunu yürütün ve güncelleştirilmiş JSON dosyasının tam yolunu **--Policy** parametresinin değeri olarak geçirin.

    ```azurecli
    az backup policy set --resource-group rg1234 --vault-name testvault --policy C:\temp2\Policy.json --name testing123
    ```

>[!NOTE]
>Örnek JSON ilkesini, [az Backup Policy Get-default-for-VM](/cli/azure/backup/policy#az_backup_policy_get_default_for_vm) komutunu yürüterek de alabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Backup hizmeti ile Azure VM yedeklemelerini yönetme](backup-azure-manage-vms.md)