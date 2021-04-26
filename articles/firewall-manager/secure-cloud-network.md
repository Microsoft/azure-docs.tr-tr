---
title: "Öğretici: Azure Güvenlik Duvarı Yöneticisi 'Ni kullanarak sanal hub 'ınızı güvenli hale getirme"
description: Bu öğreticide, Azure portal kullanarak sanal hub 'ınızın güvenliğini Azure Güvenlik Duvarı Yöneticisi ile nasıl sağlayacağınızı öğreneceksiniz.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 03/19/2021
ms.author: victorh
ms.openlocfilehash: 14ec6fafbbadff2ecc37b229270c269f068a666f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104670469"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-firewall-manager"></a>Öğretici: Azure Güvenlik Duvarı Yöneticisi 'Ni kullanarak sanal hub 'ınızı güvenli hale getirme

Azure Güvenlik Duvarı Yöneticisi 'ni kullanarak, özel IP adreslerine, Azure PaaS 'ye ve Internet 'e yönelik bulut ağ trafiğinizi güvenli hale getirmek için güvenli sanal hub 'lar oluşturabilirsiniz. Güvenlik duvarındaki trafik yönlendirme otomatikleştirilmiştir, bu nedenle Kullanıcı tanımlı yollar (UDRs) oluşturmanız gerekmez.

![bulut ağının güvenliğini sağlama](media/secure-cloud-network/secure-cloud-network.png)

Güvenlik Duvarı Yöneticisi ayrıca bir hub sanal ağ mimarisini destekler. Güvenli sanal hub ve hub sanal ağ mimarisi türleri karşılaştırması için bkz [. Azure Güvenlik Duvarı Yöneticisi mimari seçenekleri nelerdir?](vhubs-and-vnets.md)

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bağlı bileşen sanal ağını oluşturma
> * Güvenli sanal hub oluşturma
> * Hub ve bağlı bileşen sanal ağlarını bağlama
> * Trafiği hub 'ınıza yönlendirin
> * Sunucuları dağıtma
> * Bir güvenlik duvarı ilkesi oluşturma ve merkezinizi güvenli hale getirme
> * Güvenlik duvarını test etme

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="create-a-hub-and-spoke-architecture"></a>Hub ve bağlı bileşen mimarisi oluşturma

İlk olarak, sunucularınızı yerleştirebileceğiniz bağlı olan sanal ağlar oluşturun.

### <a name="create-two-spoke-virtual-networks-and-subnets"></a>İki bağlı bileşen sanal ağı ve alt ağı oluşturma

İki sanal ağ üzerinde her biri bir iş yükü sunucusu olacak ve güvenlik duvarı tarafından korunacaktır.

1. Azure portal giriş sayfasında, **kaynak oluştur**' u seçin.
2. **Sanal ağ** araması yapın ve **Oluştur**' u seçin.
2. **Abonelik** bölümünde aboneliğinizi seçin.
1. **Kaynak grubu** için, **Yeni oluştur**' u seçin ve ad için **FW-Manager-RG** yazın ve **Tamam**' ı seçin.
2. **Ad** Için **bağlı bileşen-01** yazın.
3. **Bölge** Için **(US) Doğu ABD** seçin.
4. **İleri ' yi seçin: IP adresleri**.
1. **Adres alanı** için **10.0.0.0/16** yazın.
3. **Alt ağ adı** altında **varsayılan**' ı seçin.
4. **Alt ağ adı** Için, **iş yükü-01-sn** yazın.
5. **Alt ağ adres aralığı** için **10.0.1.0/24** yazın.
6. **Kaydet**’i seçin.
1. **Gözden geçir ve oluştur**’u seçin.
2. **Oluştur**’u seçin.

Benzer bir sanal ağ oluşturmak için bu yordamı yineleyin:

Ad: **bağlı bileşen-02**<br>
Adres alanı: **10.1.0.0/16**<br>
Alt ağ adı: **Iş yükü-02-sn**<br>
Alt ağ adres aralığı: **10.1.1.0/24**

### <a name="create-the-secured-virtual-hub"></a>Güvenli sanal hub 'ı oluşturma

Güvenlik Duvarı Yöneticisi 'Ni kullanarak güvenli sanal hub 'ınızı oluşturun.

