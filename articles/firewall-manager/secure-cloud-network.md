---
title: "Öğretici: Azure portal kullanarak bulut ağınızı güvenli hale getirmek için Azure Güvenlik Duvarı Yöneticisi önizlemesi 'ni kullanın"
description: Bu öğreticide, Azure portal kullanarak bulut ağınızın güvenliğini Azure Güvenlik Duvarı Yöneticisi ile nasıl sağlayacağınızı öğreneceksiniz.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 10/27/2019
ms.author: victorh
ms.openlocfilehash: d2ebfd6003c0bc2b47636be1e38f47e554cc6988
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501914"
---
# <a name="tutorial-secure-your-cloud-network-with-azure-firewall-manager-preview-using-the-azure-portal"></a>Öğretici: Azure portal kullanarak bulut ağınızı Azure Güvenlik Duvarı Yöneticisi önizleme ile koruyun

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure Güvenlik Duvarı Yöneticisi önizlemesi 'ni kullanarak, özel IP adreslerine, Azure PaaS 'ye ve Internet 'e yönelik bulut ağ trafiğinizi güvenli hale getirmek için güvenli hub 'lar oluşturabilirsiniz. Güvenlik duvarındaki trafik yönlendirme otomatikleştirilmiştir, bu nedenle Kullanıcı tanımlı yollar (UDRs) oluşturmanız gerekmez.

![bulut ağının güvenliğini sağlama](media/secure-cloud-network/secure-cloud-network.png)

## <a name="prerequisites"></a>Önkoşullar

> [!IMPORTANT]
> Azure Güvenlik Duvarı Yöneticisi önizlemenin `Register-AzProviderFeature` PowerShell komutu kullanılarak açıkça etkinleştirilmesi gerekir.

PowerShell komut isteminden aşağıdaki komutları çalıştırın:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
Özellik kaydının tamamlanabilmesi 30 dakika kadar sürer. Kayıt durumunuzu denetlemek için şu komutu çalıştırın:

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="create-a-hub-and-spoke-architecture"></a>Hub ve bağlı bileşen mimarisi oluşturma

İlk olarak, sunucularınızı yerleştirebileceğiniz bir bağlı ağ VNet oluşturun.

### <a name="create-a-spoke-vnet-and-subnets"></a>Bir bağlı ağ sanal ağı ve alt ağları oluşturma

1. Azure portal giriş sayfasında, **kaynak oluştur**' u seçin.
2. **Ağ**altında **sanal ağ**' ı seçin.
4. **Ad**Için **bağlı bileşen-01**yazın.
5. **Adres alanı** için **10.0.0.0/16** yazın.
6. **Abonelik** bölümünde aboneliğinizi seçin.
7. **Kaynak grubu**için, **Yeni oluştur**' u seçin ve ad için **ILT-Manager** yazın ve **Tamam**' ı seçin.
8. **Konum**Için **(US) Doğu ABD**seçin.
9. **Alt ağ**altında, **ad** türü **iş yükü-sn**için.
10. **Adres aralığı** için **10.0.1.0/24** yazın.
11. Diğer varsayılan ayarları kabul edin ve **Oluştur**' u seçin.

Ardından, bir sıçrama sunucusu için bir alt ağ oluşturun.

1. Azure portal giriş sayfasında, **İlt-Manager** > **kaynak grupları** ' nı seçin.
2. **Bağlı bileşen-01** sanal ağını seçin.
3. **Alt ağları** > alt **ağ**' ı seçin.
4. **Ad**Için, **atsn**yazın.
5. **Adres aralığı** için **10.0.2.0/24** yazın.
6. **Tamam**’ı seçin.

### <a name="create-the-secured-virtual-hub"></a>Güvenli sanal hub 'ı oluşturma

Güvenlik Duvarı Yöneticisi 'Ni kullanarak güvenli sanal hub 'ınızı oluşturun.

1. Azure portal giriş sayfasında, **tüm hizmetler**' i seçin.
2. Arama kutusuna **güvenlik duvarı Yöneticisi** yazın ve **güvenlik duvarı Yöneticisi**' ni seçin.
3. **Güvenlik duvarı Yöneticisi** sayfasında, **güvenli sanal hub oluştur**' u seçin.
4. **Yeni güvenli sanal hub oluştur** sayfasında, aboneliğinizi ve **İlt-Manager** kaynak grubunu seçin.
5. **Güvenli sanal hub adı**Için, **hub-01**yazın.
6. **Konum**için **Doğu ABD**' yi seçin.
7. **Hub adres alanı**için **10.1.0.0/16**yazın.
8. Yeni vWAN adı için **vwan-01**yazın.
9. **Güvenilen güvenlik Iş ortaklarını etkinleştirmek IÇIN VPN ağ geçidini dahil et** onay kutusunu işaretsiz bırakın.
10. **İleri ' yi seçin: Azure Güvenlik Duvarı**.
11. Varsayılan **Azure Güvenlik Duvarı** **etkin** ayarını kabul edin ve ardından **İleri: güvenilen güvenlik ortağı**' nı seçin.
12. Varsayılan **güvenilir güvenlik Iş ortağı** **devre dışı** ayarını kabul edin ve ileri ' yi seçin **: gözden geçir + oluştur**.
13. **Oluştur**'u seçin. Dağıtımı yaklaşık 30 dakika sürer.

### <a name="connect-the-hub-and-spoke-vnets"></a>Hub ve bağlı bileşen VNET 'leri bağlama

Artık hub ve bağlı bileşen VNET 'leri de eşler.

1. **İlt-Manager** kaynak grubunu seçin ve ardından **vwan-01** sanal WAN ' ı seçin.
2. **Bağlantı**altında **sanal ağ bağlantıları**' nı seçin.
3. **Bağlantı ekle**' yi seçin.
4. **Bağlantı adı**için **hub-kol**yazın.
5. Hub **'lar**için **hub-01**' i seçin.
6. **Sanal ağ**Için **bağlı bileşen-01**' i seçin.
7. **Tamam**’ı seçin.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Bir güvenlik duvarı ilkesi oluşturma ve merkezinizi güvenli hale getirme

Bir güvenlik duvarı ilkesi, trafiği bir veya daha fazla güvenli sanal hub üzerinde yönlendirmek için kural koleksiyonlarını tanımlar. Güvenlik Duvarı ilkenizi oluşturup merkezinizi güvenli hale getirin.

1. Güvenlik Duvarı Yöneticisi 'nden **Azure Güvenlik Duvarı Ilkesi oluştur**' u seçin.
2. Aboneliğinizi seçin ve ardından **FW-Manager** kaynak grubunu seçin.
3. **İlke ayrıntıları**' nın altında, **ad** türü **ilkesi-01** ve **bölge** için **Doğu ABD**seçin.
4. **Sonraki: kurallar**' ı seçin.
5. **Kurallar** sekmesinde **kural koleksiyonu Ekle**' yi seçin.
6. **Kural koleksiyonu Ekle** sayfasında, **ad**için **RC-01** yazın.
7. **Kural koleksiyonu türü**için **uygulama**' yı seçin.
8. **Öncelik**için **100**yazın.
9. **Kural toplama eyleminin** **izin ver**olduğundan emin olun.
10. Kural **adı** için **Allow-MSFT**yazın.
11. **Kaynak adresi**için **\*** yazın.
12. **Protokol**için **http, https**yazın.
13. \* * Hedef türünün **FQDN**olduğundan emin olun.
14. **Hedef**için **\*. Microsoft.com**yazın.
15. **Add (Ekle)** seçeneğini belirleyin.
16. **İleri ' yi seçin: güvenli sanal hub 'lar**.
17. **Güvenli sanal hub 'lar** sekmesinde **hub-01**' i seçin.
19. **İncele ve oluştur**’u seçin.
20. **Oluştur**'u seçin.

Bu işlem yaklaşık beş dakika veya daha uzun sürebilir.

## <a name="route-traffic-to-your-hub"></a>Trafiği hub 'ınıza yönlendirin

Artık ağ trafiğinin güvenlik duvarınızdan yönlendirildiğinden emin olmanız gerekir.

1. Güvenlik Duvarı Yöneticisi 'nden **güvenli sanal hub 'lar**' ı seçin.
2. **Hub-01**' i seçin.
3. **Ayarlar**altında **yol ayarları**' nı seçin.
4. **Internet trafiği**, **sanal ağlardan gelen trafik**altında **Azure Güvenlik Duvarı aracılığıyla gönder**' i seçin.
5. **Azure özel trafiği**, **sanal ağlara giden trafik**altında **Azure Güvenlik Duvarı aracılığıyla gönder**' i seçin.
6. **IP adresi ön eklerini Düzenle**' yi seçin.
7. **IP adresi öneki ekle**' yi seçin.
8. Iş yükü alt ağının adresi olarak **10.0.1.0/24** yazın ve **Kaydet**' i seçin.
9. **Ayarlar**altında **Bağlantılar**' ı seçin.
10. **Hub-ışınsal** bağlantısı ' nı seçin ve ardından **güvenli internet trafiği** ' ni seçin ve ardından **Tamam**' ı seçin.


## <a name="test-your-firewall"></a>Güvenlik duvarınızı test etme

Güvenlik Duvarı kurallarınızı test etmek için, birkaç sunucu dağıtmanız gerekir. Güvenlik duvarı kurallarını test etmek için iş yükü-SN alt ağında Iş yükünü-SRV 'yi dağıtırsınız ve ardından Internet 'ten bağlanmak için Uzak Masaüstü 'Nü kullanabilmeniz ve sonra Iş yükü-SRV 'ye bağlanabilmek için.

### <a name="deploy-the-servers"></a>Sunucuları dağıtma

1. Azure portal **kaynak oluştur**' u seçin.
2. **Popüler** listede **Windows Server 2016 Datacenter** ' ı seçin.
3. Sanal makine için şu değerleri girin:

   |Ayar  |Değer  |
   |---------|---------|
   |Kaynak grubu     |**FW-yönetici**|
   |Sanal makine adı     |**Geç-SRV**|
   |Bölge     |**ABD Doğu ABD)**|
   |Yönetici Kullanıcı adı     |**azureuser**|
   |Parola     |**Azure123456!**|

4. **Gelen bağlantı noktası kuralları**altında, **Genel gelen bağlantı noktaları**Için **Seçili bağlantı noktalarına izin ver**' i seçin
5. **Gelen bağlantı noktaları Seç**için **RDP (3389)** seçeneğini belirleyin.

6. Diğer varsayılanları kabul edin ve **İleri ' yi seçin: diskler**.
7. Disk varsayılanlarını kabul edin ve **İleri ' yi seçin: ağ**.
8. Sanal ağ için **bağlı bileşen-01** ' in seçildiğinden ve alt AĞıN **geç sn**olduğundan emin olun.
9. **Genel IP**için, varsayılan yeni genel IP adresi adını (at-SRV-IP) kabul edin.
11. Diğer varsayılanları kabul edin ve **İleri: yönetim**' i seçin.
12. Önyükleme tanılamayı devre dışı bırakmak için **Kapat** ' ı seçin. Diğer varsayılanları kabul edin ve **gözden geçir + oluştur**' u seçin.
13. Özet sayfasında ayarları gözden geçirin ve ardından **Oluştur**' u seçin.

**Iş yükü-SRV**adlı başka bir sanal makineyi yapılandırmak için aşağıdaki tablodaki bilgileri kullanın. Yapılandırmanın geri kalanı Srv-Jump sanal makinesiyle aynıdır.

|Ayar  |Değer  |
|---------|---------|
|Alt ağ|**İş yükü-SN**|
|Genel IP|**Seçim**|
|Ortak gelen bağlantı noktaları|**Seçim**|

### <a name="add-a-route-table-and-default-route"></a>Yol tablosu ve varsayılan yol ekleme

Bir Internet bağlantısı ile bağlantı kurulmasına izin vermek için, bir yol tablosu ve Internet 'e bir varsayılan ağ geçidi yolu **atlayın** .

1. Azure portal **kaynak oluştur**' u seçin.
2. Arama kutusuna **yol tablosu** yazın ve ardından **yol tablosu**' nu seçin.
3. **Oluştur**'u seçin.
4. **Ad**için **RT-01** yazın.
5. Kaynak grubunun ve **(US) bölge Doğu ABD** için aboneliğinizi, **İlt-Manager** ' ı seçin.
6. **Oluştur**'u seçin.
7. Dağıtım tamamlandığında **RT-01** yol tablosunu seçin.
8. **Rotalar** ' ı seçin ve ardından **Ekle**' yi seçin.
9. **Yol adı**için, **atlayın** yazın.
10. **Adres ön eki**için **0.0.0.0/0** yazın.
11. **Sonraki atlama türü**için **Internet** ' i seçin.
12. **Tamam**’ı seçin.
13. Dağıtım tamamlandığında, **alt ağlar**' ı seçin ve ardından **ilişkilendir**' i seçin.
14. **Sanal ağ**Için **bağlı bileşen-01** ' i seçin.
15. **Alt ağ**için **at-sn** ' i seçin.
16. **Tamam**’ı seçin.

### <a name="test-the-rules"></a>Kuralları test etme

Şimdi, beklendiği gibi çalıştığını doğrulamak için güvenlik duvarı kurallarını test edin.

1. Azure portal, **Iş yükü-SRV** sanal makinesi için ağ ayarlarını gözden geçirin ve özel IP adresini aklınızda edin.
2. Uzak bir masaüstünü, **Atsrv** sanal makinesine bağlayın ve oturum açın. Buradan, **Iş yükü-SRV** özel IP adresine bir Uzak Masaüstü bağlantısı açın.

3. Internet Explorer'ı açın ve https://www.microsoft.com adresine gidin.
4. Internet Explorer güvenlik uyarıları ' nı **kapatmak** > **Tamam ' ı** seçin.

   Microsoft giriş sayfasını görmeniz gerekir.

5. https://www.google.com adresine gidin.

   Güvenlik duvarının engellemesi gerekir.

Artık Güvenlik Duvarı kurallarının çalıştığını doğruladınız:

* İzin verilen bir FQDN'ye göz atabilir ancak diğerlerine göz atamazsınız.



## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Güvenilen güvenlik iş ortakları hakkında bilgi edinin](trusted-security-partners.md)
