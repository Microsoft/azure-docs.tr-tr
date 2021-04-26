---
title: StorSimple cihazlarını yönetmek için Azurere PowerShell betikleri kullanın
description: StorSimple 8000 serisi cihazınızı yönetmek için Azure Resource Manager SDK tabanlı betikleri nasıl kullanacağınızı öğrenin.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: e3339f883d177366a64187e0c090752527802c8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94968783"
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>StorSimple cihazlarını yönetmek için Azure Resource Manager SDK tabanlı betikleri kullanma

Bu makalede, StorSimple 8000 serisi cihazınızı yönetmek için SDK tabanlı betiklerin Azure Resource Manager nasıl kullanılabileceği açıklanmaktadır. Ortamınızı bu betikleri çalıştırmak için yapılandırma adımlarında size yol gösterecek bir örnek betik de eklenmiştir.

Bu makale yalnızca Azure portal ' de çalışan StorSimple 8000 serisi cihazları için geçerlidir.

## <a name="sample-scripts"></a>Örnek betikler

Aşağıdaki örnek betikler çeşitli StorSimple işlerini otomatikleştirmek için kullanılabilir.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Azure Resource Manager SDK tabanlı örnek betikler tablosu

| Azure Resource Manager betiği                    | Description                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Authorize-ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | Bu betik, StorSimple cihazınızı, hizmet veri şifreleme anahtarını değiştirecek şekilde yetkilendirebilmeniz için izin verir.                                                                                                           |
| [Create-StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Bu betik bir 8010 veya 8020 StorSimple Cloud Appliance oluşturur. Bulut gereci daha sonra StorSimple Veri Yöneticisi hizmetinize yapılandırılıp kaydedilebilir.                                                       |
| [CreateOrUpdate-Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Bu betik StorSimple birimlerini oluşturur veya değiştirir.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Bu betik, StorSimple Device Manager hizmetinize kayıtlı bir cihaza yönelik tüm yedeklemeleri listeler.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Bu betik, StorSimple cihazınız için tüm yedekleme ilkelerine yöneliktir.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Bu betik, StorSimple Device Manager hizmetinizde çalışan tüm StorSimple işlerini alır.                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Bu betik güncelleştirme sunucusunu tarar ve StorSimple cihazınıza yüklenmek üzere güncelleştirmelerin olup olmadığını bilmenizi sağlar.                                                                                          |
| [Install-DeviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Bu betik, StorSimple cihazınıza kullanılabilir güncelleştirmeleri yüklüyor.                                                                                                                                           |
| [Manage-CloudSnapshots.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Bu betik el ile bulut anlık görüntüsünü başlatır ve belirtilen saklama günlerden eski olan bulut anlık görüntülerini siler.                                                                                                   |
| [Monitor-Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Bu Azure Otomasyonu runbook PowerShell betiği tüm yedekleme işlerinin durumunu bildirir.                                                                                                              |
| [Remove-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Bu betik tek bir yedekleme nesnesini siler.                                                                                                                                                           |
| [Start-DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Bu betik, StorSimple cihazınızda el ile yedekleme başlatır.                                                                                                                                       |
| [Update-CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | Bu betik, StorSimple Device Manager hizmetinize kayıtlı tüm 8010/8020 StorSimple bulut gereçlerine yönelik hizmet veri şifreleme anahtarını güncelleştirir.                                     |
| [Verify-BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Bu betik, yedekleme ilkeleriyle ilişkili tüm zamanlamaları analiz ettikten sonra eksik yedeklemeleri vurgular. Ayrıca, yedekleme kataloğunu kullanılabilir yedeklemeler listesiyle doğrular.             |




## <a name="configure-and-run-a-sample-script"></a>Örnek betiği yapılandırma ve çalıştırma

Bu bölüm örnek bir betiği alır ve betiği çalıştırmak için gereken çeşitli adımlara ilişkin ayrıntılar sağlar.

### <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, şunları kullandığınızdan emin olun:

*   Azure PowerShell yüklendi. Azure PowerShell modüllerini yüklemek için:
    * Bir Windows ortamında, [Azure PowerShell yükleyip yapılandırma](/powershell/azure/install-az-ps)içindeki adımları izleyin. Kullanıyorsanız, StorSimple için Windows Server ana bilgisayarınıza Azure PowerShell yükleyebilirsiniz.
    * Bir Linux veya MacOS ortamında, [MacOS veya Linux üzerinde Azure PowerShell yükleyip yapılandırma](/powershell/azure/install-az-ps)bölümündeki adımları izleyin.

Azure PowerShell kullanma hakkında daha fazla bilgi için [Azure PowerShell kullanmaya başlama](/powershell/azure/get-started-azureps)bölümüne gidin.

### <a name="run-azure-powershell-script"></a>Azure PowerShell Betiği Çalıştır

Bu örnekte kullanılan betik, StorSimple cihazındaki tüm işleri listeler. Bu, başarılı, başarısız veya sürmekte olan işleri içerir. Betiği indirmek ve çalıştırmak için aşağıdaki adımları gerçekleştirin.

1. Azure PowerShell’i çalıştırın. Yeni bir klasör oluşturun ve dizini yeni klasör ile değiştirin.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. Önceki adımda oluşturulan klasöre [NUGET CLI 'Yı indirin](https://www.nuget.org/downloads) . _nuget.exe_ çeşitli sürümleri vardır. SDK 'nize karşılık gelen sürümü seçin. Her indirme bağlantı noktası doğrudan bir _. exe_ dosyasına bağlanır. Sağ tıklayıp dosyayı tarayıcıdan çalıştırmak yerine bilgisayarınıza kaydettiğinizden emin olun.

    Ayrıca, aşağıdaki komutu çalıştırarak betiği daha önce oluşturduğunuz aynı klasöre indirebilir ve kaydedebilirsiniz.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Bağımlı SDK 'Yı indirin.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Betiği örnek GitHub projesinden indirin.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. Betiği çalıştırın. Kimlik doğrulaması istendiğinde, Azure kimlik bilgilerinizi sağlayın. Bu betik, StorSimple cihazınızdaki tüm işlerin filtrelenmiş bir listesini çıktısından oluşmalıdır.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Örnek çıktı

Örnek betik çalıştırıldığında aşağıdaki çıktı sunulur. Çıktı, 25 Eylül 2017 ' de başlayan ve 2 Ekim 2017 tarihinde tamamlanan kayıtlı bir cihazda çalıştırılan tüm işleri içerir.

```
-----------------------------------------
Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

PS C:\Scripts\StorSimpleSDKTools> wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1
PS C:\Scripts\StorSimpleSDKTools> .\Get-DeviceJobs.ps1 -SubscriptionId 1234ab5c-678d-910e-9fc4-0accc9c0166e -TenantId 12a345bc-67d8-91ef-01ab-2c7cd123ef45 -DeviceName 8600-ABC1234567D89EF -ResourceGroupName Contoso -ManagerName ContosoDeviceMgr -FilterByStartTime "09/25/2017 08:10:02" -FilterByEndTime "10/02/2017 08:10:02"


Status            : Succeeded
StartTime         : 10/2/2017 10:30:02 PM
EndTime           : 10/2/2017 10:31:36 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : ss-asr-policy1
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC/jobs/75905c48-b153-4af1-8b21-4b9a2ff9
                    825b
Name              : 75905c48-b153-4af1-8b21-4b9a2ff9825b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000
-------------------------------------------
CUT              CUT  
-------------------------------------------
Status            : Succeeded
StartTime         : 9/26/2017 5:00:02 PM
EndTime           : 9/26/2017 5:01:20 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : 8010 policy
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF/jobs/3cfd8108-db60-4e9a-a8da-6d8fe457
                    8d2b
Name              : 3cfd8108-db60-4e9a-a8da-6d8fe4578d2b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000



PS C:\Scripts\StorSimpleSDKTools>
--------------------------------------------

```


## <a name="next-steps"></a>Sonraki adımlar

StorSimple [cihazınızı yönetmek Için storsimple Device Manager hizmetini kullanın](storsimple-8000-manager-service-administration.md).