1. Azure portal giriş sayfasında, **tüm hizmetler**' i seçin.
2. Arama kutusuna **güvenlik duvarı Yöneticisi** yazın ve **güvenlik duvarı Yöneticisi**' ni seçin.
3. **Güvenlik duvarı Yöneticisi** sayfasında, **güvenli sanal hub 'ları görüntüle**' yi seçin.
4. **Güvenlik duvarı Yöneticisi 'nde | Güvenli sanal hub 'lar** sayfasında, **yeni güvenli sanal hub oluştur**' u seçin.
5. **Kaynak grubu** için, **İlt-Manager-RG** öğesini seçin.
7. **Bölge** için **Doğu ABD**' yi seçin.
1. **Güvenli sanal hub adı** Için, **hub-01** yazın.
2. **Hub adres alanı** için **10.2.0.0/16** yazın.
3. Yeni vWAN adı için **vwan-01** yazın.
4. **Güvenilen güvenlik Iş ortaklarını etkinleştirmek IÇIN VPN ağ geçidini dahil et** onay kutusunu işaretsiz bırakın.
5. **İleri ' yi seçin: Azure Güvenlik Duvarı**.
6. Varsayılan **Azure Güvenlik Duvarı** **etkin** ayarını kabul edin ve ardından **İleri: güvenilen güvenlik ortağı**' nı seçin.
7. Varsayılan **güvenilir güvenlik Iş ortağı** **devre dışı** ayarını kabul edin ve ileri ' yi seçin **: gözden geçir + oluştur**.
8. **Oluştur**’u seçin. 

   Dağıtımı yaklaşık 30 dakika sürer.

Dağıtım tamamlandıktan sonra güvenlik duvarı genel IP adresini alabilirsiniz.

1. **Güvenlik duvarı Yöneticisi 'ni** açın.
2. **Sanal hub 'ları** seçin.
3. **Hub-01**' i seçin.
7. **Genel IP yapılandırması**' nı seçin.
8. Daha sonra kullanılacak genel IP adresini aklınızda edin.

### <a name="connect-the-hub-and-spoke-virtual-networks"></a>Hub ve bağlı bileşen sanal ağlarını bağlama

Artık hub ve bağlı bileşen sanal ağlarını eşleyebilir.

1. **FW-Manager-RG** kaynak grubunu seçin ve ardından **vwan-01** sanal WAN ' ı seçin.
2. **Bağlantı** altında **sanal ağ bağlantıları**' nı seçin.
3. **Bağlantı ekle**' yi seçin.
4. **Bağlantı adı** için **hub-ışınsal-01** yazın.
5. Hub **'lar** için **hub-01**' i seçin.
6. **Kaynak grubu** için, **İlt-Manager-RG** öğesini seçin.
7. **Sanal ağ** Için **bağlı bileşen-01**' i seçin.
8. **Oluştur**’u seçin.

**Bağlı bileşen-02** sanal ağını bağlamak için tekrarlayın: bağlantı adı- **Merkez-bağlı-02**

## <a name="deploy-the-servers"></a>Sunucuları dağıtma

1. Azure portal **kaynak oluştur**' u seçin.
2. **Popüler** listede **Windows Server 2016 Datacenter** ' ı seçin.
3. Sanal makine için şu değerleri girin:

   |Ayar  |Değer  |
   |---------|---------|
   |Kaynak grubu     |**İlt-Manager-RG**|
   |Sanal makine adı     |**SRV-iş yükü-01**|
   |Region     |**ABD Doğu ABD)**|
   |Yönetici Kullanıcı adı     |Kullanıcı adı yazın|
   |Parola     |parola yazın|

4. **Gelen bağlantı noktası kuralları** altında, **Genel gelen bağlantı noktaları** için **hiçbiri**' ni seçin.
6. Diğer varsayılanları kabul edin ve **İleri ' yi seçin: diskler**.
7. Disk varsayılanlarını kabul edin ve **İleri ' yi seçin: ağ**.
8. Sanal ağ için **bağlı bileşen-01** ' i seçin ve alt ağ Için **iş yükü-01-sn** öğesini seçin.
9. **Genel IP** için **hiçbiri**' ni seçin.
11. Diğer varsayılanları kabul edin ve **İleri: yönetim**' i seçin.
12. Önyükleme tanılamayı devre dışı bırakmak için **devre dışı bırak** seçeneğini belirleyin Diğer varsayılanları kabul edin ve **gözden geçir + oluştur**' u seçin.
13. Özet sayfasında ayarları gözden geçirin ve ardından **Oluştur**' u seçin.

**SRV-Workload-02** adlı başka bir sanal makineyi yapılandırmak için aşağıdaki tablodaki bilgileri kullanın. Yapılandırmanın geri kalanı, **SRV-Workload-01** sanal makinesiyle aynıdır.

|Ayar  |Değer  |
|---------|---------|
|Sanal ağ|**Bağlı bileşen-02**|
|Alt ağ|**İş yükü-02-SN**|

Sunucular dağıtıldıktan sonra bir sunucu kaynağı seçin ve **ağ** içinde her bir sunucunun özel IP adresini göz önünde bulunur.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Bir güvenlik duvarı ilkesi oluşturma ve merkezinizi güvenli hale getirme

Bir güvenlik duvarı ilkesi, trafiği bir veya daha fazla güvenli sanal hub üzerinde yönlendirmek için kural koleksiyonlarını tanımlar. Güvenlik Duvarı ilkenizi oluşturup merkezinizi güvenli hale getirin.

1. Güvenlik Duvarı Yöneticisi 'nden **Azure Güvenlik Duvarı Ilkelerini görüntüle**' yi seçin.
2. **Azure Güvenlik Duvarı Ilkesi oluştur**' u seçin.
1. **Kaynak grubu** için, **İlt-Manager-RG** öğesini seçin.
1. **İlke ayrıntıları**' nın altında, **ad** türü **ilkesi-01** ve **bölge** için **Doğu ABD** seçin.
1. **İleri ' yi seçin: DNS ayarları**.
1. **İleri ' yi seçin: TLS incelemesi (Önizleme)**.
1. **Sonraki: kurallar**' ı seçin.
1. **Kurallar** sekmesinde **kural koleksiyonu Ekle**' yi seçin.
1. **Kural koleksiyonu Ekle** sayfasında, **ad** için **App-RC-01** yazın.
1. **Kural koleksiyonu türü** için **uygulama**' yı seçin.
1. **Öncelik** için **100** yazın.
1. **Kural toplama eyleminin** **izin ver** olduğundan emin olun.
1. Kural **adı** için **Allow-MSFT** yazın.
1. **Kaynak türü** için **IP adresi**' ni seçin.
1. **Kaynak** için yazın **\*** .
1. **Protokol** için **http, https** yazın.
1. **Hedef türün** **FQDN** olduğundan emin olun.
1. **Hedef** için **\* . Microsoft.com** yazın.
1. **Add (Ekle)** seçeneğini belirleyin.

Bir uzak masaüstü 'nü **SRV-Workload-01** sanal makinesine bağlayabilmeniz IÇIN BIR DNAT kuralı ekleyin.

1. **Ekle/kural koleksiyonu**' nu seçin.
1. **Ad** için **DNAT-RDP** yazın.
1. **Kural koleksiyonu türü** Için **DNAT**' yi seçin.
1. **Öncelik** için **100** yazın.
1. Kural **adı** Için **izin ver-RDP** yazın.
1. **Kaynak türü** için **IP adresi**' ni seçin.
1. **Kaynak** için yazın **\*** .
1. **Protokol** alanında **TCP**'yi seçin.
1. **Hedef bağlantı noktaları** için **3389** yazın.
1. **Hedef türü** Için **IP adresi**' ni seçin.
1. **Hedef** için, daha önce not ettiğiniz güvenlik DUVARı genel IP adresini yazın.
1. **Çevrilen adres** için, daha önce not ettiğiniz **SRV-Workload-01** özel IP adresini yazın.
1. **Çevrilmiş bağlantı noktası** için **3389** yazın.
1. **Add (Ekle)** seçeneğini belirleyin.

Bir ağ kuralı ekleyerek, **SRV-Iş yükü-01** ' den **SRV-iş yüküne-02**' e bir uzak masaüstü bağlayabilirsiniz.

1. **Kural koleksiyonu Ekle**' yi seçin.
2. **Ad** için **VNET-RDP** yazın.
3. **Kural koleksiyonu türü** için **ağ**' ı seçin.
4. **Öncelik** için **100** yazın.
1. **Kural toplama eylemi** Için **izin ver**' i seçin.
1. Kural **adı** Için **izin ver-VNET**' i girin.
1. **Kaynak türü** için **IP adresi**' ni seçin.
1. **Kaynak** için yazın **\*** .
1. **Protokol** alanında **TCP**'yi seçin.
1. **Hedef bağlantı noktaları** için **3389** yazın.
1. **Hedef türü** Için **IP adresi**' ni seçin.
1. **Hedef** için, daha önce not ettiğiniz **SRV-Workload-02** özel IP adresini yazın.
1. **Add (Ekle)** seçeneğini belirleyin.
1. **Gözden geçir ve oluştur**’u seçin.
1. **Oluştur**’u seçin.

## <a name="associate-policy"></a>İlkeyi ilişkilendir

Güvenlik Duvarı ilkesini hub ile ilişkilendirin.

1. Güvenlik Duvarı Yöneticisi 'nden **Azure Güvenlik Duvarı ilkeleri**' ni seçin.
1. **İlke-01** onay kutusunu seçin.
1. **Ilişkileri Yönet/hub 'Ları ilişkilendir**' i seçin.
1. **Hub-01**' i seçin.
1. **Add (Ekle)** seçeneğini belirleyin.

## <a name="route-traffic-to-your-hub"></a>Trafiği hub 'ınıza yönlendirin

Artık ağ trafiğinin güvenlik duvarınız üzerinden yönlendirildiğinden emin olmanız gerekir.

1. Güvenlik Duvarı Yöneticisi 'nde **sanal hub 'lar**' ı seçin.
2. **Hub-01**' i seçin.
3. **Ayarlar** altında **Güvenlik Yapılandırması**' nı seçin.
4. **Internet trafiği** altında **Azure Güvenlik Duvarı**' nı seçin.
5. **Özel trafik** altında **Azure Güvenlik Duvarı aracılığıyla gönder**' i seçin.
1. **Kaydet**’i seçin.

   Yol tablolarının güncelleştirilmesi birkaç dakika sürer.
1. İki bağlantının Azure Güvenlik Duvarı 'Nın hem Internet hem de özel trafiğin güvenlik altına aldığını doğrulayın.

## <a name="test-the-firewall"></a>Güvenlik duvarını test etme

Güvenlik duvarı kurallarını test etmek için, **SRV-Iş yükü-01**' de bulunan güvenlik DUVARı genel IP adresini kullanarak bir uzak masaüstü bağlayacaksınız. Buradan, uygulama kuralını test etmek ve ağ kuralını sınamak için bir uzak masaüstünü **SRV-Workload-02** ' ye bağlamak için bir tarayıcı kullanacaksınız.

### <a name="test-the-application-rule"></a>Uygulama kuralını test etme

Şimdi, beklendiği gibi çalıştığını doğrulamak için güvenlik duvarı kurallarını test edin.

1. Uzak bir masaüstünü güvenlik duvarı genel IP adresine bağlayın ve oturum açın.

3. Internet Explorer'ı açın ve `https://www.microsoft.com` adresine gidin.
4.   >  Internet Explorer güvenlik uyarıları 'nda Tamam **Kapat** ' ı seçin.

   Microsoft giriş sayfasını görmeniz gerekir.

5. `https://www.google.com` adresine gidin.

   Güvenlik duvarının engellemesi gerekir.

Artık güvenlik duvarı uygulama kuralının çalıştığını doğruladınız:

* İzin verilen bir FQDN'ye göz atabilir ancak diğerlerine göz atamazsınız.

### <a name="test-the-network-rule"></a>Ağ kuralını test etme

Şimdi ağ kuralını test edin.

- SRV-iş yükü-01 ' den, SRV-Iş yükü-02 özel IP adresine bir Uzak Masaüstü açın.

   Uzak Masaüstü, SRV-Workload-02 ' e bağlanmalıdır.

Artık güvenlik duvarı ağ kuralının çalıştığını doğruladınız:
* Uzak bir masaüstünü, başka bir sanal ağda bulunan bir sunucuya bağlayabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Güvenlik Duvarı kaynaklarınızı test etmeyi tamamladığınızda, güvenlik duvarı ile ilgili tüm kaynakları silmek için **FW-Manager-RG** kaynak grubunu silin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Güvenilen güvenlik iş ortakları hakkında bilgi edinin](trusted-security-partners.md)
