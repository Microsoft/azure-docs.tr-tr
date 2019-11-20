---
title: Windows 7 sanal makineleri Windows sanal masaüstü-Azure
description: Windows Sanal Masaüstü ortamındaki Windows 7 sanal makineleri (VM 'Ler) için sorunları çözme.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: helohr
ms.openlocfilehash: ee641a694371ffd3fbc691a05edac707e37e959d
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607335"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>Windows Sanal Masaüstü’nde Windows 7 sanal makinesiyle ilgili sorunları giderme

Windows sanal masaüstü oturumu ana bilgisayarı sanal makinelerini (VM 'Ler) yapılandırırken karşılaştığınız sorunları gidermek için bu makaleyi kullanın.

## <a name="known-issues"></a>Bilinen sorunlar

Windows sanal masaüstleri üzerinde Windows 7 aşağıdaki özellikleri desteklemez:

- Sanallaştırılmış uygulamalar (RemoteApps)
- Saat dilimi yönlendirmesi
- Otomatik DPı ölçeklendirme

Windows sanal masaüstü, Windows 7 için yalnızca tam masaüstlerini sanallaştırtırabilir.

Otomatik DPı ölçeklendirme desteklenirken, uzak masaüstü istemcisinde simgeye sağ tıklayıp **Çözümleme**' yi seçerek sanal makinenizde çözümü el ile değiştirebilirsiniz.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Hata: Uzak Masaüstü kullanıcı grubuna erişilemiyor

Windows sanal masaüstü, uzak masaüstü kullanıcı grubunda sizi veya kullanıcılarınızın kimlik bilgilerini bulamazsa, aşağıdaki hata iletilerinden birini görebilirsiniz:

- "Bu Kullanıcı, uzak masaüstü Kullanıcı grubunun bir üyesi değil"
- "Uzak Masaüstü Hizmetleri aracılığıyla oturum açmak için izin verilmelidir"

Bu hatayı onarmak için kullanıcıyı uzak masaüstü kullanıcı grubuna ekleyin:

1. Azure portalı açın.
2. Hata iletisini gördüğünüz sanal makineyi seçin.
3. **Çalıştır komutunu**seçin.
4. `<username>`, eklemek istediğiniz kullanıcının adı ile değiştirilmiş olan aşağıdaki komutu çalıştırın:
   
   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```