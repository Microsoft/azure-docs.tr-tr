---
title: Azure sanal makine oluşturulduktan sonra WinRM 'yi yapılandırma | Azure Marketi
description: Azure 'da barındırılan bir sanal makine oluşturulduktan sonra Windows Uzaktan Yönetimi (WinRM) öğesinin nasıl yapılandırılacağını açıklar.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: pabutler
ms.openlocfilehash: ae5a55c6d640852cbd873bc6b36e502b5fe17165
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73817948"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>Sanal makine oluşturulduktan sonra WinRM 'yi yapılandırma

Bu makalede, HTTPS üzerinden WinRM 'yi etkinleştirmek için mevcut bir Azure barındırılan sanal makinenin (VM) nasıl yapılandırılacağı açıklanmaktadır.  Bu yapılandırma yalnızca Windows tabanlı VM 'Ler için geçerlidir ve aşağıdaki iki adımlı işlemi gerektirir:

1. HTTPS üzerinden WinRM protokolü için bağlantı noktası trafiğini etkinleştirin.  Bu ayarı, Azure portal VM 'niz için yapılandıracaksınız.
2. Sağlanan PowerShell betiklerini çalıştırarak, sanal makineyi WinRM 'yi etkinleştirecek şekilde yapılandırın.


## <a name="enabling-port-traffic"></a>Bağlantı noktası trafiğini etkinleştirme

HTTPS üzerinden WinRM protokolü, Azure Marketi 'nde sunulan önceden yapılandırılmış Windows VM 'lerde varsayılan olarak etkinleştirilmemiş 5896 numaralı bağlantı noktasını kullanır. Bu protokolü etkinleştirmek için aşağıdaki adımları kullanarak ağ güvenlik grubuna (NSG) [Azure Portal](https://portal.azure.com)yeni bir kural ekleyin.  NSG 'ler hakkında daha fazla bilgi için bkz. [güvenlik grupları](https://docs.microsoft.com/azure/virtual-network/security-overview).

1.    <*VM-adı*>   **> ayarları/ağ**> dikey pencere **sanal makinelerine** gidin.
2.  Özelliklerini göstermek için NSG adına tıklayın (Bu örnekte, **testvm11002**):

    ![Ağ güvenlik grubu özellikleri](./media/nsg-properties.png)
 
3. **Ayarlar**altında, bu dikey pencereyi göstermek için **gelen güvenlik kuralları** ' nı seçin.
4. 5986 numaralı TCP bağlantı noktası için `WinRM_HTTPS` adlı yeni bir kural oluşturmak için **+ Ekle** ' ye tıklayın.

    ![Gelen ağ güvenlik kuralı ekle](./media/nsg-new-rule.png)

5. Değer sağlamayı tamamladığınızda **Tamam** ' a tıklayın.  Gelen güvenlik kuralları listesi aşağıdaki yeni girdileri içermelidir.

    ![Gelen ağ güvenlik kurallarının listelenmesi](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>WinRM 'yi etkinleştirmek için VM 'yi yapılandırma 

Windows sanal makinenizde Windows Uzaktan Yönetimi özelliğini etkinleştirmek ve yapılandırmak için aşağıdaki adımları kullanın.   

1. Azure 'da barındırılan VM 'niz ile bir Uzak Masaüstü bağlantısı kurun.  Daha fazla bilgi için bkz. [Windows çalıştıran bir Azure sanal makinesinde bağlanma ve oturum açma](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).  Geri kalan adımlar VM 'niz üzerinde çalıştırılır.
2. Aşağıdaki dosyaları indirin ve sanal makinenizde bir klasöre kaydedin:
    - [ConfigureWinRM. ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [MakeCert. exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf. cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. Yükseltilmiş ayrıcalıklarla **PowerShell konsolunu** açın (**yönetici olarak çalıştır**). 
4. Gerekli parametreyi sağlayarak aşağıdaki komutu çalıştırın: VM 'niz için tam etki alanı adı (FQDN): <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![PowerShell yapılandırma betiği 1](./media/powershell-file1.png)

    Bu betik, aynı klasörde bulunan diğer iki dosyanın üzerine bağlıdır.


## <a name="next-steps"></a>Sonraki adımlar

WinRM 'yi yapılandırdıktan sonra, [sanal makinenizin kendisini oluşturan VHD 'lerinden dağıtmaya](./cpp-deploy-vm-vhd.md)hazırlanın.
