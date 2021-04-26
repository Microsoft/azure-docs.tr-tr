---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4732abacc80a24d268f0e5cb7d100079e5a8d90b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105104963"
---
Sanal ağınıza dağıtılan bir VM 'ye bağlanmak için bir Uzak Masaüstü Bağlantısı oluşturun. Sanal makinenize bağlanabildiğinizi doğrulamak için en iyi yol, kendi bilgisayar adı yerine özel IP adresiyle bağlantı kurmak olur. Bu şekilde, ad çözümlemenin doğru yapılandırılıp yapılandırılmadığını değil, bağlanıp bağlanamadığınızı görmek için test edersiniz. 

1. Sanal makinenizin özel IP adresini bulun. Bir VM 'nin özel IP adresini bulmak için, Azure portal VM 'nin özelliklerini görüntüleyin veya PowerShell kullanın.
2. Noktadan siteye VPN bağlantısıyla sanal ağınıza bağlı olduğunuzdan emin olun. 
3. Uzak Masaüstü Bağlantısı açmak için, görev çubuğundaki arama kutusuna *RDP* veya *Uzak Masaüstü bağlantısı* girin ve ardından **Uzak Masaüstü bağlantısı**' yı seçin. Ayrıca, PowerShell 'de **mstsc** komutunu kullanarak da açabilirsiniz. 
3. **Uzak Masaüstü bağlantısı**' de, VM 'nın özel IP adresini girin. Gerekirse, ek ayarları ayarlamak için **seçenekleri göster** ' i seçin, sonra bağlanın.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>VM ile RDP bağlantısı sorunlarını giderme

VPN bağlantınız üzerinden bir sanal makineye bağlanırken sorun yaşıyorsanız, kontrol yapabileceğiniz birkaç nokta vardır. 

- VPN bağlantınızın başarılı olduğunu doğrulayın.
- VM için özel IP adresine bağlandığınızdan emin olun.
- Bağlanmakta olduğunuz bilgisayardaki Ethernet bağdaştırıcısına atanmış IPv4 adresini denetlemek için **ipconfig** yazın. IP adresi, bağlanmakta olduğunuz VNet 'in adres aralığı içinde veya VPNClientAddressPool adres aralığında olduğunda çakışan bir adres alanı oluşur. Adres alanınız bu şekilde çakıştığında, ağ trafiği Azure’a ulaşmaz ve yerel ağda kalır.
- VM 'ye özel IP adresini kullanarak bağlanabildiğinizi, ancak bilgisayar adını değil, DNS 'i doğru yapılandırdığınızdan emin olun. VM’ler için ad çözümlemesinin nasıl çalıştığı hakkında daha fazla bilgi için bkz. [VM'ler için Ad Çözümlemesi](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
- Sanal ağın DNS sunucusu IP adreslerini belirttikten sonra VPN istemcisi yapılandırma paketinin oluşturulduğunu doğrulayın. DNS sunucusu IP adreslerini güncelleştirirseniz yeni bir VPN istemcisi yapılandırma paketi oluşturup yükleyebilirsiniz.

Daha fazla sorun giderme bilgisi için bkz. [VM ile Uzak Masaüstü bağlantılarında sorun giderme](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection).