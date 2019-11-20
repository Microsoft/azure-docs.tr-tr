---
title: Azure VM 'de çalışan SQL Server için Azure Backup
description: Bu makalede, Azure sanal makinesinde çalışan SQL Server Azure Backup nasıl kaydedeceğinizi öğrenin.
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: 77492454e2519c98cadfb6819c850c4830015b59
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748948"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Azure VM 'de çalışan SQL Server için Azure Backup

Diğer tekliflerde Azure Backup, Azure VM 'lerinde çalışan SQL Server gibi iş yüklerini yedeklemeye yönelik destek sağlar. SQL uygulaması bir Azure VM içinde çalıştığından, yedekleme hizmeti uygulamaya erişmek ve gerekli ayrıntıları getirmek için izne ihtiyaç duyuyor.
Bunu yapmak için Azure Backup, Kullanıcı tarafından tetiklenen kayıt işlemi sırasında SQL Server çalıştığı sanal makineye **AzureBackupWindowsWorkload** uzantısını yüklenir.

## <a name="prerequisites"></a>Ön koşullar

Desteklenen senaryolar listesi için Azure Backup tarafından desteklenen [desteklenebilirlik matrisine](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#scenario-support) bakın.

## <a name="network-connectivity"></a>Ağ bağlantısı

Azure Backup, bir proxy sunucu veya listelenen IP aralıklarını dağıtmak için NSG etiketlerini destekler; yöntemlerin her biri hakkında ayrıntılı bilgi edinmek için bu [makaleye](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity)bakın.

## <a name="extension-schema"></a>Uzantı şeması

Uzantı şeması ve özellik değerleri, hizmetin CRP API 'sine geçirme yapılandırma değerleridir (çalışma zamanı ayarları). Bu yapılandırma değerleri kayıt ve yükseltme sırasında kullanılır. **AzureBackupWindowsWorkload** uzantısı bu şemayı de kullanır. Şema önceden ayarlanmıştır; objectStr alanına yeni bir parametre eklenebilir

  ```json
      "runtimeSettings": [{
      "handlerSettings": {
      "protectedSettingsCertThumbprint": "",
      "protectedSettings": {
      "objectStr": "",
      "logsBlobUri": "",
      "statusBlobUri": ""
      }
      },
      "publicSettings": {
      "locale": "en-us",
      "taskId": "1c0ae461-9d3b-418c-a505-bb31dfe2095d",
      "objectStr": "",
      "commandStartTimeUTCTicks": "636295005824665976",
      "vmType": "vmType"
      }
      }]
      }
  ```

Aşağıdaki JSON, WorkloadBackup uzantısının şemasını gösterir.  

  ```json
  {
    "type": "extensions",
    "name": "WorkloadBackup",
    "location":"<myLocation>",
    "properties": {
      "publisher": "Microsoft.RecoveryServices",
      "type": "AzureBackupWindowsWorkload",
      "typeHandlerVersion": "1.1",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "locale":"<location>",
        "taskId":"<TaskId used by Azure Backup service to communicate with extension>",

        "objectStr": "<The configuration passed by Azure Backup service to extension>",

        "commandStartTimeUTCTicks": "<Scheduled start time of registration or upgrade task>",
        "vmType": "<Type of VM where registration got triggered Eg. Compute or ClassicCompute>"
      },
      "protectedSettings": {
        "objectStr": "<The sensitive configuration passed by Azure Backup service to extension>",
        "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
        "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
      }
    }
  }
  ```

### <a name="property-values"></a>Özellik değerleri

Ad | Değer/örnek | Veri türü
 --- | --- | ---
ayarlar | En-US  |  string
TaskID | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | string
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = = " | string
commandStartTimeUTCTicks | "636967192566036845"  | string
vmType  | "Microsoft. COMPUTE/virtualmachines"  | string
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = = " | string
logsBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | string
statusBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | string

## <a name="template-deployment"></a>Şablon dağıtımı

Sanal makinede SQL Server yedeklemeyi etkinleştirerek bir sanal makineye AzureBackupWindowsWorkload uzantısının eklenmesinin önerildiğini öneririz. Bu, SQL Server VM yedeklemenin otomatikleştirilmesi için tasarlanan [Kaynak Yöneticisi şablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) aracılığıyla elde edilebilir.

## <a name="powershell-deployment"></a>PowerShell dağıtımı

Kurtarma Hizmetleri kasasıyla SQL uygulamasını içeren Azure VM 'yi ' kaydetmeniz ' gerekir. Kayıt sırasında, AzureBackupWindowsWorkload uzantısı VM 'ye yüklenir. VM 'yi kaydettirmek için [register-AzRecoveryServicesBackupContainerPS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) cmdlet 'ini kullanın.

```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

Komut bu kaynağın bir **yedekleme kapsayıcısını** döndürür ve durum **kaydedilir**.

## <a name="next-steps"></a>Sonraki adımlar

- Azure SQL Server VM yedekleme sorunlarını giderme yönergeleri hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot)
- Azure sanal makinelerinde (VM 'Ler) çalışan ve Azure Backup hizmetini kullanan SQL Server veritabanlarının yedeklenmesi hakkında [genel sorular](https://docs.microsoft.com/azure/backup/faq-backup-sql-server) .
