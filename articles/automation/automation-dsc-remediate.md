---
title: Uyumlu olmayan Azure Otomasyonu durum yapılandırma sunucularını düzelt
description: Yapılandırma durumunun düzeltebilecekler sahip olduğu sunuculara yapılandırmaları isteğe bağlı olarak yeniden uygulama
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: a6c7639cb4988eb13dfaa1c151085cda6e53b5d3
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68614504"
---
# <a name="remediate-non-compliant-dsc-servers"></a>Uyumlu olmayan DSC sunucularını düzeltme

Sunucular Azure Otomasyonu durum yapılandırmasıyla kaydedildiğinde, ' yapılandırma modu ' yalnızca Apply, ApplyandMonitor veya Applyandadutocorrect olarak ayarlanır.
Mod otomatik düzeltme olarak ayarlanmamışsa, herhangi bir nedenden dolayı uyumlu durumdan alınan sunucular el ile düzeltilene kadar uyumsuz olarak kalır.

Azure işlem, müşterilerin sanal makinelerde betikleri çalıştırmasına izin veren Run komutu adlı bir özellik sunar.
Bu belge, yapılandırma DRFT 'i el ile düzeltirken bu özellik için örnek betik sağlar.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>PowerShell kullanarak Windows sanal makinelerini doğru Drın

Windows sanal makinelerinde Çalıştır komut özelliğini kullanarak adım adım yönergeler için, [Çalıştır komutuyla WINDOWS VM 'niz Içindeki PowerShell betiklerini çalıştırma](/azure/virtual-machines/windows/run-command)sayfasına bakın.

Bir Azure Otomasyonu durum yapılandırma düğümünü en son yapılandırmayı indirip uygulamayı zorlamak için `Update-DscConfiguration` cmdlet 'ini kullanın.
Ayrıntılar için bkz. cmdlet belge [güncelleştirmesi-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration).

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Linux sanal makinelerini doğru DRT

Linux sunucuları için şu anda benzer işlevler bulunmamaktadır.
Tek seçenek kayıt işlemini tekrarlamalısınız.
Azure düğümlerinde, drara düzeltme, portaldan veya az Automation cmdlet 'leri kullanılarak yapılabilir.
Bu işlemle ilgili ayrıntılar, [Azure Otomasyonu durum yapılandırması tarafından Yönetilemeyen sayfa ekleme makinelerinde](/azure/automation/automation-dsc-onboarding#azure-portal)belgelenmiştir.
Karma düğümler için, dahil edilen Python betikleri kullanılarak drara düzeltme yapılabilir.
[Linux deposu Için POWERSHELL DSC](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer)'nin belgelerine bakın.

## <a name="next-steps"></a>Sonraki adımlar

- PowerShell cmdlet başvurusu için bkz. [Azure Otomasyonu durum yapılandırması cmdlet 'leri](/powershell/module/azurerm.automation/#automation)
- Azure Otomasyonu durum yapılandırması 'nı sürekli bir dağıtım ardışık düzeninde kullanmaya ilişkin bir örnek görmek için bkz. [Azure Otomasyonu durum yapılandırması ve Chocolatey kullanarak sürekli dağıtım](automation-dsc-cd-chocolatey.md)
