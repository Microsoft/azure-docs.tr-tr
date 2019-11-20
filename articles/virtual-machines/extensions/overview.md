---
title: Azure sanal makine uzantıları ve özellikleri
description: Azure VM uzantılarının ne olduğunu ve bunları Azure sanal makineleri ile nasıl kullanacağınızı öğrenin
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/12/2019
ms.author: akjosh
ms.openlocfilehash: bf17f499c1e8339a1e9abb13cffd5e35c390f564
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072972"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Azure sanal makine uzantıları ve özellikleri
Azure sanal makinesi (VM) uzantıları, Azure VM 'lerinde dağıtım sonrası yapılandırma ve otomasyon görevleri sağlayan küçük uygulamalardır, var olan görüntüleri kullanabilir ve sonra, özel bir iş elde etmeniz için, dağıtımlarınızın bir parçası olarak bunları özelleştirebilirsiniz görüntü oluşturma.

Azure platformu, VM yapılandırması, izleme, güvenlik ve yardımcı program uygulamalarından çok çeşitli uzantılar barındırır. Yayımcılar bir uygulamayı alır, ardından bir uzantıya sarın ve yüklemeyi basitleştirerek, tüm yapmanız gereken zorunlu parametreler sağlamalıdır. 

 Birinci ve üçüncü taraf uzantılarının büyük bir seçimi vardır, uzantı deposundaki uygulama yoksa, Özel Betik uzantısını kullanabilir ve VM 'nizi kendi komut dosyalarınız ve komutlarınız ile yapılandırabilirsiniz.

Uzantıların kullanıldığı önemli senaryolar örnekleri:
* VM yapılandırması, VM yapılandırma aracılarını yüklemek ve VM 'nizi yapılandırmak için PowerShell DSC (Istenen durum yapılandırması), Chef, Pupevcil hayvan ve özel betik uzantılarını kullanabilirsiniz. 
* Symantec, ESET gibi AV ürünleri.
* Qualys, Rapid7, HPE gibi VM Güvenlik Açığı aracı.
* DynaTrace, Azure ağ Izleyicisi, Site24x7 ve Stacking gibi VM ve uygulama izleme araçları.

Uzantılar, yeni bir VM dağıtımıyla birlikte paketlenmiştir. Örneğin, bu uygulamalar daha büyük bir dağıtımın parçası olabilirler, VM sağlaması üzerinde uygulamaları yapılandırıyor veya desteklenen tüm uzantı çalıştırılan sistemlerin dağıtımı sonrasında çalıştırılabilir.

## <a name="how-can-i-find-what-extensions-are-available"></a>Hangi uzantıların kullanılabilir olduğunu nasıl bulabilirim?
Kullanılabilir uzantıları portaldaki VM dikey penceresinde görüntüleyebilirsiniz, Uzantılar altında bu yalnızca küçük bir miktarı temsil eder, tam liste için CLı araçlarını kullanabilir, [Linux IÇIN VM uzantılarını bulma](features-linux.md) ve [Windows Için VM uzantılarını bulma](features-windows.md)bölümüne bakın.

## <a name="how-can-i-install-an-extension"></a>Bir uzantıyı nasıl yükleyebilirim?
Azure VM uzantıları, Azure CLı, Azure PowerShell, Azure Resource Manager şablonları ve Azure portal kullanılarak yönetilebilir. Bir uzantıyı denemek için Azure portal gidebilir, Özel Betik uzantısını seçebilir, sonra bir komut/betik geçirebilir ve uzantıları çalıştırabilirsiniz.

Portala CLı veya Kaynak Yöneticisi şablonuna göre eklediğiniz aynı uzantıya isterseniz, bkz. [Windows Özel Betik uzantısı](custom-script-windows.md) ve [Linux özel Betik uzantısı](custom-script-linux.md)gibi farklı uzantı belgeleri.

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Nasıl yaparım? uzantısı uygulama yaşam döngüsünü yönetmek mi istiyorsunuz?
Uzantıyı yüklemek veya silmek için bir VM 'ye doğrudan bağlanmanız gerekmez. Azure uzantı uygulaması yaşam döngüsü VM dışında yönetiliyor ve Azure platformuyla tümleştirildiği için, uzantının tümleşik durumunu da alırsınız.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Uzantılar için göz önünde bulundurulması gereken başka bir şey var mı?
Uzantılar yükleme uygulamalar gibi bazı gereksinimler vardır, uzantılar için desteklenen Windows ve Linux Işletim sistemleri listesi ve Azure VM aracılarının yüklü olması gerekir. Bazı bağımsız VM Uzantısı uygulamalarının bir uç noktaya erişim gibi kendi ortam önkoşulları olabilir.

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
| vmaccessforlinux. Microsoft. ostcextensions | [Linux için parolayı sıfırlama (VMAccess)](vmaccess.md#troubleshoot-and-support) |
| Microsoft. recoveryservices. VMSnapshot | [Linux için anlık görüntü](vmsnapshot-linux.md#troubleshoot-and-support) |
| Microsoft. recoveryservices. VMSnapshot | [Windows için anlık görüntü](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Sonraki adımlar
* Linux aracısının ve uzantılarının nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Linux Için Azure VM uzantıları ve özellikleri](features-linux.md).
* Windows Konuk aracısının ve uzantılarının nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Windows Için Azure VM uzantıları ve özellikleri](features-windows.md).  
* Windows Konuk Aracısı 'nı yüklemek için bkz. [Azure Windows sanal makine aracısına genel bakış](agent-windows.md).  
* Linux Aracısı 'nı yüklemek için bkz. [Azure Linux sanal makine aracısına genel bakış](agent-linux.md).  

