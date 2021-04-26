---
title: include dosyası
description: include dosyası
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4407ffb9ab16a720ef00a288d72b0fb4c2dbced1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774578"
---
Sunucudan Azure Dosya Eşitleme hizmetine erişilemediğinde bu hata oluşabilir. Aşağıdaki adımları uygulayarak bu hataya giderebilirsiniz:

1. Windows hizmetinin `FileSyncSvc.exe` güvenlik duvarınız tarafından engellenmediğinden emin olun.
2. Bağlantı noktası 443 ' nin Azure Dosya Eşitleme hizmetine giden bağlantılara açık olduğunu doğrulayın. Bunu `Test-NetConnection` cmdlet 'i ile yapabilirsiniz. Aşağıdaki `<azure-file-sync-endpoint>` yer tutucusunun URL'si [Azure Dosya Eşitleme ara sunucu ve güvenlik duvarı ayarları](../articles/storage/file-sync/file-sync-firewall-and-proxy.md#firewall) belgesinde bulunabilir. 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Ara sunucu yapılandırmasının beklendiği gibi ayarlandığından emin olun. Bu, `Get-StorageSyncProxyConfiguration` cmdlet'iyle yapılabilir. Azure Dosya Eşitleme için ara sunucu yapılandırmasıyla ilgili daha fazla bilgiyi [Azure Dosya Eşitleme ara sunucu ve güvenlik duvarı ayarları](../articles/storage/file-sync/file-sync-firewall-and-proxy.md#firewall) belgesinde bulabilirsiniz.

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
4. Hizmet uç noktalarına ağ bağlantısını denetlemek için Test-StorageSyncNetworkConnectivity cmdlet 'ini kullanın. Daha fazla bilgi için bkz. [hizmet uç noktalarına ağ bağlantısını test](../articles/storage/file-sync/file-sync-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints)etme.    

5. Ağ bağlantısı sorunlarını gidermeyle ilgili ek yardım için ağ yöneticinize başvurun.