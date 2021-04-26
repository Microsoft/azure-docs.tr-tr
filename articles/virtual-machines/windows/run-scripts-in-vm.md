---
title: Azure Windows VM 'de betikleri çalıştırma
description: Bu konu, bir Windows sanal makinesi içinde betikleri çalıştırmayı açıklar
services: automation
ms.service: virtual-machines
ms.collection: windows
author: bobbytreed
ms.author: robreed
ms.date: 05/02/2018
ms.topic: how-to
manager: carmonm
ms.openlocfilehash: 7bf62eb2ab8d2ce82ce73e3e8ae26cf303b8ba67
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765890"
---
# <a name="run-scripts-in-your-windows-vm"></a>Windows sanal makinenizde betikleri çalıştırma

Görevleri otomatikleştirmek veya sorunları gidermek için, bir VM 'de komutları çalıştırmanız gerekebilir. Aşağıdaki makale, VM 'lerinizde betikleri ve komutları çalıştırmak için kullanılabilen özelliklere kısa bir genel bakış sunar.

## <a name="custom-script-extension"></a>Özel Betik Uzantısı

[Özel Betik uzantısı](../extensions/custom-script-windows.md) öncelikle dağıtım sonrası yapılandırma ve yazılım yüklemesi için kullanılır.

* Azure sanal makinelerinde betikleri indirip çalıştırın.
* Azure Resource Manager şablonları, Azure CLı, REST API, PowerShell veya Azure portal kullanılarak çalıştırılabilir.
* Betik dosyaları Azure Storage veya GitHub 'dan indirilebilir veya Azure portal çalıştırıldığında BILGISAYARıNıZDAN sağlanmalıdır.
* Linux makinelerde Windows makinelerinde ve Bash betikte PowerShell betiğini çalıştırın.
* Dağıtım sonrası yapılandırma, yazılım yükleme ve diğer yapılandırma ya da yönetim görevleri için faydalıdır.

## <a name="run-command"></a> komutunu çalıştırın

[Çalıştır komutu](run-command.md) özelliği, sanal makine ve uygulama yönetimini ve betikleri kullanarak sorun gidermeyi ve makinenin ulaşılamaz olduğu durumlarda bile kullanılabilir. Örneğin, Konuk GÜVENLIK duvarında RDP veya SSH bağlantı noktası açık olmaz.

* Azure sanal makinelerinde betikleri çalıştırın.
* [Azure Portal](run-command.md), [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), [Azure CLI](/cli/azure/vm/run-command#az_vm_run_command_invoke)veya [PowerShell](/powershell/module/az.compute/invoke-azvmruncommand) kullanarak çalıştırılabilir
* Azure portal hızlıca bir betiği çalıştırın ve çıktıyı görüntüleyin ve gerektiğinde tekrarlayın.
* Betik doğrudan yazılabilir veya yerleşik betiklerin birini çalıştırabilirsiniz.
* Linux makinelerde Windows makinelerinde ve Bash betikte PowerShell betiğini çalıştırın.
* Sanal makine ve uygulama yönetimi için ve ulaşılamaz olan sanal makinelerde betikleri çalıştırmak için faydalıdır.

## <a name="hybrid-runbook-worker"></a>Karma Runbook Çalışanı

[Karma Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) , kullanıcının bir Otomasyon hesabında depolanan özel betiklerine sahip genel makine, uygulama ve ortam yönetimi sağlar.

* Betikleri Azure 'da ve Azure dışı makinelerde çalıştırın.
* Azure portal, Azure CLı, REST API, PowerShell, Web kancası kullanılarak çalıştırılabilir.
* Otomasyon hesabında depolanan ve yönetilen betikler.
* PowerShell, PowerShell Iş akışı, Python veya grafik runbook 'ları çalıştırma
* Betik çalıştırma sırasında zaman sınırı yok.
* Birden çok komut dosyası aynı anda çalışabilir.
* Tam betik çıkışı döndürülür ve depolanır.
* İş geçmişi 90 gün boyunca kullanılabilir.
* Betikler, yerel sistem veya Kullanıcı tarafından sağlanan kimlik bilgileriyle çalıştırılabilir.
* [El ile yükleme](../../automation/automation-windows-hrw-install.md) gerektirir

## <a name="serial-console"></a>Seri konsol

[Seri konsol](/troubleshoot/azure/virtual-machines/serial-console-windows) , VM 'ye bağlı bir klavye olmasına benzer şekilde, bir VM 'ye doğrudan erişim sağlar.

* Azure sanal makinelerinde komutları çalıştırın.
* Azure portal makineye metin tabanlı bir konsol kullanılarak çalıştırılabilir.
* Yerel bir kullanıcı hesabı ile makinede oturum açın.
* Makinenin ağı veya işletim sistemi durumu ne olursa olsun, sanal makineye erişim gerektiğinde faydalıdır.

## <a name="next-steps"></a>Sonraki adımlar

Sanal makinelerinizdeki betikleri ve komutları çalıştırmak için kullanılabilen farklı özellikler hakkında daha fazla bilgi edinin.

* [Özel Betik Uzantısı](../extensions/custom-script-windows.md)
* [Komutu Çalıştır](run-command.md)
* [Karma Runbook Çalışanı](../../automation/automation-hybrid-runbook-worker.md)
* [Seri konsol](/troubleshoot/azure/virtual-machines/serial-console-windows)