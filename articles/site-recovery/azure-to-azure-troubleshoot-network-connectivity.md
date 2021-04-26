---
title: Azure Site Recovery ile Azure ile Azure olağanüstü durum kurtarma ile ilgili bağlantı sorunlarını giderme
description: Azure VM olağanüstü durum kurtarma 'da bağlantı sorunlarını giderme
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: 24ffce1528aa5c82fec9666fa0cb7b8717107f54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97652271"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Azure-Azure VM ağ bağlantısı sorunlarını giderme

Bu makalede, Azure sanal makinelerini (VM) bir bölgeden başka bir bölgeye çoğalttığınızda ve kurtardığınızda ağ bağlantısıyla ilgili yaygın sorunlar açıklanmaktadır. Ağ gereksinimleri hakkında daha fazla bilgi için bkz. [Azure VM 'leri çoğaltmaya yönelik bağlantı gereksinimleri](azure-to-azure-about-networking.md).

Site Recovery çoğaltmanın çalışması için, VM 'den belirli URL 'Lere veya IP aralıklarına giden bağlantı gerekir. VM 'niz bir güvenlik duvarının arkasındaysa veya giden bağlantıyı denetlemek için ağ güvenlik grubu (NSG) kuralları kullanıyorsa, bu sorunlardan birini görebilirsiniz.

| **Ad**                  | **Ticari**                               | **Kamu**                                 | **Açıklama** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Depolama                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Verilerin, VM 'den kaynak bölgedeki önbellek depolama hesabına yazılabilmeleri için gereklidir. Sanal makinelerinize yönelik tüm önbellek depolama hesaplarını biliyorsanız, belirli depolama hesabı URL 'Leri için bir izin verilenler listesi kullanabilirsiniz. Örneğin, `cache1.blob.core.windows.net` ve `cache2.blob.core.windows.net` yerine `*.blob.core.windows.net` . |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Site Recovery hizmeti URL 'Lerinde yetkilendirme ve kimlik doğrulaması için gereklidir. |
| Çoğaltma               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Site Recovery hizmeti iletişiminin sanal makineden gerçekleşebilmesi için gereklidir. Güvenlik Duvarı ara sunucunuz IP 'Leri destekliyorsa, karşılık gelen _SITE Recovery IP_ 'sini kullanabilirsiniz. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Site Recovery izleme ve tanılama verilerinin VM 'den yazılabilmesini sağlamak için gereklidir. Güvenlik duvarı proxy 'si, IP 'Leri destekliyorsa, karşılık gelen _Site Recovery Izleme IP_ 'sini kullanabilirsiniz. |

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 'Ler veya IP aralıkları için giden bağlantı (hata kodu 151037 veya 151072)

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>Sorun 1: Azure sanal makinesi Site Recovery kaydedilemedi (151195)

#### <a name="possible-cause"></a>Olası nedeni

Etki alanı adı sistemi (DNS) çözümleme hatası nedeniyle uç noktalara Site Recovery bağlantı oluşturulamıyor. Bu sorun, VM üzerinden yük devretmeye karşın DNS sunucusuna olağanüstü durum kurtarma (DR) bölgesinden ulaşılamadığında yeniden koruma sırasında daha yaygın bir sorundur.

#### <a name="resolution"></a>Çözüm

Özel DNS kullanıyorsanız, olağanüstü durum kurtarma bölgesinden DNS sunucusuna erişilebildiğinden emin olun.

VM 'nin özel bir DNS ayarı kullanıp kullanmadığını denetlemek için:

1. **Sanal makineleri** açın ve VM 'yi seçin.
1. VM 'Ler **ayarlarına** gidin ve **ağ**' ı seçin.
1. **Sanal ağ/alt ağ**' da, sanal ağın kaynak sayfasını açmak için bağlantıyı seçin.
1. **Ayarlar** ' a gidin ve **DNS sunucuları**' nı seçin.

DNS sunucusuna sanal makineden erişmeyi deneyin. DNS sunucusu erişilebilir değilse, DNS sunucusu üzerinden yük devrederden veya DR ağı ile DNS arasında site satırı oluşturarak erişilebilir hale getirin.

  :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-hata":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Sorun 2: Site Recovery yapılandırma başarısız oldu (151196)

> [!NOTE]
> VM 'Ler **Standart** bir iç yük dengeleyicinin arkasında ise, varsayılan olarak, gibi Microsoft 365 IP 'lerine erişemez `login.microsoftonline.com` . Bunu **temel** iç yük dengeleyici türüne değiştirin veya [Azure CLI kullanarak standart Load Balancer yük dengelemeyi ve giden kuralları yapılandırma](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard#create-outbound-rule-configuration)makalesinde belirtildiği şekilde giden erişim oluşturun.

#### <a name="possible-cause"></a>Olası nedeni

Microsoft 365 kimlik doğrulaması ve kimlik ıP4 uç noktalarına bir bağlantı kurulamazsa.

#### <a name="resolution"></a>Çözüm

- Azure Site Recovery, kimlik doğrulaması için Microsoft 365 IP aralıklarına erişim gerektirir.
- VM 'deki giden ağ bağlantısını denetlemek için Azure ağ güvenlik grubu (NSG) kuralları/güvenlik duvarı proxy kullanıyorsanız, Microsoft 365 IP aralıklarıyla iletişime izin verildiğinden emin olun. Azure AD 'ye karşılık gelen tüm IP adreslerine erişim sağlayan bir [Azure Active Directory (Azure AD) hizmet etiketi](../virtual-network/network-security-groups-overview.md#service-tags) tabanlı NSG kuralı oluşturun.
- Daha sonra Azure AD 'ye yeni adresler eklenirse, yeni NSG kuralları oluşturmanız gerekir.

### <a name="example-nsg-configuration"></a>Örnek NSG yapılandırması

Bu örnek, bir VM 'nin yinelenmesi için NSG kurallarının nasıl yapılandırılacağını gösterir.

- Giden bağlantıyı denetlemek için NSG kuralları kullanıyorsanız, tüm gerekli IP adresi aralıkları için bağlantı noktası 443 ' e **https giden kuralları ver** ' i kullanın.
- Örnek, VM kaynak konumunun **Doğu ABD** olduğunu ve hedef konumun **Orta ABD** olduğunu varsayar.

#### <a name="nsg-rules---east-us"></a>NSG kuralları-Doğu ABD

1. Aşağıdaki ekran görüntüsünde gösterildiği gibi NSG için bir HTTPS giden güvenlik kuralı oluşturun. Bu örnek, **hedef hizmet etiketini** kullanır: _Storage. EastUS_ ve **hedef bağlantı noktası aralıkları**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/storage-tag.png" alt-text="Ekran görüntüsü, depolama noktası Doğu U S için bir güvenlik kuralına giden güvenlik kuralı ekle bölmesini gösterir.":::

1. Aşağıdaki ekran görüntüsünde gösterildiği gibi NSG için bir HTTPS giden güvenlik kuralı oluşturun. Bu örnek, **hedef hizmet etiketini** kullanır: _AzureActiveDirectory_ ve **hedef bağlantı noktası aralıkları**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/aad-tag.png" alt-text="Ekran görüntüsünde Azure Active Directory için bir güvenlik kuralına giden güvenlik kuralı ekle bölmesi görüntülenir.":::

1. Yukarıdaki güvenlik kurallarına benzer şekilde, hedef konuma karşılık gelen NSG 'de "EventHub. Merkezileştirus" için giden HTTPS (443) güvenlik kuralı oluşturun. Bu, Site Recovery izlemeye erişim sağlar.
1. NSG 'de "Azuresterecovery" için giden bir HTTPS (443) güvenlik kuralı oluşturun. Bu, herhangi bir bölgedeki Site Recovery hizmetine erişim sağlar.

#### <a name="nsg-rules---central-us"></a>NSG kuralları-Orta ABD

Bu örnekte, çoğaltmanın hedef bölgeden kaynak bölgeye yük devretme sonrası etkinleştirilebilmesi için bu NSG kuralları gereklidir:

1. _Storage. merkezileştirme_ için https giden güvenlik kuralı oluşturun:

   - **Hedef hizmet etiketi**: _Storage. merkezde ABD_
   - **Hedef bağlantı noktası aralıkları**: _443_

1. _AzureActiveDirectory_ IÇIN bir https giden güvenlik kuralı oluşturun.

   - **Hedef hizmet etiketi**: _AzureActiveDirectory_
   - **Hedef bağlantı noktası aralıkları**: _443_

1. Yukarıdaki güvenlik kurallarına benzer şekilde, kaynak konuma karşılık gelen NSG 'de "EventHub. EastUS" için giden HTTPS (443) güvenlik kuralı oluşturun. Bu, Site Recovery izlemeye erişim sağlar.
1. NSG 'de "Azuresterecovery" için giden bir HTTPS (443) güvenlik kuralı oluşturun. Bu, herhangi bir bölgedeki Site Recovery hizmetine erişim sağlar.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Sorun 3: Site Recovery yapılandırma başarısız oldu (151197)

#### <a name="possible-cause"></a>Olası nedeni

Hizmet uç noktalarına Azure Site Recovery bir bağlantı kurulamazsa.

#### <a name="resolution"></a>Çözüm

Makinedeki giden ağ bağlantısını denetlemek için bir Azure ağ güvenlik grubu (NSG) kuralı/güvenlik duvarı proxy 'si kullanıyorsanız, izin verilmesi gereken birkaç hizmet etiketi vardır. [Daha fazla bilgi edinin](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags).

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>4. sorun: ağ trafiği şirket içi ara sunucu üzerinden geçtiğinde Azure 'dan Azure 'a çoğaltma başarısız oldu (151072)

#### <a name="possible-cause"></a>Olası nedeni

Özel ara sunucu ayarları geçersiz ve Azure Site Recovery Mobility hizmeti Aracısı, proxy ayarlarını Internet Explorer 'dan (IE) otomatik olarak algılamadım.

#### <a name="resolution"></a>Çözüm

1. Mobility hizmeti Aracısı Windows ve Linux üzerinde IE 'deki proxy ayarlarını algılar `/etc/environment` .
1. Proxy 'yi yalnızca Azure Site Recovery Mobility hizmeti için ayarlamayı tercih ediyorsanız, şu adreste bulunan _ProxyInfo. conf_ dosyasında proxy ayrıntılarını sağlayabilirsiniz:

   - **Linux**: `/usr/local/InMage/config/`
   - **Windows**: `C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _ProxyInfo. conf_ _dosyası aşağıdaki ını_ biçiminde proxy ayarlarına sahip olmalıdır:

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Azure Site Recovery Mobility Service Agent yalnızca **kimliği doğrulanmamış proxy 'leri** destekler.

### <a name="fix-the-problem"></a>Sorunu çözme

[Gerekli URL 'lere](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) veya [gerekli IP aralıklarına](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)izin vermek için [Ağ Kılavuzu belgesindeki](./azure-to-azure-about-networking.md)adımları izleyin.

## <a name="next-steps"></a>Sonraki adımlar

[Azure sanal makinelerini başka bir Azure bölgesine çoğaltma](azure-to-azure-how-to-enable-replication.md)