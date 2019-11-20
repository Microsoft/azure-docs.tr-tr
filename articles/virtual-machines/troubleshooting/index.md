---
layout: LandingPage
description: Sanal makine dağıtımlarında sorun gidermeyi öğrenin.
title: Azure Sanal Makineler Hakkında Sorun Giderme Belgeleri| Microsoft Docs
services: virtual-machines
author: genlin
manager: dcscontentpm
ms.assetid: ''
ms.service: virtual-machines
ms.tgt_pltfrm: na
ms.topic: landing-page
ms.date: 10/3/2018
ms.author: genli
ms.openlocfilehash: d7ceb3acb1d2e3d174f3b665ec6210d3ddac9970
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059161"
---
# <a name="troubleshooting-azure-virtual-machines"></a>Azure sanal makineleriyle ilgili sorunları giderme

## <a name="tools-for-troubleshooting"></a>Sorun giderme araçları

- [Seri Konsol](serial-console-windows.md)
- [Önyükleme tanılamaları](boot-diagnostics.md)
- [Windows VM: Sorun giderme için işletim sistemi diskini başka bir VM’ye ekleme](troubleshoot-recovery-disks-portal-windows.md)
- [Linux VM: Sorun giderme için işletim sistemi diskini başka bir VM’ye ekleme](troubleshoot-recovery-disks-portal-linux.md)

## <a name="cant-connect-to-the-vm"></a>Sanal makineye bağlanılamıyor

### <a name="windows"></a>Windows

**Ortak çözüm**

- [RDP’yi sıfırlama](reset-rdp.md)
- [RDP sorunlarını giderme](troubleshoot-rdp-connection.md)
- [RDP sorunlarını gidermeyle ilgili ayrıntılar](detailed-troubleshoot-rdp.md)

**RDP Hataları**

- [Lisans sunucusu yok](troubleshoot-rdp-no-license-server.md)
- [Dahili bir](Troubleshoot-rdp-internal-error.md)
- [kimlik doğrulaması hataları](troubleshoot-authentication-error-rdp-vm.md)
- [Belirli hatalarda sorun giderme](troubleshoot-specific-rdp-errors.md)

**Sanal makine önyükleme hataları**

* [BitLocker önyükleme hataları](troubleshoot-bitlocker-boot-error.md) 
* [Windows, önyükleme sırasında "dosya sistemi denetleniyor" iletisini gösterdi](troubleshoot-check-disk-boot-error.md)
* [Mavi ekran hataları](troubleshoot-common-blue-screen-error.md)
* [VM başlatma “Windows Hazırlanıyor” kısmında takıldı](troubleshoot-vm-boot-configure-update.md)
* [Mavi ekranda "KRİTİK HİZMET BAŞARISIZ OLDU" hatası](troubleshoot-critical-service-failed-boot-error.md)
* [Yeniden başlatma döngüsü sorunu](troubleshoot-reboot-loop.md)
* [VM, Windows güncelleştirmesi aşamasında takıldı](troubleshoot-stuck-updating-boot-error.md)
* [VM, güvenli modda önyüklendi](troubleshoot-rdp-safe-mode.md)

**Diğer**
- [Windows VM çevrimdışı için VM parolasını sıfırlama](reset-local-password-without-agent.md)
- [Yanlış yapılandırmadan sonra NIC’yi sıfırlama](reset-network-interface.md)

### <a name="linux"></a>Linux

- [SSH sorunlarını giderme](troubleshoot-ssh-connection.md)
- [SSH sorunlarını gidermeyle ilgili ayrıntılar](detailed-troubleshoot-ssh-connection.md)
- [Genel hata iletileri](error-messages.md)
- [Windows VM çevrimdışı için VM parolasını sıfırlama](reset-password.md)

## <a name="vm-deployment-issues"></a>VM dağıtım sorunları

- [Ayırma hataları](allocation-failure.md)
- Bir VM’yi yeniden dağıtma
    - [Linux](redeploy-to-new-node-linux.md)
    - [Windows](redeploy-to-new-node-windows.md)
- Dağıtımlarla ilgili sorunları giderme
    - [Linux](troubleshoot-deploy-vm-linux.md)
    - [Windows](troubleshoot-deploy-vm-windows.md)
- [Cihaz adları değiştirildi](troubleshoot-device-names-problems.md)
- [Windows VM aracısını çevrimdışı olarak yükleme](install-vm-agent-offline.md)
- [VM’yi yeniden başlatma veya yeniden boyutlandırma](restart-resize-error-troubleshooting.md)

## <a name="vm-performance-issue"></a>VM ile ilgili performans sorunu
- [VM’lerle ilgili performans sorunları](performance-diagnostics.md)
- Windows
    - [PerfInsights’ı kullanma](how-to-use-perfinsights.md)
    - [Performans tanılama uzantısı](performance-diagnostics-vm-extension.md)
- Linux
    - [PerfInsights’ı kullanma](how-to-use-perfinsights-linux.md)