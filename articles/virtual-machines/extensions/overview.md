---
title: Azure sanal makine uzantıları ve özellikleri
description: Azure VM uzantıları hakkında daha fazla bilgi
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.date: 08/03/2020
ms.openlocfilehash: e1b96293db0389201fdab3340d8f0e74fefc4c52
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559728"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Azure sanal makine uzantıları ve özellikleri
Uzantılar, Azure VM 'lerinde dağıtım sonrası yapılandırma ve otomasyon sağlayan küçük uygulamalardır. Azure platformu VM yapılandırması, izleme, güvenlik ve yardımcı program uygulamalarını kapsayan birçok uzantıyı barındırır. Yayımcılar bir uygulamayı alır, bir uzantıya sarın ve yüklemeyi basitleştirir. Yapmanız gereken tek şey zorunlu parametreler sağlamaktır. 

## <a name="how-can-i-find-what-extensions-are-available"></a>Hangi uzantıların kullanılabilir olduğunu nasıl bulabilirim?
Sol menüdeki **uzantıları** SEÇIN bir VM seçerek kullanılabilir uzantıları görüntüleyebilirsiniz. Uzantıların tam listesini almak için bkz. [Linux IÇIN VM uzantılarını bulma](features-linux.md) ve [Windows Için VM uzantılarını bulma](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Bir uzantıyı nasıl yükleyebilirim?
Azure VM uzantıları, Azure CLı, PowerShell, Kaynak Yöneticisi şablonları ve Azure portal kullanılarak yönetilebilir. Bir uzantıyı denemek için Azure portal gidin, Özel Betik uzantısını seçin, sonra uzantıyı çalıştırmak için bir komut veya betik geçirin.

Daha fazla bilgi için bkz. [Windows Özel Betik uzantısı](custom-script-windows.md) ve [Linux özel Betik uzantısı](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Nasıl yaparım? uzantısı uygulama yaşam döngüsünü yönetmek mi istiyorsunuz?
Bir uzantıyı yüklemek veya silmek için bir VM 'ye doğrudan bağlanmanız gerekmez. Azure uzantı yaşam döngüsü, sanal makine dışında yönetilir ve Azure platformunda tümleşiktir.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Uzantılar için göz önünde bulundurulması gereken başka bir şey var mı?
Bazı bağımsız VM Uzantısı uygulamalarının bir uç noktaya erişim gibi kendi ortam önkoşulları olabilir. Her uzantının, hangi işletim sistemlerinin desteklendiği de dahil olmak üzere önkoşulları açıklayan bir makalesi vardır.

## <a name="troubleshoot-extensions"></a>Uzantı sorunlarını giderme

Her bir uzantıya ilişkin sorun giderme bilgileri, uzantının genel bakış konusunun **sorun giderme ve destek** bölümünde bulunabilir. Kullanılabilir sorun giderme bilgilerinin listesi aşağıda verilmiştir:

| Ad Alanı | Sorun giderme |
|-----------|-----------------|
| Microsoft. Azure. Monitoring. dependencyagent. dependencyagentlinux | [Linux için Azure Izleyici bağımlılığı](agent-dependency-linux.md#troubleshoot-and-support) |
| Microsoft. Azure. Monitoring. dependencyagent. dependencyagentwindows | [Windows için Azure Izleyici bağımlılığı](agent-dependency-windows.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. azurediskencryptionforlinux | [Linux için Azure disk şifrelemesi](azure-disk-enc-linux.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. azurediskencryption | [Windows için Azure disk şifrelemesi](azure-disk-enc-windows.md#troubleshoot-and-support) |
| Microsoft. COMPUTE. customscriptextension | [Windows için özel betik](custom-script-windows.md#troubleshoot-and-support) |
| Microsoft. ostcextensions. customscriptforlinux | [Linux için istenen durum yapılandırması](dsc-linux.md#troubleshoot-and-support) |
| Microsoft. PowerShell. DSC | [Windows için istenen durum yapılandırması](dsc-windows.md#troubleshoot-and-support) |
| Microsoft. hpccompute. nvidiagpudriverlinux | [Linux için NVıDıA GPU sürücü uzantısı](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| Microsoft. hpccompute. nvidiagpudriverwindows | [Windows için NVıDıA GPU sürücü uzantısı](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. ıaasantimalware | [Windows için kötü amaçlı yazılımdan koruma uzantısı](iaas-antimalware-windows.md#troubleshoot-and-support) |
| Microsoft. enterprisecloud. Monitoring. omsagentforlinux | [Linux için Azure Izleyici](oms-linux.md#troubleshoot-and-support)
| Microsoft. enterprisecloud. Monitoring. microsoftmonitoringagent | [Windows için Azure Izleyici](oms-windows.md#troubleshoot-and-support) |
| stackbelirt. linuxagent. Extension. stackifılinuxbir TExtension | [Linux için yeniden Izlemeyi stackbelirt](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux. Microsoft. ostcextensions | [Linux için parolayı sıfırlama](vmaccess.md#troubleshoot-and-support) |
| Microsoft. recoveryservices. VMSnapshot | [Linux için anlık görüntü](vmsnapshot-linux.md#troubleshoot-and-support) |
| Microsoft. recoveryservices. VMSnapshot | [Windows için anlık görüntü](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Sonraki adımlar
* Linux aracısının ve uzantılarının nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Linux Için Azure VM uzantıları ve özellikleri](features-linux.md).
* Windows Konuk aracısının ve uzantılarının nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Windows Için Azure VM uzantıları ve özellikleri](features-windows.md).  
* Windows Konuk Aracısı 'nı yüklemek için bkz. [Azure Windows sanal makine aracısına genel bakış](agent-windows.md).  
* Linux Aracısı 'nı yüklemek için bkz. [Azure Linux sanal makine aracısına genel bakış](agent-linux.md).  

