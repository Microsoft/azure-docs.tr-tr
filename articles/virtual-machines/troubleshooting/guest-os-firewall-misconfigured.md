---
title: Azure VM konuk işletim sistemi güvenlik duvarı yanlış yapılandırılmış. | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 8f04d943e1db49beed13c183fbd06e401546fc03
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71153888"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>Azure VM konuk işletim sistemi güvenlik duvarı yanlış yapılandırılmış

Bu makalede, yanlış yapılandırılmış konuk işletim sistemi güvenlik duvarı Azure sanal makinesinde nasıl tanıtır.

## <a name="symptoms"></a>Belirtiler

1.  VM tam olarak yüklendiğinden, sanal makine (VM) Karşılama ekranı gösterilir.

2.  Konuk işletim sisteminin nasıl yapılandırıldığına bağlı olarak olabilir VM ulaşmasını bazı veya hiçbir ağ trafiği.

## <a name="cause"></a>Nedeni

Konuk sistemi Güvenlik Duvarı'nın hatalı yapılandırılması bazı veya tüm VM ağ trafiği türlerinin engelleyebilirsiniz.

## <a name="solution"></a>Çözüm

Bu adımları gerçekleştirmeden önce sistem diski etkilenen sanal makinenin anlık görüntüsünü yedekleyin. Daha fazla bilgi için [bir diskin anlık görüntüsünü alma](../windows/snapshot-copy-managed-disk.md).

Bu sorunu gidermek için seri konsolu veya [çevrimdışı VM'yi onarın](troubleshoot-rdp-internal-error.md#repair-the-vm-offline) sanal Makinenin sistem diskini bir kurtarma sanal Makinesine ekleyerek.

## <a name="online-mitigations"></a>Çevrimiçi bir risk azaltma işlemleri

Bağlanma [seri konsolu ve bir PowerShell örneği açın](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Seri konsol VM üzerinde etkin değilse, "Onarım VM çevrimdışı" bölümüne aşağıdaki Azure makalesini gidin:

 [Uzak Masaüstü aracılığıyla Azure VM'ye bağlanmaya çalışırken bir iç hata oluşur.](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)

Aşağıdaki kuralları ya da VM (RDP) üzerinden erişim sağlamak veya sorun giderme daha kolay bir deneyim sağlamak üzere düzenlenebilir:

*   Uzak Masaüstü (TCP-ın): Bu, Azure 'da RDP 'ye izin vererek VM 'ye birincil erişim sağlayan standart bir kuraldır.

*   Windows Uzaktan Yönetimi (HTTP-ın): Bu kural, PowerShell 'i kullanarak VM 'ye bağlanmanızı sağlar. Azure 'Da bu tür bir erişim, uzaktan betik oluşturma ve sorun giderme konusunda komut dosyası kullanımını kullanmanıza imkan sağlar.

*   Dosya ve yazıcı paylaşımı (SMB-gelen): Bu kural, sorun giderme seçeneği olarak ağ paylaşımının erişimine izin vermez.

*   Dosya ve yazıcı paylaşımı (yankı Isteği-Icmpv4-gelen): Bu kural, VM 'ye ping yapmanızı sağlar.

Seri konsol erişimi örnekte, güvenlik duvarı kuralı geçerli durumunu sorgulayabilirsiniz.

*   Bir parametre görünen ad'ı kullanarak sorgu:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(DisplayName.*<FIREWALL RULE NAME>)" -context 9,4 | more
    ```

*   Yerel uygulama tarafından kullanılan bağlantı noktasını kullanarak sorgu:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<APPLICATION PORT>)" -context 9,4 | more
    ```

*   Uygulamanın kullandığı yerel IP adresi kullanarak sorgu:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalIP.*<CUSTOM IP>)" -context 9,4 | more
    ```

*   Kuralı devre dışı olduğunu görürseniz, aşağıdaki komutu çalıştırarak etkinleştirebilirsiniz:

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   Sorun giderme için güvenlik duvarı profili kapatabilirsiniz:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Güvenlik Duvarı doğru şekilde ayarlanması bunu yaparsanız, sorun giderme tamamladıktan sonra Güvenlik Duvarı'nı yeniden etkinleştirin.

    > [!Note]
    > Bu değişikliği uygulamak için VM'yi yeniden başlatma gerekmez.

*   VM RDP aracılığıyla bağlanmak bir kez daha deneyin.

### <a name="offline-mitigations"></a>Çevrimdışı bir risk azaltma işlemleri

1.  Güvenlik duvarı kurallarını etkinleştirmek veya devre dışı bırakmak için [bir Azure VM Konuk işletim sistemindeki güvenlik duvarı kuralını etkinleştirme veya devre dışı bırakma](enable-disable-firewall-rule-guest-os.md)bölümüne bakın.

2.  İçinde olup olmadığını denetleyin [konuk işletim sistemi güvenlik duvarı engelleme gelen trafiği senaryo](guest-os-firewall-blocking-inbound-traffic.md).

3.  Erişiminizi güvenlik duvarı olup engelliyor hakkında şüpheli hala açıksa, başvurmak [konuk işletim sistemi güvenlik duvarı Azure VM'de devre dışı](disable-guest-os-firewall-windows.md)ve ardından Konuk sistemi güvenlik duvarı doğru kurallarını kullanarak yeniden etkinleştirin.

