---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: f002fec531a0355e803a1545990fc0b13b535742
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582748"
---
Herhangi bir cihaz sorunu yaşarsanız, sistem günlüklerinden bir destek paketi oluşturabilirsiniz. Microsoft Desteği sorunları gidermek için bu paketi kullanır. Destek paketi oluşturmak için aşağıdaki adımları izleyin:

1. [Cihazınızın PowerShell arabirimine bağlanın](#connect-to-the-powershell-interface).
2. `Get-HcsNodeSupportPackage`Bir destek paketi oluşturmak için komutunu kullanın. Cmdlet 'in kullanımı aşağıdaki gibidir:

    ```powershell
    Get-HcsNodeSupportPackage [-Path] <string> [-Zip] [-ZipFileName <string>] [-Include {None | RegistryKeys | EtwLogs
            | PeriodicEtwLogs | LogFiles | DumpLog | Platform | FullDumps | MiniDumps | ClusterManagementLog | ClusterLog |
            UpdateLogs | CbsLogs | StorageCmdlets | ClusterCmdlets | ConfigurationCmdlets | KernelDump | RollbackLogs |
            Symbols | NetworkCmdlets | NetworkCmds | Fltmc | ClusterStorageLogs | UTElement | UTFlag | SmbWmiProvider |
            TimeCmds | LocalUILogs | ClusterHealthLogs | BcdeditCommand | BitLockerCommand | DirStats | ComputeRolesLogs |
            ComputeCmdlets | DeviceGuard | Manifests | MeasuredBootLogs | Stats | PeriodicStatLogs | MigrationLogs |
            RollbackSupportPackage | ArchivedLogs | Default}] [-MinimumTimestamp <datetime>] [-MaximumTimestamp <datetime>]
            [-IncludeArchived] [-IncludePeriodicStats] [-Credential <pscredential>]  [<CommonParameters>]
    ```

    Cmdlet 'i cihazınızdan günlükleri toplar ve bu günlükleri belirtilen bir ağa veya yerel paylaşıma kopyalar.

    Kullanılan parametreler aşağıdaki gibidir:

    - `-Path` -Destek paketini kopyalamak için ağı veya yerel yolu belirtin. istenir
    - `-Credential` -Korumalı yola erişmek için kimlik bilgilerini belirtin.
    - `-Zip` -Bir ZIP dosyası oluşturmak için belirtin.
    - `-Include` -Destek paketine dahil edilecek bileşenleri dahil etmek için belirtin. Belirtilmezse, `Default` varsayılır.
    - `-IncludeArchived` -Destek paketine arşivlenmiş günlükleri dahil etmek için belirtin.
    - `-IncludePeriodicStats` -Destek paketine düzenli stat günlüklerini dahil etmek için belirtin.

    
