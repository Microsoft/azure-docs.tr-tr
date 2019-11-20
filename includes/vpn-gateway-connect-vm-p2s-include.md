---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 35814de74fa03f9969cdd48882a5f672cfe306a1
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "67188232"
---
Sanal ağınıza dağıtılmış VM’ye, sanal makinenizle bir Uzak Masaüstü Bağlantısı oluşturarak bağlanabilirsiniz. Sanal makinenize bağlanabildiğinizi doğrulamanın en iyi yolu, bilgisayar yerine özel IP adresini kullanarak bağlantı kurmaktır. Bu yolla, ad çözünürlüğünün düzgün şekilde yapılandırılıp yapılandırılmadığını değil, bağlantı kurup kuramadığınızı test edersiniz.

1. Özel IP adresini bulun. Bir VM’nin özel IP adresini, Azure portalında VM’nin özelliklerine bakarak veya PowerShell kullanarak bulabilirsiniz.

   - Azure portalı - Sanal makinenizi Azure portalında bulun. VM’nin özelliklerini görüntüleyin. Özel IP adresi listelenir.

   - PowerShell - Örneği kullanarak kaynak gruplarınızdaki VM’lerin ve özel IP adreslerinin listesini görüntüleyin. Bu örneği kullanmadan önce değiştirmeniz gerekmez.

     ```powershell
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

2. Sanal ağınıza Noktadan Siteye VPN bağlantısı kullanarak bağlandığınızı doğrulayın.
3. Görev çubuğundaki arama kutusuna "RDP" veya "Uzak Masaüstü Bağlantısı" yazıp Uzak Masaüstü Bağlantısı’nı seçerek **Uzak Masaüstü Bağlantısı**’nı açın. Ayrıca PowerShell'de 'mstsc' komutunu kullanarak Uzak Masaüstü Bağlantısı’nı açabilirsiniz. 
4. Uzak Masaüstü Bağlantısı'nda VM’nin özel IP adresini girin. Diğer ayarları düzenlemek için "Seçenekleri Göster" öğesine tıklayabilir, ardından bağlanabilirsiniz.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>VM ile RDP bağlantısı sorunlarını giderme

VPN bağlantınız üzerinden bir sanal makineye bağlanmakta sorun yaşıyorsanız aşağıdaki kontrolleri yapın:

- VPN bağlantınızın başarılı olduğunu doğrulayın.
- VM’nin özel IP adresine bağlandığınızı doğrulayın.
- 'ipconfig' seçeneğini kullanarak bağlantıyı kurduğunuz bilgisayardaki Ethernet bağdaştırıcısına atanmış IPv4 adresini denetleyin. IP adresi bağlanacağınız sanal ağın adres aralığında veya VPNClientAddressPool adres aralığında ise, bu durum çakışan bir adres alanı olarak adlandırılır. Adres alanınız bu şekilde çakıştığında, ağ trafiği Azure’a ulaşmaz ve yerel ağda kalır.
- Bilgisayar adı yerine özel IP adresini kullanarak VM ile bağlantı kurabiliyorsanız, DNS’i düzgün yapılandırdığınızdan emin olun. VM’ler için ad çözümlemesinin nasıl çalıştığı hakkında daha fazla bilgi için bkz. [VM'ler için Ad Çözümlemesi](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
- Sanal ağ için DNS sunucusu IP adresleri belirtildikten sonra VPN istemci yapılandırma paketinin oluşturulduğunu doğrulayın. DNS sunucusu IP adreslerini güncelleştirdiyseniz, yeni bir VPN istemci yapılandırma paketi oluşturup yükleyin.
- RDP bağlantıları hakkında daha fazla bilgi için bkz. [Bir VM ile Uzak Masaüstü bağlantılarında sorun giderme](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).