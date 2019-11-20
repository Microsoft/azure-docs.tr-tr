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
ms.openlocfilehash: 07cae1cee9810646de5bf9610a29991376736373
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391820"
---
Bu hata, Azure Dosya Eşitleme hizmetine sunucudan erişilemediği zaman oluşabilir. Aşağıdaki adımlarda çalışarak bu hatayı giderebilirsiniz:

1. Windows hizmeti `FileSyncSvc.exe` ' ın güvenlik duvarınız tarafından engellenmediğinden emin olun.
2. Bağlantı noktası 443 ' nin Azure Dosya Eşitleme hizmetine giden bağlantılara açık olduğunu doğrulayın. Bunu `Test-NetConnection` cmdlet 'i ile yapabilirsiniz. Aşağıdaki `<azure-file-sync-endpoint>` yer tutucusunun URL 'SI [Azure dosya eşitleme proxy ve güvenlik duvarı ayarları](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall) belgesinde bulunabilir. 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Proxy yapılandırmasının beklenen şekilde ayarlandığından emin olun. Bu, `Get-StorageSyncProxyConfiguration` cmdlet 'i ile yapılabilir. Azure Dosya Eşitleme için proxy yapılandırmasını yapılandırma hakkında daha fazla bilgi [Azure dosya eşitleme proxy ve güvenlik duvarı ayarlarından](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall)bulunabilir.

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
    
4. Ağ bağlantısı sorunlarını gidermeyle ilgili ek yardım için ağ yöneticinize başvurun.