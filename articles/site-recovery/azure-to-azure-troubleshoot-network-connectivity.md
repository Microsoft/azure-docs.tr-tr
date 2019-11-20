---
title: Azure Site Recovery ile Azure ile Azure olağanüstü durum kurtarma ile ilgili bağlantı sorunlarını giderme
description: Olağanüstü durum kurtarma için Azure sanal makinelerini çoğalttığınızda oluşan hata ve sorunları giderme
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/05/2019
ms.author: asgang
ms.openlocfilehash: 4995a4086c471a06fe859febfd2d1af7fbb22a76
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622445"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Azure-Azure VM ağ bağlantısı sorunlarını giderme

Bu makalede, Azure sanal makinelerini bir bölgeden başka bir bölgeye çoğalttığınızda ve kurtardığınızda ağ bağlantısıyla ilgili yaygın sorunlar açıklanmaktadır. Ağ gereksinimleri hakkında daha fazla bilgi için bkz. [Azure VM 'leri çoğaltmaya yönelik bağlantı gereksinimleri](azure-to-azure-about-networking.md).

Site Recovery çoğaltmanın çalışması için, VM 'den belirli URL 'Lere veya IP aralıklarına giden bağlantı gerekir. VM 'niz bir güvenlik duvarının arkasındaysa veya giden bağlantıyı denetlemek için ağ güvenlik grubu (NSG) kuralları kullanıyorsa, bu sorunlardan birini görebilirsiniz.

**URL** | **Ayrıntılar**  
--- | ---
*.blob.core.windows.net | Verilerin, VM 'den kaynak bölgedeki önbellek depolama hesabına yazılabilmeleri için gereklidir. Sanal makinelerinize yönelik tüm önbellek depolama hesaplarını biliyorsanız, *. blob.core.windows.net yerine belirli depolama hesabı URL 'Lerini (örneğin, cache1.blob.core.windows.net ve cache2.blob.core.windows.net) listeleme izni verebilirsiniz.
login.microsoftonline.com | Site Recovery hizmeti URL 'Lerinde yetkilendirme ve kimlik doğrulaması için gereklidir.
*.hypervrecoverymanager.windowsazure.com | Site Recovery hizmeti iletişiminin sanal makineden gerçekleşebilmesi için gereklidir. Güvenlik duvarı proxy 'si, IP 'Leri destekliyorsa, karşılık gelen ' Site Recovery IP ' kullanabilirsiniz.
*.servicebus.windows.net | Site Recovery izleme ve tanılama verilerinin VM 'den yazılabilmesini sağlamak için gereklidir. Güvenlik duvarı proxy 'si, IP 'Leri destekliyorsa, karşılık gelen ' Site Recovery Izleme IP ' kullanabilirsiniz.

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 'Ler veya IP aralıkları için giden bağlantı (hata kodu 151037 veya 151072)

## <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Sorun 1: Azure sanal makinesi Site Recovery kaydedilemedi (151195) </br>
- **Olası neden** </br>
  - DNS çözümleme hatası nedeniyle Site Recovery uç noktalara bağlantı sağlanamadı.
  - Sanal makine yükünü devretmenize rağmen DNS sunucusuna DR bölgesinden erişilemediğinde yeniden koruma sırasında bu durum daha sık görülür.

- **Çözünürlüğüne**
   - Özel DNS kullanıyorsanız, olağanüstü durum kurtarma bölgesinden DNS sunucusuna erişilebildiğinden emin olun. Özel bir DNS olup olmadığınızı denetlemek için VM > olağanüstü durum kurtarma ağı > DNS sunucuları ' na gidin. DNS sunucusuna sanal makineden erişmeyi deneyin. Erişilebilir değilse, DNS sunucusu üzerinden yük devrederden veya DR ağı ile DNS arasında site satırı oluşturarak erişilebilir hale getirin.

    ![com-hata](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


## <a name="issue-2-site-recovery-configuration-failed-151196"></a>Sorun 2: Site Recovery yapılandırma başarısız oldu (151196)

> [!NOTE]
> Sanal makineler **Standart** iç yük dengeleyicinin arkasındaysa, varsayılan olarak O365 IP 'lerine (yani, Login.microsoftonline.com) erişemez. **Temel** iç yük dengeleyici türüne değiştirin veya [makalede](https://aka.ms/lboutboundrulescli)belirtilen şekilde giden erişim oluşturun.

- **Olası neden** </br>
  - Office 365 kimlik doğrulaması ve kimlik ıP4 uç noktalarına bağlantı sağlanamadı.

- **Çözünürlüğüne**
  - Kimlik doğrulaması için Office 365 IP aralıklarına gereken Azure Site Recovery erişimi.
    VM 'deki giden ağ bağlantısını denetlemek için Azure ağ güvenlik grubu (NSG) kuralları/güvenlik duvarı proxy 'si kullanıyorsanız O365 ıpranges iletişime izin verildiğinden emin olun. Azure AD 'ye karşılık gelen tüm IP adreslerine erişime izin vermek için [Azure Active Directory (Azure AD) hizmet etiketi](../virtual-network/security-overview.md#service-tags) tabanlı NSG kuralı oluşturun
      - Daha sonra Azure AD 'ye yeni adresler eklenirse, yeni NSG kuralları oluşturmanız gerekir.

### <a name="example-nsg-configuration"></a>Örnek NSG yapılandırması

Bu örnek, bir VM 'nin yinelenmesi için NSG kurallarının nasıl yapılandırılacağını gösterir.

- Giden bağlantıyı denetlemek için NSG kuralları kullanıyorsanız, tüm gerekli IP adresi aralıkları için bağlantı noktası: 443 ' e Izin ver (HTTPS giden) kuralları ' nı kullanın.
- Örnek, VM kaynak konumunun "Doğu ABD" ve hedef konumun "Orta ABD" olduğunu varsayar.

### <a name="nsg-rules---east-us"></a>NSG kuralları-Doğu ABD

1. Aşağıdaki ekran görüntüsünde gösterildiği gibi NSG 'de "Storage. EastUS" için giden bir HTTPS (443) güvenlik kuralı oluşturun.

      ![depolama etiketi](./media/azure-to-azure-about-networking/storage-tag.png)

2. Aşağıdaki ekran görüntüsünde gösterildiği gibi NSG 'de "AzureActiveDirectory" için giden HTTPS (443) güvenlik kuralı oluşturun.

      ![aad etiketi](./media/azure-to-azure-about-networking/aad-tag.png)

3. Hedef konuma karşılık gelen Site Recovery IP 'Leri için giden HTTPS (443) kuralları oluşturun:

   **Konum** | **Site Recovery IP adresi** |  **Site Recovery izleme IP adresi**
    --- | --- | ---
   Orta ABD | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>NSG kuralları-Orta ABD

Bu kurallar, çoğaltmanın hedef bölgeden kaynak bölgeye yük devretme sonrası etkinleştirilebilmesi için gereklidir:

1. NSG 'de "Storage. Merkezileştirus" için giden bir HTTPS (443) güvenlik kuralı oluşturun.

2. NSG 'de "AzureActiveDirectory" için giden HTTPS (443) güvenlik kuralı oluşturun.

3. Kaynak konuma karşılık gelen Site Recovery IP 'Leri için giden HTTPS (443) kuralları oluşturun:

   **Konum** | **Site Recovery IP adresi** |  **Site Recovery izleme IP adresi**
    --- | --- | ---
   Orta ABD | 13.82.88.226 | 104.45.147.24
## <a name="issue-3-site-recovery-configuration-failed-151197"></a>Sorun 3: Site Recovery yapılandırma başarısız oldu (151197)
- **Olası neden** </br>
  - Hizmet uç noktalarına Azure Site Recovery bağlantı sağlanamadı.

- **Çözünürlüğüne**
  - Bölgeye göre [SITE Recovery IP aralıklarına](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) gereken Azure Site Recovery erişimi. Gerekli IP aralıklarının sanal makineden erişilebilir olduğundan emin olun.


## <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Sorun 4: ağ trafiği şirket içi proxy sunucusu üzerinden geçtiğinde A2A çoğaltma başarısız oldu (151072)
- **Olası neden** </br>
  - Özel ara sunucu ayarları geçersiz ve Azure Site Recovery Mobility hizmeti Aracısı IE 'den proxy ayarlarını otomatik olarak algılamadı


- **Çözünürlüğüne**
  1. Mobility hizmeti Aracısı, Linux üzerinde Windows ve/etc/Environment üzerinde IE 'deki proxy ayarlarını algılar.
  2. Proxy 'yi yalnızca Azure Site Recovery Mobility hizmeti için ayarlamayı tercih ediyorsanız, şu adreste bulunan ProxyInfo. conf dosyasında proxy ayrıntılarını sağlayabilirsiniz:</br>
     - ***Linux*** üzerinde ``/usr/local/InMage/config/``
     - ***Windows*** üzerinde ``C:\ProgramData\Microsoft Azure Site Recovery\Config``
  3. ProxyInfo.conf dosyasında ara sunucu ayarları şu INI biçiminde bulunmalıdır.</br>
                *Proxy*</br>
                *Adres =http://1.2.3.4*</br>
                *Bağlantı noktası = 567*</br>
  4. Azure Site Recovery Mobility hizmeti Aracısı yalnızca ***kimliği doğrulanmamış proxy 'leri***destekler.

### <a name="fix-the-problem"></a>Sorunu çözme
[Gerekli URL 'lere](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) veya [gerekli IP aralıklarına](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)izin vermek için [Ağ Kılavuzu belgesindeki](site-recovery-azure-to-azure-networking-guidance.md)adımları izleyin.


## <a name="next-steps"></a>Sonraki adımlar
[Azure sanal makinelerini çoğaltma](site-recovery-replicate-azure-to-azure.md)
