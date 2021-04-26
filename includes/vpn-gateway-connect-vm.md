---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/23/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5d631d8492ed1869bdc244e2cc90595183892822
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105105269"
---
Sanal ağınıza dağıtılmış VM’ye, sanal makinenizle bir Uzak Masaüstü Bağlantısı oluşturarak bağlanabilirsiniz. Sanal makinenize bağlanabildiğinizi doğrulamanın en iyi yolu, bilgisayar yerine özel IP adresini kullanarak bağlantı kurmaktır. Bu yolla, ad çözünürlüğünün düzgün şekilde yapılandırılıp yapılandırılmadığını değil, bağlantı kurup kuramadığınızı test edersiniz.

1. Özel IP adresini bulun. Bir VM’nin özel IP adresini, Azure portalında VM’nin özelliklerine bakarak veya PowerShell kullanarak bulabilirsiniz.

   * Azure portalı - Sanal makinenizi Azure portalında bulun. VM’nin özelliklerini görüntüleyin. Özel IP adresi listelenir.

   * PowerShell - Örneği kullanarak kaynak gruplarınızdaki VM’lerin ve özel IP adreslerinin listesini görüntüleyin. Bu örneği kullanmadan önce değiştirmeniz gerekmez.

     ```azurepowershell-interactive
     $VMs = Get-AzVM
     $Nics = Get-AzNetworkInterface | Where VirtualMachine -ne $null

     foreach($Nic in $Nics)
     {
      $VM = $VMs | Where-Object -Property Id -eq $Nic.VirtualMachine.Id
      $Prv = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAddress
      $Alloc = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($VM.Name): $Prv,$Alloc"
     }
     ```

1. Sanal ağınıza Noktadan Siteye VPN bağlantısı kullanarak bağlandığınızı doğrulayın.
1. Görev çubuğundaki arama kutusuna "RDP" veya "Uzak Masaüstü Bağlantısı" yazıp Uzak Masaüstü Bağlantısı’nı seçerek **Uzak Masaüstü Bağlantısı**’nı açın. Ayrıca PowerShell'de 'mstsc' komutunu kullanarak Uzak Masaüstü Bağlantısı’nı açabilirsiniz. 
1. Uzak Masaüstü Bağlantısı'nda VM’nin özel IP adresini girin. Diğer ayarları düzenlemek için "Seçenekleri Göster" öğesine tıklayabilir, ardından bağlanabilirsiniz.

**Bir bağlantının sorunlarını giderme**

VPN bağlantınız üzerinden bir sanal makineye bağlanmakta sorun yaşıyorsanız aşağıdaki kontrolleri yapın:

* VPN bağlantınızın başarılı olduğunu doğrulayın.

* VM’nin özel IP adresine bağlandığınızı doğrulayın.

* Bilgisayar adı yerine özel IP adresini kullanarak VM ile bağlantı kurabiliyorsanız, DNS’i düzgün yapılandırdığınızdan emin olun. VM’ler için ad çözümlemesinin nasıl çalıştığı hakkında daha fazla bilgi için bkz. [VM'ler için Ad Çözümlemesi](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

* RDP bağlantıları hakkında daha fazla bilgi için bkz. [Bir VM ile Uzak Masaüstü bağlantılarında sorun giderme](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection).