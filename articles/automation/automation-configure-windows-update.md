---
title: Windows Update ayarlarını Azure Güncelleştirme Yönetimi ile çalışacak şekilde yapılandırma
description: Bu makalede, Azure Güncelleştirme Yönetimi ile çalışmak üzere yapılandırdığınız Windows Update ayarları açıklanmaktadır.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 813d34f9c07e6c2909c483f040d4f3bf09b3ad24
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72690849"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Güncelleştirme Yönetimi için Windows Update ayarlarını yapılandırma

Azure Güncelleştirme Yönetimi, Windows güncelleştirmelerini indirmek ve yüklemek için Windows Update bağımlıdır. Sonuç olarak, Windows Update tarafından kullanılan ayarların birçoğunu Güncelleştirme Yönetimi. Windows dışı güncelleştirmeleri etkinleştirmek için ayarları kullanıyorsanız, Güncelleştirme Yönetimi bu güncelleştirmeleri de yönetecektir. Güncelleştirme dağıtımı gerçekleşmeden önce güncelleştirmelerin indirilmesini etkinleştirmek istiyorsanız, güncelleştirme dağıtımı daha hızlı, daha verimli ve bakım penceresini aşmaya daha az olabilir.

## <a name="pre-download-updates"></a>Güncelleştirmeleri indirme öncesi

Grup ilkesi güncelleştirmeleri otomatik olarak indirmeyi yapılandırmak için [otomatik güncelleştirmeleri Yapılandır ayarını](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) **3**olarak ayarlayın. Bu ayar, arka planda gerekli güncelleştirmelerin indiribilmesini, ancak yüklemez. Bu şekilde Güncelleştirme Yönetimi zamanlamaların denetiminde kalır, ancak güncelleştirmeler Güncelleştirme Yönetimi bakım penceresinin dışında indirilebilir. Bu davranış, Güncelleştirme Yönetimi "bakım penceresi aşıldı" hatalarını önler.

Ayrıca, güncelleştirmelerin otomatik indirilmesi için yapılandırmak istediğiniz bir sistemde aşağıdaki PowerShell komutunu çalıştırarak da bu ayarı açabilirsiniz:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>Otomatik yüklemeyi devre dışı bırak

Varsayılan olarak, Azure sanal makineler (VM) üzerinde güncelleştirmelerin otomatik olarak yüklenmesi etkinleştirilir. Bu, Güncelleştirme Yönetimi tarafından yüklenmek üzere zamanlamadan önce güncelleştirmelerin yüklenmesine neden olabilir. @No__t_0 kayıt defteri anahtarını `1` olarak ayarlayarak bu davranışı devre dışı bırakabilirsiniz. Aşağıdaki PowerShell kod parçacığında bunun nasıl yapılacağı gösterilmektedir:

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>Yeniden başlatma ayarlarını yapılandırma

[Yeniden başlatmayı yönetmek için kullanılan](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) kayıt defteri ve kayıt defteri anahtarlarını [düzenleyerek otomatik güncelleştirmeleri yapılandırma](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) bölümünde listelenen kayıt defteri anahtarları, **güncelleştirme dağıtım** ayarlarında **hiçbir şekilde yeniden başlatma** belirtseniz bile makinelerinizin yeniden başlatılmasına neden olabilir. . Bu kayıt defteri anahtarlarını ortamınıza en uygun şekilde yapılandırmanız gerekir.

## <a name="enable-updates-for-other-microsoft-products"></a>Diğer Microsoft ürünleri için güncelleştirmeleri etkinleştir

Varsayılan olarak Windows Update yalnızca Windows için güncelleştirmeleri sağlar. **Windows 'u güncelleştirdiğimde diğer Microsoft ürünlerine yönelik güncelleştirmeleri ver** ' i etkinleştirirseniz, Microsoft SQL Server ve diğer Microsoft yazılımları için güvenlik düzeltme ekleri de dahil olmak üzere diğer ürünler için de güncelleştirmeler alırsınız. Bu seçenek grup ilkesi tarafından yapılandırılamaz. Diğer Microsoft güncelleştirmelerini etkinleştirmek istediğiniz sistemlerde aşağıdaki PowerShell komutunu çalıştırın. Güncelleştirme Yönetimi Bu ayarla uyumlu olacak.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>WSUS Yapılandırma ayarları

Güncelleştirme Yönetimi, Windows Server Update Services (WSUS) ayarlarına uyar. Güncelleştirme Yönetimi ile çalışmak için yapılandırabileceğiniz WSUS ayarları aşağıda listelenmiştir.

### <a name="intranet-microsoft-update-service-location"></a>Intranet Microsoft güncelleştirme hizmeti konumu

Güncelleştirmeleri taramak ve indirmek için, [intranet Microsoft Update hizmet konumunu belirtin](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)altında kaynakları belirtebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Windows Update ayarlarını yapılandırdıktan sonra, [Azure VM 'leriniz için güncelleştirmeleri ve düzeltme eklerini yönetme](automation-tutorial-update-management.md)bölümündeki yönergeleri izleyerek bir güncelleştirme dağıtımı zamanlayabilirsiniz.