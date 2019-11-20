---
title: Azure Site Recovery ile Azure VM olağanüstü durum kurtarma 'da ağ iletişimi hakkında
description: Azure Site Recovery kullanarak Azure VM 'lerinin çoğaltılmasına yönelik ağa genel bir bakış sağlar.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/22/2019
ms.author: sutalasi
ms.openlocfilehash: 09cd814ade25be438a17b83fb73e74b89c14e22f
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954205"
---
# <a name="about-networking-in-azure-vm-disaster-recovery"></a>Azure VM olağanüstü durum kurtarma 'da ağ iletişimi hakkında



Bu makalede, Azure VM 'lerini bir bölgeden diğerine çoğaltırken ve kurtarırken, [Azure Site Recovery](site-recovery-overview.md)kullanarak Ağ Kılavuzu sağlanmaktadır.

## <a name="before-you-start"></a>Başlamadan önce

Site Recovery [Bu senaryo](azure-to-azure-architecture.md)için olağanüstü durum kurtarma nasıl sağladığını öğrenin.

## <a name="typical-network-infrastructure"></a>Tipik ağ altyapısı

Aşağıdaki diyagramda, Azure VM 'lerde çalışan uygulamalar için tipik bir Azure ortamı gösterilmektedir:

![müşteri-ortam](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Azure ExpressRoute veya şirket içi ağınızdan Azure 'a bir VPN bağlantısı kullanıyorsanız, ortam aşağıdaki gibidir:

![müşteri-ortam](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Genellikle, ağlar güvenlik duvarları ve ağ güvenlik grupları (NSG 'ler) kullanılarak korunur. Güvenlik duvarları, ağ bağlantısını denetlemek için URL veya IP tabanlı beyaz liste kullanır. NSG 'ler, ağ bağlantısını denetlemek için IP adresi aralıklarını kullanan kurallar sağlar.

>[!IMPORTANT]
> Ağ bağlantısını denetlemek için kimliği doğrulanmış bir proxy kullanmak Site Recovery tarafından desteklenmez ve çoğaltma etkinleştirilemez.


## <a name="outbound-connectivity-for-urls"></a>URL'ler için giden bağlantı

Giden bağlantıyı denetlemek için URL tabanlı bir güvenlik duvarı proxy 'si kullanıyorsanız, bu Site Recovery URL 'Lerine izin verin:


**URL** | **Ayrıntılar**  
--- | ---
*.blob.core.windows.net | Verilerin, VM 'den kaynak bölgedeki önbellek depolama hesabına yazılabilmeleri için gereklidir. Sanal makinelerinize yönelik tüm önbellek depolama hesaplarını biliyorsanız, *. blob.core.windows.net yerine belirli depolama hesabı URL 'Lerine (örn: cache1.blob.core.windows.net ve cache2.blob.core.windows.net) erişime izin verebilirsiniz
login.microsoftonline.com | Site Recovery hizmeti URL 'Lerinde yetkilendirme ve kimlik doğrulaması için gereklidir.
*.hypervrecoverymanager.windowsazure.com | Site Recovery hizmeti iletişiminin sanal makineden gerçekleşebilmesi için gereklidir. Güvenlik duvarı proxy 'si, IP 'Leri destekliyorsa, karşılık gelen ' Site Recovery IP ' kullanabilirsiniz.
*.servicebus.windows.net | Site Recovery izleme ve tanılama verilerinin VM 'den yazılabilmesini sağlamak için gereklidir. Güvenlik duvarı proxy 'si, IP 'Leri destekliyorsa, karşılık gelen ' Site Recovery Izleme IP ' kullanabilirsiniz.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>IP adresi aralıkları için giden bağlantı

Giden bağlantıyı denetlemek için IP tabanlı bir güvenlik duvarı ara sunucusu veya NSG kuralları kullanıyorsanız, bu IP aralıklarına izin verilmesi gerekir.

- Kaynak bölgedeki depolama hesaplarına karşılık gelen tüm IP adresi aralıkları
    - Kaynak bölge için bir [depolama hizmeti etiketi](../virtual-network/security-overview.md#service-tags) tabanlı NSG kuralı oluşturun.
    - Bu adreslere, verilerin VM 'den önbellek depolama hesabına yazılabilmeleri için izin verin.
- Oluşturma bir [Azure Active Directory (AAD) hizmet etiketi](../virtual-network/security-overview.md#service-tags) erişimi için AAD karşılık gelen tüm IP adreslerine izin vermek için NSG kural tabanlı
    - Azure Active Directory (AAD) gelecekte yeni adresler eklenir, yeni NSG kuralları oluşturmak gerekir.
- Hizmet uç noktası IP adreslerini Site Recovery-bir [XML dosyasında](https://aka.ms/site-recovery-public-ips) kullanılabilir ve hedef konumunuza göre değişir. 
- Gerekli NSG kurallarını bir test NSG üzerinde oluşturmanızı ve bir üretim NSG 'de kuralları oluşturmadan önce hiçbir sorun olmadığını doğrulamanızı öneririz.


Site Recovery IP adresi aralıkları aşağıdaki gibidir:

   **Hedef** | **Site Recovery IP** |  **Site Recovery izleme IP 'si**
   --- | --- | ---
   Doğu Asya | 52.175.17.132 | 13.94.47.61
   Güneydoğu Asya | 52.187.58.193 | 13.76.179.223
   Orta Hindistan | 52.172.187.37 | 104.211.98.185
   Güney Hindistan | 52.172.46.220 | 104.211.224.190
   Orta Kuzey ABD | 23.96.195.247 | 168.62.249.226
   Kuzey Avrupa | 40.69.212.238 | 52.169.18.8
   Batı Avrupa | 52.166.13.64 | 40.68.93.145
   Doğu ABD | 13.82.88.226 | 104.45.147.24
   Batı ABD | 40.83.179.48 | 104.40.26.199
   Orta Güney ABD | 13.84.148.14 | 104.210.146.250
   Orta ABD | 40.69.144.231 | 52.165.34.144
   Doğu ABD 2 | 52.184.158.163 | 40.79.44.59
   Japonya Doğu | 52.185.150.140 | 138.91.1.105
   Japonya Batı | 52.175.146.69 | 138.91.17.38
   Güney Brezilya | 191.234.185.172 | 23.97.97.36
   Avustralya Doğu | 104.210.113.114 | 191.239.64.144
   Avustralya Güneydoğu | 13.70.159.158 | 191.239.160.45
   Orta Kanada | 52.228.36.192 | 40.85.226.62
   Doğu Kanada | 52.229.125.98 | 40.86.225.142
   Batı Orta ABD | 52.161.20.168 | 13.78.149.209
   Batı ABD 2 | 52.183.45.166 | 13.66.228.204
   Birleşik Krallık Batı | 51.141.3.203 | 51.141.14.113
   Birleşik Krallık Güney | 51.140.43.158 | 51.140.189.52
   Birleşik Krallık Güney 2 | 13.87.37.4| 13.87.34.139
   Birleşik Krallık Kuzey | 51.142.209.167 | 13.87.102.68
   Kore Orta | 52.231.28.253 | 52.231.32.85
   Kore Güney | 52.231.198.185 | 52.231.200.144
   Fransa Orta | 52.143.138.106 | 52.143.136.55
   Fransa Güney | 52.136.139.227 |52.136.136.62
   Avustralya orta| 20.36.34.70 | 20.36.46.142
   Avustralya Orta 2| 20.36.69.62 | 20.36.74.130
   Güney Afrika Batı | 102.133.72.51 | 102.133.26.128
   Güney Afrika Kuzey | 102.133.160.44 | 102.133.154.128
   ABD Devleti Virginia | 52.227.178.114 | 23.97.0.197
   US Gov Iowa | 13.72.184.23 | 23.97.16.186
   ABD Devleti Arizona | 52.244.205.45 | 52.244.48.85
   ABD Devleti Texas | 52.238.119.218 | 52.238.116.60
   US DoD Doğu | 52.181.164.103 | 52.181.162.129
   US DoD Orta | 52.182.95.237 | 52.182.90.133
   Çin Kuzey | 40.125.202.254 | 42.159.4.151
   Çin Kuzey 2 | 40.73.35.193 | 40.73.33.230
   Çin Doğu | 42.159.205.45 | 42.159.132.40
   Çin Doğu 2 | 40.73.118.52| 40.73.100.125
   Almanya Kuzey| 51.116.208.58| 51.116.58.128
   Almanya Batı Orta | 51.116.156.176 | 51.116.154.192
   İsviçre Batı | 51.107.231.223| 51.107.154.128
   İsviçre Kuzey | 51.107.68.31| 51.107.58.128
   Norveç Doğu | 51.120.100.64| 51.120.98.128
   Norveç Batı | 51.120.220.65| 51.120.218.160

## <a name="example-nsg-configuration"></a>Örnek NSG yapılandırması

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
   Doğu ABD | 13.82.88.226 | 104.45.147.24


## <a name="network-virtual-appliance-configuration"></a>Ağ sanal gereç yapılandırması

VM 'lerden giden ağ trafiğini denetlemek için ağ sanal gereçlerini (NVA 'lar) kullanıyorsanız, tüm çoğaltma trafiği NVA üzerinden geçerse gereç azalmasıyla karşılaşabilirsiniz. Çoğaltma trafiğinin NVA 'ya gitmemesi için, sanal ağınızda "depolama" için bir ağ hizmeti uç noktası oluşturmanız önerilir.

### <a name="create-network-service-endpoint-for-storage"></a>Depolama için ağ hizmeti uç noktası oluşturma
Çoğaltma trafiğinin Azure sınırından çıkmadan, sanal ağınızda "depolama" için bir ağ hizmeti uç noktası oluşturabilirsiniz.

- Azure Sanal ağınızı seçin ve ' hizmet uç noktaları ' seçeneğine tıklayın

    ![depolama uç noktası](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- ' Ekle ' seçeneğine tıklayın ve ' hizmet uç noktaları Ekle ' sekmesi açılır
- ' Hizmet ' altında ' Microsoft. Storage ' ve ' alt ağlar ' alanı altında gerekli alt ağlar ' ı seçin ve ' Ekle ' seçeneğine tıklayın

>[!NOTE]
>ASR için kullanılan depolama hesaplarınıza sanal ağ erişimini kısıtlamayın. ' Tüm ağlar 'dan erişime izin vermeniz gerekir

### <a name="forced-tunneling"></a>Zorlamalı tünel oluşturma

[Özel bir rota](../virtual-network/virtual-networks-udr-overview.md#custom-routes) ile 0.0.0.0/0 adres ön eki için Azure 'un varsayılan sistem yolunu geçersiz KıLABILIR ve VM trafiğini şirket içi ağ sanal gerecine (NVA) yönlendirebilirsiniz, ancak bu yapılandırma Site Recovery çoğaltma için önerilmez. Özel yollar kullanıyorsanız, çoğaltma trafiğinin Azure sınırından ayrılmaması için sanal ağınızda "depolama" için [bir sanal ağ hizmet uç noktası oluşturmanız](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) gerekir.

## <a name="next-steps"></a>Sonraki adımlar
- [Azure sanal makinelerini çoğaltarak](site-recovery-azure-to-azure.md)iş yüklerinizi korumaya başlayın.
- Azure sanal makine yük devretmesi için [IP adresi saklama](site-recovery-retain-ip-azure-vm-failover.md) hakkında daha fazla bilgi edinin.
- [ExpressRoute Ile Azure sanal makinelerinin](azure-vm-disaster-recovery-with-expressroute.md)olağanüstü durum kurtarması hakkında daha fazla bilgi edinin.
